---
name: openidea-architecture
description: >-
  Architectural guide, crate map, feature map, hierarchical README.md contract
  index, and end-to-end data flow reference for the OpenIDEA codebase. Activate
  this skill when exploring the repository, planning cross-cutting changes,
  debugging document synchronization, working with remote development
  (openidea-server), or understanding how the Rust backend (crates/*, src-tauri)
  and React/TypeScript frontend (src/*) interact.
---

# OpenIDEA Architecture & Data Flow

OpenIDEA is a native IDE inspired by IntelliJ IDEA, built with a **Rust backend** (34 workspace crates + a thin Tauri v2 desktop shell) and a **React 19 + TypeScript + CodeMirror 6 + Zustand** frontend.

## Hierarchical `README.md` Contract System (224 Directories)

Every code-bearing and intermediate directory across the repository (`crates/*`, `src-tauri/*`, `src/*`, `plugins/*`, `perf/*`, `scripts/*`, `public/`) contains an up-to-date `README.md` architectural contract:
- **Intermediate Blueprints** (`crates/README.md`, `crates/<crate>/README.md`, etc.) summarize subsystem boundaries and link to child contracts.
- **Component Packages (Peanuts)** (`crates/<crate>/src/README.md`, `src/features/<feature>/README.md`, etc.) document:
  - `## Overview`: Single-sentence responsibility.
  - `## Interface`: Exported symbols (`[file:symbol]`) and child subsystem links (`./<child>/README.md`).
  - `## Invariants`: Hard state, concurrency, and ordering guarantees.
  - `## Side Effects`: I/O, IPC, DOM, storage, and process mutations.
  - `## Verification`: Exact test command (`cargo test -p <crate>`, `pnpm test`, etc.).
- **Rule when exploring or modifying a directory**: Read the target directory's `README.md` first before scanning every source file, and update its `Interface`, `Invariants`, and `Side Effects` sections whenever your code changes alter exported symbols or behavioral guarantees.

---

## Architectural Principles

1. **Headless Backend (`ide-host`) vs. Desktop Glue (`src-tauri`)**:
   - Domain logic never lives in `src-tauri/`. `src-tauri` is strictly a windowing/IPC adapter wrapping `Arc<ide_host::Host>`.
   - `ide-host` is UI-toolkit agnostic: it powers both the local Tauri desktop app and the headless `openidea-server --stdio` binary (`crates/ide-host/src/bin/server.rs`) used for SSH Remote Development.
   - Domain crates (`ide-*`) never depend on Tauri. They emit events via the `ide_core::EventSink` trait.
2. **Contribution-Driven Frontend (`src/workbench` vs. `src/features`)**:
   - `src/workbench/` and `src/editor/core/` never import feature modules (`src/features/*`).
   - Instead, features register UI and editor extensions during startup (`activateFeatures()` in `src/features/index.ts`) through registries in `src/workbench/contributions.ts`, `src/editor/api.ts`, and `src/platform/actions.ts`.
3. **Single Authoritative Document Model (`Documents` + `DocSync`)**:
   - Open files are stored in Rust as `ropey` ropes in `ide_workspace::Documents` and synchronized with CodeMirror 6 in the frontend via single-server OT (`src/editor/core/sync/DocSync.ts`).

---

## Codebase Map

### Backend (`crates/` & `src-tauri/`)

- **Core Primitives**:
  - `crates/ide-core`: Shared IDs (`DocId`, `RequestId`, `ServerId`, `TaskId`, `RunId`, `TerminalId`, `DebugSessionId`), settings schemas (`settings.rs`), `IdeError`, `CancellationToken`, and the backend-to-UI event bus (`IdeEvent`, `EventSink`).
  - `crates/ide-text` & `crates/ide-vfs`: Rope buffers (`TextBuffer`), UTF-8/UTF-16/line-col offset conversions, atomic file writes (`write_atomic`), charset detection, trash deletion, and debounced filesystem watching (`Watcher`).
  - `crates/ide-jsonrpc`: Content-Length framed JSON-RPC 2.0 `Peer` over async `Read`/`Write` streams (used by LSP, DAP, ACP, and `openidea-server`).
  - `crates/ide-language` & `crates/ide-syntax`: Loads the 60+ `languages/*.toml` definitions (`LanguageRegistry`) and manages incremental Tree-sitter parsing (`SyntaxCache`), syntax highlighting, folding, structure outlines (`StructureService`), local variable scopes, and structural search/replace.
- **Workspace, Indexing & Code Insight**:
  - `crates/ide-workspace`: Open document table (`Documents`), `DocumentObserver` trait, `LocalHistory`, `SettingsStore` (global + `.idea` project settings), multi-project `Workspace` manifests, and source/test/excluded root detection (`roots.rs`).
  - `crates/ide-index` & `crates/ide-search`: Persistent trigram/symbol index (`redb`), fuzzy matcher (`nucleo-matcher`), and ripgrep-backed (`grep-searcher`) Find/Replace in Files.
  - `crates/ide-lsp` & `crates/ide-analysis`: Language Server Protocol lifecycle (`LspManager`), multi-server routing, Eclipse JDTLS (`src/jdtls/`), TypeScript/Angular bridges, virtual documents, static inspections, and duplicate code detection.
- **Execution & Tooling Crates**:
  - `crates/ide-run`, `crates/ide-dap`, `crates/ide-build`, `crates/ide-coverage`, `crates/ide-profiler`, `crates/ide-sdk`, `crates/ide-tools`: Process execution & run config auto-detection (`src/detect/`), Debug Adapter Protocol (`DebugManager`), Maven/Gradle/Cargo models & project-wide compilation (`src/compile/`), code coverage, CPU flamegraphs, SDK detection/JDK download, and managed tool downloads.
  - `crates/ide-vcs`, `crates/ide-github`, `crates/ide-terminal`, `crates/ide-db`, `crates/ide-http`, `crates/ide-notebook`, `crates/ide-agent`, `crates/ide-plugin`, `crates/ide-ssh`, `crates/ide-remote`, `crates/ide-docker`, `crates/ide-web`, `crates/ide-spell`, `crates/ide-clipboard`, `crates/ide-fonts`: Feature-specific domain crates.
- **Orchestrator & Shell**:
  - `crates/ide-host`: Defines `Host` (`lib.rs`), per-window `Project` (`project.rs`), shared command implementations (`ops/*.rs` and submodules `ops/{debug,doc,java,run,search,term,vcs}/`), project trust (`trust.rs`), and the remote JSON-RPC server (`rpc.rs`, `bin/server.rs`).
  - `src-tauri`: Defines `AppState` (`state.rs`), Tauri `#[tauri::command]` wrappers (`commands/*.rs`), window/menu management, and the client side of remote development (`remote_dev.rs`).

> For the complete 34-crate dependency hierarchy and local `README.md` contract links, see [references/crate-map.md](./references/crate-map.md).

### Frontend (`src/`)

- `src/bindings.ts`: Auto-generated by `tauri-specta` (`pnpm gen:bindings`). **Never edit manually.**
- `src/platform/`:
  - `ipc.ts`: `call(commands.foo(...))` wrapper, `onIdeEvent()` subscriber, and `nextRequestId()`.
  - `actions.ts`, `context.ts`, `keymap.ts`, `keymaps/*`: IntelliJ-style `ActionManager` (`registerAction`, `executeAction`), `when` / `keyWhen` context expression evaluation, and preset keymaps (`intellij-macos`, `intellij-windows`, `vscode-macos`, `vscode-windows`, `eclipse-macos`, `eclipse-windows`).
  - `project.ts`, `projectRoots.ts`, `settings.ts`, `workflowSettings.ts`, `editorSettings.ts`: Reactive stores for project state, roots, and validated settings.
  - `tauriCore.ts` & `remote.ts`: Vite aliases `@tauri-apps/api/core` to `tauriCore.ts` so `invoke` transparently routes to `openidea-server` in remote windows.
- `src/ui/`: Design system primitives (`Tree`, `List`, `Dialog`, `Popup`, `Menu`, `Tabs`, `Splitter`, `Input`, `Select`, and `expui`/`openidea` icons).
- `src/workbench/`: The "Islands" shell (`Workbench.tsx`, `TitleBar.tsx`, `ToolWindows.tsx`, `EditorArea.tsx`, `StatusBar.tsx`, `NavigationBar.tsx`, `Notifications.tsx`) and extension registries (`contributions.ts`).
- `src/editor/`:
  - `api.ts`: Public contract for features to extend CodeMirror (`registerEditorContribution`, `scrollbarMarks`, `gutterLaneMarkers`, `codeLenses`, `intentionProviders`, `quickFixProviders`, `registerSaveParticipant`, `whenSynced`).
  - `core/`: `EditorController` (`controller.ts`), `DocumentManager` (`model/document.ts`), `DocSync` (`sync/DocSync.ts`), and base CodeMirror setup (`cm/`, including `cm/angular/`).
  - `lsp/`, `navigation/`, `structure/`, `refactor/`: Editor intelligence layers.
- `src/features/*`: 38 independent feature packages activated by `src/features/index.ts`.

> For the complete map of frontend layers and all 38 `src/features/*` packages, see [references/feature-map.md](./references/feature-map.md).

---

## Core Data Flows

### 1. Boot & Window Lifecycle (`src/main.tsx` & `src-tauri/src/lib.rs`)
1. **Rust Setup**: `src-tauri/src/lib.rs` initializes logging, inherits the login shell PATH, spawns a background thread to warm up Tree-sitter grammars (`Syntax::new()`), creates `AppState` (which constructs `ide_host::Host`), and opens the main window.
2. **Webview Entry (`src/main.tsx`)**:
   - Checks `settingsWindowParent()`: if the window label is `settings-<parent>`, it calls `activateSecondaryWindow()`, `activateSettingsWindowFeatures(parent)`, and renders `<SettingsWindow />`.
   - Otherwise, it calls `activateWorkbench()`, `activateFeatures()` (strictly before `installKeyboardDispatcher()` so capture-phase feature listeners such as the terminal shortcut interceptor run first), `installKeyboardDispatcher()`, renders `<Workbench />`, calls `hideSplash()`, and prefetches lazy UI components.

### 2. Project Open & File Watching (`crates/ide-host/src/project.rs`)
When `Host::open_project(window, root)` runs:
1. Canonicalizes `root`, verifies `.openidea/workspace.toml` (if any), and checks `TrustStore::trusted(&root)`.
2. Loads `SettingsStore` (merging global settings with `<root>/.idea` settings) and auto-detects source/test/excluded roots (`ide_workspace::roots::detect`).
3. Opens `LocalHistory` and `ProjectIndex` (`redb` in `local_data_dir/index/<fnv1a_key>`).
4. Configures `LspManager` hooks and registers `DocumentObserver`s on `Documents`:
   - `SyntaxObserver` -> updates incremental Tree-sitter trees in `SyntaxCache`.
   - `LspObserver` -> sends `didOpen`, `didChange`, `didSave`, `didClose` to running language servers.
   - `HistoryObserver` -> records `"Saved"` snapshots in `LocalHistory`.
   - `DeployObserver` -> uploads saved files to SFTP if auto-deploy is enabled and the project is trusted.
   - `KotlinBridge` -> syncs Kotlin/Gradle classpaths.
5. Starts the debounced `ide_vfs::Watcher`. Disk changes update `ProjectIndex`, notify `LspManager` and `KotlinBridge`, reload clean open `Documents` (`on_disk_changed`), record external changes in `LocalHistory`, re-detect project/VCS roots if build files or `.git/HEAD` changed, and emit `IdeEvent::FsChanged` / `IdeEvent::VcsChanged`.

### 3. Document Synchronization (`DocSync` <-> `Documents`)
See [references/data-flows.md](./references/data-flows.md) for the complete state machine.
- **Local typing**: CodeMirror transactions feed into `DocSync.local(changes)` (`src/editor/core/sync/DocSync.ts`). Edits are buffered per animation frame and sent via `commands.docEdit(doc, baseVersion, changes)` (at most one request in flight per document).
- **Backend patches**: When Rust modifies a buffer (disk reload, `apply_workspace_edit`, formatting), `Documents` bumps the version and emits `IdeEvent::DocPatched { doc, baseVersion, version, changes }`. `DocSync.remote(patch)` transforms any in-flight/buffered local edits against the patch and dispatches the transformed delta into CodeMirror with the `remoteChange` annotation.
- **Position-dependent LSP calls**: Because LSP servers read the Rust backend's copy of the document, any frontend feature making an edit-sensitive LSP call (format, rename, code actions, surround with) **must** `await whenSynced(view)` (from `@/editor/api`) before invoking the backend command.

### 4. Validation & Verification Commands
- **Typecheck (TS)**: `pnpm typecheck`
- **Frontend Unit Tests**: `pnpm test` (Vitest)
- **End-to-End Browser Tests**: `pnpm e2e` (Playwright)
- **Dead Code / Unused Exports**: `pnpm knip`
- **Rust Tests**: `cargo test --workspace` (or `cargo test -p <crate>`)
- **Regenerate IPC Bindings**: `pnpm gen:bindings`
- **Lint & Format**: `pnpm lint` (`oxlint`) and `pnpm format:check` (`oxfmt`)
