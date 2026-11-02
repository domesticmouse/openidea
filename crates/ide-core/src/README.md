# src

## Overview
Foundational types, settings schemas, IPC path/text primitives, child-process environment preparation, and backend-to-UI event bus shared by all OpenIDEA crates.

## Interface
- [lib.rs:CancellationToken] — Re-exported `tokio_util::sync::CancellationToken`.
- [error.rs:IdeError] / [error.rs:IdeResult] / [error.rs:ErrorKind] / [error.rs:ErrorData] — Unified IPC error type across all backend crates.
- [event.rs:IdeEvent] / [event.rs:EventSink] / [event.rs:SharedSink] / [event.rs:NullSink] / [event.rs:DebugEvent] / [event.rs:ServerStatus] / [event.rs:LspRefreshKind] / [event.rs:NotifyLevel] / [event.rs:AskpassKind] — Backend-to-frontend event bus types.
- [ids.rs:DocId] / [ids.rs:RequestId] / [ids.rs:TerminalId] / [ids.rs:RunId] / [ids.rs:DebugSessionId] / [ids.rs:ServerId] / [ids.rs:TaskId] / [ids.rs:LanguageId] — Process-unique typed numeric IDs (`next()`) and LSP language identifier.
- [ipc_path.rs:IpcPath] / [ipc_path.rs:to_ipc] — Forward-slash normalized path wrapper for IPC serialization.
- [text.rs:Pos] / [text.rs:Range] / [text.rs:Location] / [text.rs:TextChange] / [text.rs:ChangeSet] / [text.rs:TextEdit] / [text.rs:WorkspaceEdit] / [text.rs:FileEdit] / [text.rs:Severity] / [text.rs:Diagnostic] / [text.rs:SymbolKind] / [text.rs:Visibility] / [text.rs:OutlineItem] / [text.rs:MatchIndices] / [text.rs:is_virtual_uri] — UTF-16 text coordinates, edits, diagnostics, and outline symbols.
- [settings.rs:Settings] / [editor_settings.rs:EditorGeneralSettings] / [inspection_settings.rs:InspectionSettings] / [proxy_settings.rs:ProxySettings] / [remote_settings.rs:SshSettings] / [workflow_settings.rs:VcsSettings] — Layered global and project settings schemas.
- [process.rs:prepare] / [process.rs:host_env] / [process.rs:set_shell_env] / [process.rs:set_env_overlay] / [process.rs:find_executable] / [tool_path.rs:search_path] / [tool_path.rs:find_command] / [version_managers.rs:managers] / [output_encoding.rs:decode_tool_output] / [output_encoding.rs:fix] / [output_encoding.rs:NoticeFilter] — Child-process spawning, PATH/shim resolution, and Windows/JVM output encoding helpers.
- [ignored.rs:IgnoredNames] / [ignored.rs:DEFAULT_IGNORED_NAMES] / [jsonc.rs:strip] / [project_dir.rs:DIR] / [project_dir.rs:path] / [task.rs:CancelHandle] / [task.rs:on_cancel] / [task.rs:cancel] — Ignored file matching, JSONC comment stripping, `.openidea` path resolution, and background task cancellation.

## Invariants
- [text.rs:Pos] / [text.rs:TextChange] — All line numbers are 0-based and all columns/offsets crossing IPC are UTF-16 code units.
- [ipc_path.rs:to_ipc] — Serializes all Windows paths (`C:\a\b` and `\\?\C:\a\b`) with `/` separators so frontend code never handles backslashes.
- [tool_path.rs:search_dirs] — Retains only absolute directories in `search_path()` so relative entries (`.`, `node_modules/.bin`) cannot hijack IDE-spawned tools.
- [process.rs:host_env] — Strips `$APPDIR` entries on Linux AppImage builds and preserves any environment variables explicitly set by the caller before `prepare`.

## Side Effects
- [process.rs:set_shell_env] / [process.rs:set_env_overlay] / [proxy_settings.rs:set_proxy] / [task.rs:on_cancel] — Mutates process-wide `RwLock`/`Mutex` state for login shell variables, environment overlays, active HTTP proxy settings, and cancellable background task callbacks.
- [process.rs:user_path] — Reads `HKCU\Environment\Path` from the Windows Registry via Win32 `RegGetValueW`.

## Verification
- Run: `cargo test -p ide-core`
