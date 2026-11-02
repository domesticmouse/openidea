# Deep-Dive Data Flows in OpenIDEA

## 1. Document Synchronization (`DocSync` & `Documents`)

OpenIDEA maintains two copies of an open text file:
1. **Rust Backend (`crates/ide-workspace/src/documents.rs`)**: Stored as a `ropey::Rope` with a monotonically increasing `version: u32`.
2. **Frontend Editor (`src/editor/core/model/document.ts` & `src/editor/core/sync/DocSync.ts`)**: Stored as a CodeMirror `EditorState` shared across split panes showing that file.

### Invariant
$$\text{localText} = \text{confirmed} \circ \text{inflight} \circ \text{buffer}$$
- `confirmed`: Text at `serverVersion` (acknowledged by the backend).
- `inflight`: At most **one** `ChangeSet` currently sent via `commands.docEdit(doc, baseVersion, changes)`.
- `buffer`: Subsequent user edits composed locally while `inflight` is awaiting a reply, or while waiting for the next `requestAnimationFrame` flush.

### Conflict Resolution (Rebasing without Losing Keystrokes)
When the backend modifies a document (e.g., disk reload, `apply_workspace_edit`, formatting), it emits `IdeEvent::DocPatched { doc, baseVersion, version, changes }`:
1. If `patch.baseVersion === this.serverVersion` while an edit is `inflight`, the backend applied `patch` before our `inflight` edit arrived.
2. `DocSync` immediately transforms `inflight` and `buffer` over `patch` using CodeMirror's `ChangeSet.map(patch, true)`, updates `confirmed` and `serverVersion` to `patch.version`, and marks `inflightStale = true`.
3. When the in-flight `doc_edit` inevitably rejects with `IdeError { kind: "outOfSync" }`, `DocSync` sees `inflightStale === true`, folds any new `buffer` into the already-rebased `inflight`, and resends it with the new `serverVersion`.
4. If `outOfSync` arrives *before* the `DocPatched` event (due to IPC ordering), `DocSync` waits `RESYNC_GRACE_MS` (60ms) for the event; if it still hasn't arrived, it calls `doc_snapshot`, computes the minimal diff against `confirmed`, rebases `inflight` and `buffer`, and resends.

---

## 2. Three IPC Channels (Frontend $\leftrightarrow$ Backend)

| Direction | Mechanism | When to Use | Key Files |
| :--- | :--- | :--- | :--- |
| **UI $\rightarrow$ Backend (Request/Reply)** | `await call(commands.<name>(...))` | Discrete queries and mutations. | `src/platform/ipc.ts`, `src-tauri/src/commands/*.rs` |
| **Backend $\rightarrow$ UI (Broadcast / Window Event)** | `sink.emit(IdeEvent::...)` $\rightarrow$ `onIdeEvent("<type>", fn)` | State change notifications (`fsChanged`, `docPatched`, `diagnostics`, `vcsChanged`, `task`, `indexState`). | `crates/ide-core/src/event.rs`, `src-tauri/src/events.rs`, `src/platform/ipc.ts` |
| **Backend $\rightarrow$ UI (High-Volume Stream)** | `new Channel<T>()` passed as command arg $\rightarrow$ `ide_host::ops::Channel<T>` | Terminal PTY output (`term_spawn`), process stdout (`run_start`), search hits (`search_start`), install progress. | `crates/ide-host/src/ops/mod.rs`, `src-tauri/src/commands/mod.rs` |

### Cancellable Requests
For expensive operations (LSP queries, search, directory comparison):
1. Frontend allocates an ID via `const req = nextRequestId()` (`src/platform/ipc.ts`).
2. Passes `req` to the command.
3. Backend registers a `CancellationToken` via `host.begin_request(req)` (or `host.begin_project_request(window, req)` which auto-cancels if the project becomes untrusted) and cleans up with `host.end_request(req)`.
4. If the user types or closes a popup, the frontend calls `void commands.cancelRequest(req)`. Even if `cancel_request` arrives before `begin_request`, `Requests` remembers the cancelled ID so `begin_request` returns an already-cancelled token.

---

## 3. Transparent Remote Development (`openidea-server`)

When a window connects to a remote SSH host (`connectRemote` in `src/platform/remote.ts`):
1. **Server Bootstrap (`src-tauri/src/remote_dev.rs`)**:
   - Connects over SSH using `ide_ssh::SshManager`.
   - Probes `uname -sm` on the remote host and checks `~/.openidea/server/<version>/<blake3_hash>/openidea-server`.
   - If missing, uploads the matching `openidea-server` binary over SFTP and spawns `openidea-server --stdio`.
2. **Protocol Handshake (`crates/ide-host/src/rpc.rs`)**:
   - Speaks JSON-RPC 2.0 (`Content-Length` framed) over SSH stdio.
   - Calls `initialize` (verifying `PROTOCOL_VERSION`) and `commands` (retrieving the list of commands registered in `Dispatcher::standard()`).
3. **Frontend Transparent Interception (`src/platform/tauriCore.ts` & `src/platform/remote.ts`)**:
   - `vite.config.ts` aliases `@tauri-apps/api/core` to `src/platform/tauriCore.ts`.
   - `enableRemote(session)` installs a custom `InvokeRoute` in `tauriCore.ts`.
   - When any feature calls `commands.foo(arg1, channelArg)`:
     - `remoteRouting()` checks if `foo` is served remotely, locally (e.g. `system_font_families`, client UI settings), or both (global tooling settings).
     - For remote commands, `encodeArgs()` replaces any `Channel` instance with `{ "$channel": id }`.
     - Invokes local Tauri command `remote_invoke(cmd, argsJson, onChannel)`.
     - `src-tauri/src/remote_dev.rs` forwards the `invoke` request over JSON-RPC to `openidea-server`.
     - Streamed `channel` notifications and `event` (`IdeEvent`) notifications from `openidea-server` are received by `ClientHandler` in `remote_dev.rs` and re-emitted into the local webview, making remote execution completely transparent to feature code.
