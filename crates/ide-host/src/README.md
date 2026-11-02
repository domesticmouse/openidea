# src

## Overview
Core `Host` and `ProjectState` orchestration layer that binds workspace state, trust enforcement, environment probing, notebooks, Kotlin compiler bridge, HTTP client state, and JSON-RPC dispatch.

## Interface
- `pub struct Host` with `Host::new`, `Host::project`, `Host::project_for`, `Host::window_for_root`, `Host::open_project_for`, `Host::close_window`, `Host::is_window_trusted`, `Host::require_trust`, `Host::trust_window`, `Host::effective_settings`, `Host::shutdown` (`[lib.rs:Host]`)
- `pub struct ProjectState` with `ProjectState::open`, `ProjectState::close`, `ProjectState::content_roots`, `ProjectState::exclude_globs`, `ProjectState::apply_file_types`, `ProjectState::refresh_index_scope`, `ProjectState::vcs_encoding`, `ProjectState::text_of`, plus `apply_workspace_edit`, `history_retention`, `apply_git_executable` (`[project.rs:ProjectState]`, `[project.rs:apply_workspace_edit]`, `[project.rs:history_retention]`, `[project.rs:apply_git_executable]`)
- `pub struct RpcRegistry` and `pub async fn serve_stdio(host: Arc<Host>, rx: mpsc::UnboundedReceiver<IdeEvent>) -> io::Result<()>` (`[rpc.rs:RpcRegistry]`, `[rpc.rs:serve_stdio]`)
- `pub struct TrustStore` and `pub fn is_safe_rpc_method(method: &str) -> bool` (`[trust.rs:TrustStore]`, `[trust.rs:is_safe_rpc_method]`)
- `pub struct EnvironmentState`, `EnvironmentSnapshot`, `IdeCapabilities`, `WslDistroInfo`, `ContainerRuntimeStatus` (`[environment.rs:EnvironmentState]`, `[environment.rs:EnvironmentSnapshot]`)
- `pub struct Notebooks` (`[notebook.rs:Notebooks]`), `pub struct HttpState` (`[requests.rs:HttpState]`), `pub struct KotlinBridge` (`[kotlin_bridge.rs:KotlinBridge]`), `pub struct JavaRuntime` (`[java_runtime.rs:JavaRuntime]`)
- Startup & path utilities: `data_dirs::resolve`, `data_dirs::migrate_if_needed`, `shell_env::inherit_login_shell_env`, `proxy::apply_from_settings`, `formatter::format_external` (`[data_dirs.rs:resolve]`, `[shell_env.rs:inherit_login_shell_env]`, `[proxy.rs:apply_from_settings]`, `[formatter.rs:format_external]`)
- [bin](./bin/README.md) - Entrypoint for the headless `openidea-server` binary that serves `ide_host::Host` over stdio JSON-RPC 2.0.
- [ops](./ops/README.md) - Transport-agnostic operation layer that exposes `Host` subsystems as RPC/IPC handlers and registers them into `RpcRegistry`.

## Invariants
- `RpcRegistry::dispatch` enforces `is_safe_rpc_method` for untrusted windows, blocking any RPC method not on the read-only allowlist until the project root is trusted (`[rpc.rs:RpcRegistry::dispatch]`, `[trust.rs:is_safe_rpc_method]`).
- `TrustStore` canonicalizes paths, grants trust when a parent directory is trusted, and persists `trusted-projects.json` atomically (`[trust.rs:TrustStore::is_trusted]`, `[trust.rs:TrustStore::trust]`).
- Opening a project in light-edit mode (`light: true`) skips language server autostart, workspace root detection, and full project indexing (`[project.rs:ProjectState::open]`).
- `data_dirs::resolve` rejects relative override paths and cleans stale `.migrating-*` staging directories before migrating legacy app directories (`[data_dirs.rs:resolve]`, `[data_dirs.rs:migrate_if_needed]`).

## Side Effects
- Initializes background services (file watchers, `ProjectIndex`, `LspManager`, `TerminalManager`, `RunManager`, `DebugManager`, `AgentManager`, `Notebooks`, `KotlinBridge`, and `EnvironmentState` probe task) and emits `IdeEvent` notifications (`[lib.rs:Host::new]`, `[project.rs:ProjectState::open]`).
- Reads and writes global/project settings, trust records, workspace state, local history, and launch caches under `config_dir` and `data_dir` (`[lib.rs:Host::new]`, `[project.rs:ProjectState::open]`).

## Verification
- Run: `cargo test -p ide-host`
