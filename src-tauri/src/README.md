# src

## Overview
Implements the Tauri desktop shell for OpenIDEA, wiring `ide_host::Host`, project trust IPC authorization, remote SSH gateway multiplexing, multi-window lifecycle management, native OS menus, and platform-specific window framing (macOS AppKit tabbing/fullscreen, Windows DWM titlebar, Linux GTK geometry persistence).

## Interface
- [lib.rs:specta_builder], [lib.rs:export_bindings], [lib.rs:run], [main.rs:main]: Application entrypoints, Tauri builder/plugin registration, IPC authorization router, and debug TypeScript binding generator (`../src/bindings.ts`).
- [state.rs:AppState], [state.rs:Startup]: Managed Tauri state holding `Arc<Host>`, `RecentProjects`, `WindowGroups`, `WebServer`, `Connections`, and startup timing milestones.
- [remote_dev.rs:Connection], [remote_dev.rs:Connections], [remote_dev.rs:RemoteStage], [remote_dev.rs:RemoteProgress], [remote_dev.rs:ChannelFrame], [remote_dev.rs:RemoteProject], [remote_dev.rs:remote_bootstrap_list]: Remote SSH `openidea-server` connection pool, JSON-RPC command forwarding, streaming channel multiplexer, and remote SFTP file dialog helper.
- [windows.rs:LAUNCH_EVENT], [windows.rs:Launch], [windows.rs:RemoteLaunch], [windows.rs:WindowsState], [windows.rs:settings_parent], [windows.rs:is_settings_window], [windows.rs:settings_label], [windows.rs:close_settings_window]: Window creation, launch payload delivery, project-to-window mapping, and modal settings window lifecycle.
- [menu.rs:MENU_EVENT], [menu.rs:OPEN_RECENT_ACTION], [menu.rs:Os], [menu.rs:Native], [menu.rs:Entry]: Declarative native OS menu builder and menu event dispatcher.
- [events.rs:IDE_EVENT], [events.rs:TauriSink]: Bridges backend `IdeEvent` emissions to Tauri frontend event listeners and internal broadcast subscribers.
- [logging.rs:init], [logging.rs:log_dir_for], [logging.rs:log_dir], [logging.rs:latest_log]: Daily-rotated file + stderr tracing subscriber and synchronous panic hook installer.
- [application_focus.rs:DEACTIVATED], [application_focus.rs:ApplicationFocus], [application_focus.rs:changed], [application_focus.rs:observe]: Tracks application-wide focus transitions to trigger focus-lost auto-save when the last IDE window blurs.
- [theme_frame.rs:builtin_frame], [window_groups.rs:WindowTab], [window_groups.rs:current], [window_groups.rs:select], [window_groups.rs:merge], [window_groups.rs:detach], [window_work_area.rs:init], [macos.rs:new_project_tab_target], [macos.rs:forget_fullscreen], [macos.rs:transfer_fullscreen], [titlebar.rs:set_height], [titlebar.rs:forget], [dev.rs:configure], [linux_window_state.rs:plugin], [linux_window_state.rs:prepare], [linux_window_state.rs:normal_size_requested], [linux_window_state.rs:save_before_close]: Platform window chrome, native macOS window tabbing, Windows custom titlebar hit-testing, Linux window state persistence, and per-worktree dev profile isolation.
- [commands](./commands/README.md) - Defines the Tauri IPC command handlers (`#[tauri::command] #[specta::specta]`) that expose `ide-host` operations, window management, native menus, font loading, plugin hosting, web previews, and remote development to the TypeScript frontend.
- [linux_window_state](./linux_window_state/README.md) - Implements pure geometry and window-state transition calculations for Linux (`gtk`) windows so normal window sizes survive monitor scale-factor changes and maximize/fullscreen transitions without size drift.

## Invariants
- [lib.rs:run]: Every incoming `ipc://localhost/<cmd>` invoke request is checked against `state.host.authorize_command` (for local windows) or `connection.authorize_command` (for remote windows) before execution; untrusted projects cannot invoke restricted commands.
- [lib.rs:run], [remote_dev.rs:Connection::invoke]: Remote windows route `REMOTE_ROUTED` commands over the SSH JSON-RPC gateway (`openidea-server`) while keeping window/menu/local-dialog commands local, and dispatch authorized synchronous commands on the main thread.
- [main.rs:main]: Checks `ide_ssh::askpass::run_helper()` before initializing logging or Tauri so SSH askpass/host-key helper invocations exit immediately after communicating over the askpass TCP socket.
- [menu.rs]: Never assigns native OS menu accelerators to IDE action items so the frontend keymap handles all shortcuts without duplicate firing.
- [dev.rs:configure]: Hashes `OPENIDEA_WORKTREE_ROOT` with 64-bit FNV-1a (`[dev.rs:fnv1a64]`) in debug builds so separate git worktrees get isolated app identifiers (`com.openidea.desktop.dev.<hash>`) and separate WebView/config directories.

## Side Effects
- [logging.rs:init]: Creates log directories (`~/Library/Logs/<id>` on macOS or `<local-data>/<id>/logs`), spawns the non-blocking daily log writer, prunes rotated logs beyond `MAX_LOG_FILES` (10), and installs a panic hook that writes synchronously to `openidea.panic.log`.
- [lib.rs:run]: Mutates the process `PATH` environment variable by merging login-shell `PATH` (`ide_core::shell_env::inherit_path`) and the managed tools directory (`<data-dir>/tools/bin`), applies pending cache invalidations (`ide_workspace::caches::apply_pending`), and starts the Tauri event loop.
- [lib.rs:export_bindings]: Writes TypeScript IPC bindings to `../src/bindings.ts` during debug builds.
- [macos.rs], [titlebar.rs], [window_work_area.rs], [linux_window_state.rs]: Install native AppKit, Win32 (`WM_NCCALCSIZE` / `WM_NCHITTEST` / `WM_GETMINMAXINFO`), and GTK window hooks and persist `.window-state-linux.json`.

## Verification
- Run: `cargo test -p openidea`
