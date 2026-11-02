---
name: openidea-backend-ipc
description: >-
  Step-by-step workflow for adding or modifying Rust backend operations, Tauri
  IPC commands, remote server RPC handlers, Project Trust classifications,
  IdeEvent variants, TypeScript bindings, and directory README.md contracts in
  OpenIDEA. Use this skill whenever adding a new backend command, changing IPC
  types, or exposing crate functionality to the frontend.
---

# Adding & Modifying Backend IPC Commands in OpenIDEA

In OpenIDEA, `src-tauri` is a thin desktop shell. All backend logic lives in `crates/ide-*` and is orchestrated by `crates/ide-host`. Most commands work both locally (via Tauri IPC) and remotely (via `openidea-server` JSON-RPC).

Before modifying any crate or `ops` directory, read its local `README.md` contract (e.g., `crates/ide-host/src/ops/README.md` or `crates/ide-<domain>/src/README.md`).

## Step-by-Step Workflow

### 1. Implement Domain Logic in `crates/ide-<domain>`
- Put core data structures, parsers, and services inside the appropriate domain crate (`ide-vcs`, `ide-lsp`, `ide-search`, `ide-workspace`, etc.).
- Derive `serde::Serialize`, `serde::Deserialize`, and `specta::Type` on any struct or enum that crosses the IPC boundary:
  ```rust
  use serde::{Deserialize, Serialize};
  use specta::Type;

  #[derive(Clone, Debug, Serialize, Deserialize, Type)]
  #[serde(rename_all = "camelCase")]
  pub struct MyResponse {
      pub path: ide_core::IpcPath,
      pub count: u32,
  }
  ```
- **Note**: `src-tauri/src/lib.rs` configures `tauri-specta` with `.dangerously_cast_bigints_to_number()`, and paths crossing IPC should use `ide_core::IpcPath` or `PathBuf` as appropriate for the domain.

### 2. Add the Shared Host Operation in `crates/ide-host/src/ops/`
- Implement the operation in `crates/ide-host/src/ops/<domain>.rs` (or the appropriate multi-file submodule under `crates/ide-host/src/ops/{debug,doc,java,run,search,term,vcs}/`), taking `&Host`, `window: &str`, and the command arguments, returning `IdeResult<T>`.
- Resolve the window's open project with `let project = host.project(window)?;`.
- Offload blocking disk I/O or CPU-heavy work to the Tokio blocking pool using `host.blocking(move || { ... }).await`.
- For streaming output, accept `ide_host::ops::Channel<T>` (or `ide_host::ops::Emit<T>`).
- For cancellable operations, accept `request: RequestId`, obtain a token with `let cancel = host.begin_project_request(window, request);` (or `host.begin_request(request)`), and always clean up with `host.end_request(request);`.

### 3. Register for Remote Development (`crates/ide-host/src/ops/` & `rpc.rs`)
- If the command should work when connected to a remote `openidea-server` host, register it in the module's `pub fn register(d: &mut crate::rpc::Dispatcher)` function (wired into `Dispatcher::standard()` in [`crates/ide-host/src/rpc.rs`](../../../crates/ide-host/src/rpc.rs)):
  ```rust
  pub fn register(d: &mut crate::rpc::Dispatcher) {
      d.add("my_command_name", |c| async move {
          my_command(&c.host, &c.window, c.arg("firstArg")?, c.sender("onProgress")?).await
      });
  }
  ```
- Ensure argument names in `c.arg("firstArg")` match the **camelCase** parameter names that Tauri generates in TypeScript!

### 4. Expose the Tauri Command in `src-tauri/src/commands/<domain>.rs`
- Write a thin wrapper annotated with `#[tauri::command]` and `#[specta::specta]`:
  ```rust
  #[tauri::command]
  #[specta::specta]
  pub async fn my_command_name(
      window: Window,
      state: State<'_, AppState>,
      first_arg: String,
      on_progress: tauri::ipc::Channel<ProgressItem>,
  ) -> IdeResult<MyResponse> {
      ide_host::ops::my_domain::my_command(
          &state.host,
          window.label(),
          first_arg,
          crate::commands::sender(on_progress),
      )
      .await
  }
  ```
- Register the command in `collect_commands![...]` inside [`src-tauri/src/commands/mod.rs`](../../../src-tauri/src/commands/mod.rs).

### 5. Classify Project Trust (`crates/ide-host/src/trust.rs`)
- **Security Gate**: Every command goes through `Host::authorize_command` (locally in `src-tauri/src/lib.rs` and remotely in `crates/ide-host/src/rpc.rs`).
- By default, **new commands are denied in Restricted Mode** (untrusted projects) unless listed in `restricted_command_allowed(cmd: &str)` in [`crates/ide-host/src/trust.rs`](../../../crates/ide-host/src/trust.rs).
- **Rule**:
  - If the command **executes project code, spawns project-configured binaries, or mutates external systems**, leave it out of `restricted_command_allowed` (so it requires a trusted project).
  - If the command **only reads/edits source files or manages user-owned IDE state**, add it (or ensure its prefix like `fs_`, `doc_`, `search_`, `nav_` matches) in `restricted_command_allowed`.

### 6. Adding a Backend Event (`IdeEvent`)
If your feature pushes asynchronous notifications to the UI:
1. Add a variant to `enum IdeEvent` in [`crates/ide-core/src/event.rs`](../../../crates/ide-core/src/event.rs).
2. Emit it via `project.sink.emit(IdeEvent::MyVariant { ... })` (window-scoped) or `state.sink.emit(...)` (broadcast to all windows).
3. Subscribe on the frontend using `onIdeEvent("myVariant", (e) => { ... })` from `@/platform/ipc`.

### 7. Regenerate TypeScript Bindings, Update Contracts & Verify
1. Run `pnpm gen:bindings` (which runs `cargo test -p openidea --test export_bindings`) to update `src/bindings.ts`.
2. Call the command from TypeScript using `await call(commands.myCommandName(...))` from `@/platform/ipc`.
3. Update the `## Interface`, `## Invariants`, and `## Side Effects` sections in the affected `README.md` contracts (`crates/ide-<domain>/src/README.md`, `crates/ide-host/src/ops/README.md`, `src-tauri/src/commands/README.md`).
4. Verify compilation and tests:
   - `cargo test -p <crate>` / `cargo check --workspace`
   - `pnpm typecheck`
