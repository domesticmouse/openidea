# ops

## Overview
Transport-agnostic operation layer that exposes `Host` subsystems as RPC/IPC handlers and registers them into `RpcRegistry`.

## Interface
- `pub type Emit<T> = Arc<dyn Fn(T) + Send + Sync>` (`[mod.rs:Emit]`)
- `pub struct Channel<T>` with `Channel::new` and `Channel::send` (`[mod.rs:Channel]`)
- `pub fn register_all(r: &mut RpcRegistry)` and `Host::blocking` (`[mod.rs:register_all]`, `[mod.rs:Host::blocking]`)
- Domain operation modules: `agent` (`[agent.rs:create]`, `[agent.rs:prompt]`), `analysis` (`[analysis.rs:inspect_code]`, `[analysis.rs:structural_search]`), `build` (`[build.rs:build_model]`, `[build.rs:build_run]`), `bytes` (`[bytes.rs:fs_probe]`, `[bytes.rs:fs_read_range]`), `coverage` (`[coverage.rs:coverage_prepare]`, `[coverage.rs:coverage_collect]`), `debug` (`[debug.rs:debug_start]`, `[debug.rs:debug_evaluate]`), `dependencies` (`[dependencies.rs:dependency_versions]`), `deploy` (`[deploy.rs:deploy_upload]`, `[deploy.rs:deploy_sync]`), `doc` (`[doc.rs:open]`, `[doc.rs:edit]`, `[doc.rs:save]`), `env` (`[env.rs:reload]`), `fs` (`[fs.rs:read_dir]`, `[fs.rs:copy_merge]`), `history` (`[history.rs:history_list_dir]`, `[history.rs:history_restore]`), `java` (`[java.rs:java_resolve_launch]`, `[java.rs:java_build_workspace]`), `jvm_icons` (`[jvm_icons.rs:jvm_file_presentations]`), `lsp` (`[lsp.rs:lsp_completion]`, `[lsp.rs:lsp_goto]`, `[lsp.rs:apply_workspace_edit]`), `nav` (`[nav.rs:find_files]`, `[nav.rs:find_symbols]`), `notebook` (`[notebook.rs:open]`, `[notebook.rs:execute]`), `project` (`[project.rs:open]`, `[project.rs:set_settings]`), `python` (`[python.rs:register]`), `refactor` (`[refactor.rs:refactor_move_files]`), `run` (`[run.rs:start]`, `[run.rs:stop]`), `sdk` (`[sdk.rs:sdk_detect]`, `[sdk.rs:sdk_python_pip]`), `search` (`[search.rs:start]`), `spell` (`[spell.rs:spell_check]`), `syntax` (`[syntax.rs:syntax_node_at]`, `[syntax.rs:syntax_structural_ranges]`), `term` (`[term.rs:spawn]`, `[term.rs:write]`), `tools` (`[tools.rs:tools_install]`, `[tools.rs:jdk_download]`), and `vcs` (`[vcs.rs:status]`)
- [debug](./debug/README.md) - Coalesces high-frequency debugger `DebugEvent::Output` chunks per session before emitting `IdeEvent::Debug` notifications.
- [doc](./doc/README.md) - Implements document, syntax-structure, and file-level local history RPC/IPC command handlers for `ide_host::ops::doc`.
- [java](./java/README.md) - Caches and snapshots compiled Maven build outputs per launch session so repeated Java launches reuse unchanged classpaths without overlapping active processes.
- [run](./run/README.md) - Implements run configuration templates, .NET launch target resolution, Spring Boot hot-update staging, Gradle JVM args scripts, JUnit report loading, and Tomcat deployment commands.
- [search](./search/README.md) - Implements workspace-wide replace-in-files, single-match replace preview, and regex/replacement validation commands.
- [term](./term/README.md) - Securely stores pasted terminal clipboard images under `<data_dir>/terminal-images` with strict permissions and 24-hour TTL cleanup.
- [vcs](./vcs/README.md) - Implements multi-root Git, diff, patch, shelf, changelist, worktree, and repository-inspection RPC/IPC commands.

## Invariants
- `Host::blocking` runs closures on Tokio's blocking pool and maps worker panics to `IdeError::internal` (`[mod.rs:Host::blocking]`).
- Mutating build, run, debug, terminal, deploy, SDK/tool installation, and VCS commands enforce `host.require_trust(window)` before executing untrusted workspace code (`[build.rs:build_sync]`, `[debug.rs:debug_start]`, `[term.rs:spawn]`, `[deploy.rs:deploy_upload]`).

## Side Effects
- Coordinates filesystem mutations, LSP requests, build/run/debug/terminal/agent child processes, remote SFTP/FTP transfers, and streaming `Channel<T>` / `IdeEvent` notifications across all IDE subsystems (`[mod.rs:register_all]`).

## Verification
- Run: `cargo test -p ide-host`
