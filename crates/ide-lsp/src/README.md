# src

## Overview
LSP client lifecycle and multi-server orchestration (`LspManager`), JSON-RPC stdio/TCP transport, UTF-16 position and workspace-edit conversion, JDTLS and TypeScript/Angular bridges, virtual decompiled documents, and batch file inspections.

## Interface
- [lib.rs:LspManager], [lib.rs:ApplyEditHook], [lib.rs:ConfigFn], [lib.rs:ConfigHook], [lib.rs:PrepareFn], [lib.rs:ServersFn]: Multi-server LSP coordinator and host integration hooks.
- [inspect.rs:InspectOptions], [inspect.rs:InspectHost], [inspect.rs:FileInspection]: Batch LSP + syntax diagnostic and unused-symbol inspection runner.
- [launch.rs:LaunchError], [launch.rs:Launcher], [launch.rs:ProcessLauncher], [launch.rs:find_root], [launch.rs:marked_root]: Server process launcher trait, default stdio implementation, and workspace root resolution.
- [scope.rs:MAX_CANDIDATES], [scope.rs:CandidateQuery], [scope.rs:CandidateFiles], [scope.rs:CandidateFn]: Trigram-index candidate file pre-filtering for cross-workspace references/renames.
- [uri.rs:path_to_uri_string], [uri.rs:path_to_uri], [uri.rs:uri_to_path], [uri.rs:uri_target], [uri.rs:is_virtual_uri]: Bidirectional `Path` <-> `lsp_types::Uri` and virtual URI helpers.
- [jdtls.rs:configure_source_paths], [jdtls.rs:JavaBuildStatus], [jdtls.rs:JavaClasspath], [jdtls.rs:JavaLaunchInfo], [jdtls.rs:JavaCompileError], [jdtls.rs:JavaExternalBuild], [jdtls.rs:compile_errors], [jdtls.rs:parse_classpaths], [jdtls.rs:parse_port], [jdtls.rs:parse_vm_location]: JDTLS workspace, build, and debug/test integration APIs.
- [types.rs:CompletionList], [types.rs:CompletionItem], [types.rs:Hover], [types.rs:SignatureHelp], [types.rs:CodeAction], [types.rs:InlayHint], [types.rs:SemanticTokens], [types.rs:CodeLens], [types.rs:DocumentLink], [types.rs:HierarchyItem], [types.rs:ServerInfo], [types.rs:VirtualDocument], [types.rs:References], [types.rs:Renamed], [types.rs:CallHierarchy], [types.rs:ClassifiedReference]: Typed LSP query results.
- [jdtls](./jdtls/README.md) - Eclipse JDTLS code-style formatter configuration, bundling the IntelliJ-compatible Eclipse formatter XML profile and wiring profile settings into JDTLS launch configurations.

## Invariants
- Language servers are keyed by `(ServerName, WorkspaceRoot)` in `LspManager`; concurrent callers awaiting the same server key share a single `OnceCell` initialization (`[lib.rs:ServerKey]`, `[lib.rs:LspManager::ensure_running]`).
- After a server crash, `LspManager` automatically restarts up to `MAX_CRASH_RESTARTS` (4) times with exponential backoff (`CRASH_BACKOFF_BASE = 500ms`) before requiring an explicit `restart_server` call (`[lib.rs:MAX_CRASH_RESTARTS]`, `[lib.rs:LspManager::schedule_crash_restart]`).
- Open document synchronization tracks monotonic versions and caches full text so incremental or full `didChange` notifications and crash recovery re-openings remain consistent (`[lib.rs:OpenDoc]`, `[lib.rs:LspManager::did_change]`).
- All wire-level LSP UTF-16 line/character positions are clamped and converted to UTF-8 byte or character offsets against the current document snapshot (`[text.rs:lsp_pos_to_ide]`, `[text.rs:ide_pos_to_lsp]`).

## Side Effects
- Spawns language server child processes over stdio or connects over localhost TCP sockets (`[launch.rs:ProcessLauncher::launch]`, `[tcp.rs:connect]`).
- Broadcasts `IdeEvent` notifications (`DiagnosticsChanged`, `LspStatusChanged`, `ProgressChanged`, `LspLog`, `ServerNotification`, `ApplyWorkspaceEdit`, `OpenFileRequested`) (`[lib.rs:LspManager]`, `[handler.rs:ClientState]`).
- Reads/writes JDTLS `.project` / `.classpath` metadata and virtual document caches on disk (`[jdtls.rs:configure_source_paths]`, `[external_project.rs:ensure_external_project]`, `[virtual_doc.rs:VirtualDocs]`).

## Verification
- Run: `cargo test -p ide-lsp`
