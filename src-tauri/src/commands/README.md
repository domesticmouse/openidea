# commands

## Overview
Defines the Tauri IPC command handlers (`#[tauri::command] #[specta::specta]`) that expose `ide-host` operations, window management, native menus, font loading, plugin hosting, web previews, and remote development to the TypeScript frontend.

## Interface
- [mod.rs:builder], [mod.rs:sender]: Constructs the `tauri_specta::Builder` with all typed IPC commands and `IdeEvent` channels, and adapts a `tauri::ipc::Channel<T>` into an `mpsc::Sender<T>`.
- [app.rs:AppInfo], [app.rs:app_info], [app.rs:recent_projects], [app.rs:remove_recent_project], [app.rs:open_project], [app.rs:light_edit_open], [app.rs:project_rename]: Application metadata, recent projects list, project opening, LightEdit file opening, and project renaming commands.
- [window.rs:window_open_project], [window.rs:window_open_files], [window.rs:window_open_file_in_new_window], [window.rs:window_open_remote], [window.rs:window_new], [window.rs:window_open_settings], [window.rs:window_cycle], [window.rs:window_toggle_fullscreen]: Multi-window creation, remote window launching, settings window management, and window cycling/fullscreen commands.
- [fonts.rs:system_font_families], [fonts.rs:user_font_data]: System font family enumeration and raw binary font file loading for `@font-face` registration.
- [plugin.rs:create_host], [plugin.rs:plugin_subscribe], [plugin.rs:plugin_list], [plugin.rs:plugin_set_enabled], [plugin.rs:plugin_install_dir]: Plugin host initialization, event subscription, and plugin lifecycle commands.
- [perf.rs:StartupMetrics], [perf.rs:AppMetrics], [perf.rs:app_metrics]: Process memory, document count, and startup timing metrics commands.
- [web.rs:web_browsers], [web.rs:web_url], [web.rs:web_open]: Built-in web preview URL generation and external browser launching commands.

## Invariants
- [mod.rs:builder], [fonts.rs:user_font_data]: All Specta-exported commands return JSON-serializable `IdeResult<T>`, whereas `[fonts.rs:user_font_data]` is registered outside `collect_commands!` because it returns raw binary `tauri::ipc::Response` (`ArrayBuffer`).
- [plugin.rs:create_host]: Project-scoped plugins (`<project>/.openidea/plugins`) are loaded only when `host.trust.trusted(root)` is `true` for that project root.
- [fonts.rs:user_font_data]: Restricts reads to allowed font extensions (`ttf`, `otf`, `woff2`, `woff`, `ttc`), rejects symlinked paths outside allowed system/user font directories, and refuses files larger than `MAX_FONT_BYTES` (64 MiB).

## Side Effects
- [mod.rs]: Dispatches IPC calls onto Tokio blocking/async pools and forwards streaming responses over `tauri::ipc::Channel`.
- [plugin.rs:create_host]: Spawns the `ide-plugins` background thread and subscribes it to `IdeEvent` broadcasts.
- [window.rs], [app.rs]: Create, focus, resize, tab-group, and close native OS windows, and mutate `RecentProjects` and `WindowsState`.

## Verification
- Run: `cargo test -p openidea`
