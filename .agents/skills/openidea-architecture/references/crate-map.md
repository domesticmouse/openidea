# OpenIDEA Rust Workspace Crate Map (34 Crates + Tauri Shell)

All workspace crates live under `crates/` ([crates/README.md](../../../../crates/README.md)) plus the desktop shell in `src-tauri/` ([src-tauri/README.md](../../../../src-tauri/README.md)). Each crate directory and its `src/` subdirectory contain a `README.md` contract detailing its public `Interface`, `Invariants`, `Side Effects`, and `Verification` command.

## Layer 1: Foundation & Text Primitives

| Crate | Contract Path | Responsibility |
| :--- | :--- | :--- |
| `ide-core` | `crates/ide-core/src/README.md` | Shared strongly-typed IDs (`DocId`, `ServerId`, `RequestId`, `TaskId`, `RunId`, `TerminalId`, `DebugSessionId`), `IpcPath`, `IdeError` / `IdeResult`, `Settings` structs, `CancellationToken`, and `IdeEvent` / `EventSink`. |
| `ide-text` | `crates/ide-text/src/README.md` | `ropey`-backed text buffer (`TextBuffer`), line index, UTF-8 / UTF-16 / line-column offset conversions, line endings (`Lf`, `CrLf`), and `TextChange` / `ByteEdit` application. |
| `ide-vfs` | `crates/ide-vfs/src/README.md` | Atomic file saving (`write_atomic`), charset/encoding detection (`encoding_rs` + `chardetng`), `.properties` native-to-ASCII escaping, trash deletion, and debounced recursive/flat directory watching (`Watcher`). |
| `ide-jsonrpc` | `crates/ide-jsonrpc/src/README.md` | Content-Length framed and newline-delimited JSON-RPC 2.0 `Peer` over async `Read`/`Write` streams (used by LSP, DAP, ACP, and `openidea-server`). |

## Layer 2: Language, Syntax & Workspace State

| Crate | Contract Path | Responsibility |
| :--- | :--- | :--- |
| `ide-language` | `crates/ide-language/src/README.md` | Loads `languages/*.toml` into `LanguageRegistry`. File type matching (extensions, filenames, shebangs, user overrides), comment tokens, framework UI modes, and `Syntax` (`src/syntax/README.md`: compiled Tree-sitter grammars + queries). |
| `ide-syntax` | `crates/ide-syntax/src/README.md` | Tree-sitter query execution over `SyntaxCache`: structure outlines (`StructureService`), local variable scopes, identifier extraction, JVM class/method presentation, and structural search/replace matching. |
| `ide-workspace` | `crates/ide-workspace/src/README.md` | Open document table (`Documents`) and `DocumentObserver` trait; `LocalHistory` (content-addressed store + retention pruning); `SettingsStore` (global + `.idea` project settings + `.icls` import/export); `RecentProjects`; multi-project `Workspace` manifests; build-file root auto-detection (`roots.rs`). |
| `ide-index` | `crates/ide-index/src/README.md` | Persistent per-project file & symbol index (`ProjectIndex`) backed by `redb`, plus `nucleo-matcher` fuzzy ranking for Go to File / Go to Symbol / Search Everywhere. |
| `ide-search` | `crates/ide-search/src/README.md` | Ripgrep (`grep-searcher`) streaming Find in Files, regex validation, file masks, word candidate scanning (`files_with_word` for LSP references), and Replace in Files preview/execution. |

## Layer 3: Code Insight & Tooling Services

| Crate | Contract Path | Responsibility |
| :--- | :--- | :--- |
| `ide-lsp` | `crates/ide-lsp/src/README.md` | `LspManager`: spawns and routes requests to language servers per language/root, merges results from multiple servers, bridges TypeScript (`tsserver_bridge`) and Angular, configures Eclipse JDTLS (`src/jdtls/README.md`), and serves virtual documents (`virtual_doc`). |
| `ide-analysis` | `crates/ide-analysis/src/README.md` | Built-in static analysis inspections, duplicate code detection, and Tree-sitter structural search validation/execution. |
| `ide-spell` | `crates/ide-spell/src/README.md` | Identifier-aware camelCase/snake_case spell checker (`spell_check`, `spell_suggest`) with bundled dictionary and custom project words. |
| `ide-sdk` | `crates/ide-sdk/src/README.md` | Discovers installed JDKs, Python interpreters/venvs, Go/Rust/Node toolchains, downloads Eclipse Temurin JDKs, and applies user SDK overrides to language server and run configurations. |
| `ide-tools` | `crates/ide-tools/src/README.md` | Downloads and manages standalone language servers, debug adapters (`codelldb`, `java-debug`, `js-debug`, `debugpy`, `delve`), Lombok, Kotlin bridge, and `openidea-server` binaries in `local_data_dir`. |
| `ide-build` | `crates/ide-build/src/README.md` | Parses and syncs Maven (`pom.xml`), Gradle, and Cargo project models, tasks, and dependency trees; orchestrates project-wide compilation (`src/compile/README.md`) across Cargo, Gradle, Maven, Go, npm, CMake, and javac. |
| `ide-run` | `crates/ide-run/src/README.md` | `ProcessManager`: auto-detects (`src/detect/README.md`) and executes `RunConfig`s (Cargo, Go, Python, Node, Maven, Gradle, Tomcat, Shell, Compound, Docker/SSH targets), streaming output via PTY or pipes. |
| `ide-dap` | `crates/ide-dap/src/README.md` | `DebugManager`: Debug Adapter Protocol client, breakpoint verification, stack frames, variable inspection, expression evaluation, attach-to-process discovery, and JVM Hot Code Replace. |
| `ide-coverage` | `crates/ide-coverage/src/README.md` | Instruments test runs and parses LCOV, JaCoCo XML, and Go coverage profiles into per-line hit counts and package/class summaries. |
| `ide-profiler` | `crates/ide-profiler/src/README.md` | Collects CPU profiling samples (async-profiler / JFR / sample / perf / py-spy / cargo-flamegraph) and builds call trees / flamegraphs. |

## Layer 4: Integrations & Specialized Domains

| Crate | Contract Path | Responsibility |
| :--- | :--- | :--- |
| `ide-vcs` | `crates/ide-vcs/src/README.md` | Git integration (`gix` + git CLI): repository discovery (`Repos`), status, line diff hunks, staging/patch staging, commit, branches, log graph, blame, stash, shelves, interactive rebase, worktrees, and changelists. |
| `ide-github` | `crates/ide-github/src/README.md` | GitHub CLI (`gh`) integration for Pull Requests, reviews, inline diff comments, checks, and branch checkout. |
| `ide-terminal` | `crates/ide-terminal/src/README.md` | `TerminalManager`: cross-platform PTY sessions (`portable-pty`), shell integration, and clipboard image paste storage. |
| `ide-db` | `crates/ide-db/src/README.md` | `DbManager`: async SQL client (`sqlx` for Postgres, MySQL, SQLite; `tiberius` for MSSQL), SSH tunnel forwarding, schema introspection, table pagination/editing, query explain plans, and DDL generation. |
| `ide-http` | `crates/ide-http/src/README.md` | Parses `.http` / `.rest` files, resolves environment variables (`http-client.env.json`), executes requests via `reqwest`, and persists response history. |
| `ide-notebook` | `crates/ide-notebook/src/README.md` | Native `.ipynb` document model (`src/document/README.md`: undo/redo, cell operations) and Jupyter kernel lifecycle over ZeroMQ (`src/kernel/README.md`). |
| `ide-agent` | `crates/ide-agent/src/README.md` | `AgentManager`: coding agent conversations, ACP protocol (`src/acp/README.md`) and native CLI adapters (`src/native/claude/README.md`, `src/native/codex/README.md`), isolated git worktrees per task (`~/.openidea/worktrees`), diff tracking, and skills/commands. |
| `ide-plugin` | `crates/ide-plugin/src/README.md` | Wasmtime Component Model host (`wit/plugin.wit`) for sandboxed WASM plugins and theme plugins. |
| `ide-ssh` | `crates/ide-ssh/src/README.md` | SSH session pool, SFTP client (`RemoteFs`), `SSH_ASKPASS` / `GIT_ASKPASS` IPC server (`AskpassServer`), and OS keychain / fallback credential storage. |
| `ide-remote` | `crates/ide-remote/src/README.md` | SFTP deployment mappings, remote directory comparison/sync, and editing remote files locally. |
| `ide-docker` | `crates/ide-docker/src/README.md` | Docker CLI / Engine integration: containers, images, volumes, networks, Compose stacks, logs, and event streams. |
| `ide-web` | `crates/ide-web/src/README.md` | Built-in localhost static web server (`WebServer`) for "Open in Browser" and live HTML preview. |
| `ide-clipboard` | `crates/ide-clipboard/src/README.md` | Native OS file-list clipboard operations (Copy/Paste files in Project tree). |
| `ide-fonts` | `crates/ide-fonts/src/README.md` | Enumerates system and bundled fonts (`fontdb`) and imports/serves custom user font bytes. |

## Layer 5: Host & Shell

| Crate | Contract Path | Responsibility |
| :--- | :--- | :--- |
| `ide-host` | `crates/ide-host/src/README.md` | Combines all domain crates into `Host` and `Project`. Implements shared command logic in `src/ops/README.md` (`debug`, `doc`, `java`, `run`, `search`, `term`, `vcs`, etc.) and compiles the `openidea-server` remote backend binary (`src/bin/README.md`). |
| `openidea` (`src-tauri`) | `src-tauri/src/README.md` | Tauri v2 desktop application. Wraps `Host` in `AppState`, exposes `#[tauri::command]` handlers (`src/commands/README.md`), generates TypeScript bindings (`export_bindings`), manages native windows/menus, and bridges remote connections (`remote_dev.rs`). |
