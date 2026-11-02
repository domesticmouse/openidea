# OpenIDEA Rust Workspace Crate Map

All workspace crates live under `crates/` plus the desktop shell in `src-tauri/`.

## Layer 1: Foundation & Text Primitives

| Crate | Path | Responsibility |
| :--- | :--- | :--- |
| `ide-core` | `crates/ide-core` | Shared strongly-typed IDs (`DocId`, `ServerId`, `RequestId`, `TaskId`, `RunId`, `TerminalId`, `DebugSessionId`), `IpcPath`, `IdeError` / `IdeResult`, `Settings` structs, `CancellationToken`, and `IdeEvent` / `EventSink`. |
| `ide-text` | `crates/ide-text` | `ropey`-backed text buffer (`TextBuffer`), line index, UTF-8 / UTF-16 / line-column offset conversions, line endings (`Lf`, `CrLf`), and `TextChange` / `ByteEdit` application. |
| `ide-vfs` | `crates/ide-vfs` | Atomic file saving (`write_atomic`), charset/encoding detection (`encoding_rs` + `chardetng`), `.properties` ASCII escaping, trash deletion, and debounced recursive/flat directory watching (`Watcher`). |
| `ide-jsonrpc` | `crates/ide-jsonrpc` | Content-Length framed JSON-RPC 2.0 `Peer` over async `Read`/`Write` streams (used by LSP, DAP, and `openidea-server`). |

## Layer 2: Language, Syntax & Workspace State

| Crate | Path | Responsibility |
| :--- | :--- | :--- |
| `ide-language` | `crates/ide-language` | Loads `languages/*.toml` into `LanguageRegistry`. File type matching (extensions, filenames, shebangs, user overrides), comment tokens, framework UI modes (e.g. Angular templates), and `Syntax` (compiled Tree-sitter grammars + queries). |
| `ide-syntax` | `crates/ide-syntax` | Tree-sitter query execution over `SyntaxCache`: structure outlines (`StructureService`), local variable scopes, identifier extraction, JVM class/method presentation, and structural search/replace matching. |
| `ide-workspace` | `crates/ide-workspace` | Open document table (`Documents`) and `DocumentObserver` trait; `LocalHistory` (content-addressed store + retention pruning); `SettingsStore` (global + project settings); `RecentProjects`; multi-project `Workspace` manifests; build-file root auto-detection (`roots.rs`). |
| `ide-index` | `crates/ide-index` | Persistent per-project file & symbol index (`ProjectIndex`) backed by `redb`, plus `nucleo-matcher` fuzzy ranking for Go to File / Go to Symbol / Search Everywhere. |
| `ide-search` | `crates/ide-search` | Ripgrep (`grep-searcher`) streaming Find in Files, regex validation, file masks, word candidate scanning (`files_with_word` for LSP references), and Replace in Files preview/execution. |

## Layer 3: Code Insight & Tooling Services

| Crate | Path | Responsibility |
| :--- | :--- | :--- |
| `ide-lsp` | `crates/ide-lsp` | `LspManager`: spawns and routes requests to language servers per language/root, merges results from multiple servers, bridges TypeScript (`tsserver_bridge`) and Angular, configures Eclipse JDTLS (`jdtls`), and serves virtual documents (`virtual_doc`). |
| `ide-analysis` | `crates/ide-analysis` | Built-in static analysis inspections, duplicate code detection, and Tree-sitter structural search validation/execution. |
| `ide-spell` | `crates/ide-spell` | Identifier-aware camelCase/snake_case spell checker (`spell_check`, `spell_suggest`) with bundled dictionary and custom project words. |
| `ide-sdk` | `crates/ide-sdk` | Discovers installed JDKs, Python interpreters/venvs, Go/Rust/Node toolchains, and applies user SDK overrides to language server and run configurations. |
| `ide-tools` | `crates/ide-tools` | Downloads and manages standalone language servers, debug adapters (`codelldb`, `java-debug`, `js-debug`, `debugpy`, `delve`), Lombok, JDK releases, and `openidea-server` binaries in `local_data_dir`. |
| `ide-build` | `crates/ide-build` | Parses and syncs Maven (`pom.xml`), Gradle, and Cargo project models, tasks, and dependency trees. |
| `ide-run` | `crates/ide-run` | `ProcessManager`: auto-detects and executes `RunConfig`s (Cargo, Go, Python, Node, Maven, Gradle, Tomcat, Shell, Compound), streaming output via PTY or pipes. |
| `ide-dap` | `crates/ide-dap` | `DebugManager`: Debug Adapter Protocol client, breakpoint verification, stack frames, variable inspection, expression evaluation, and JVM Hot Code Replace. |
| `ide-coverage` | `crates/ide-coverage` | Instruments test runs and parses LCOV, JaCoCo XML, and Go coverage profiles into per-line hit counts. |
| `ide-profiler` | `crates/ide-profiler` | Collects CPU profiling samples (JFR / collapsed stacks / perf) and builds call trees / flamegraphs. |

## Layer 4: Integrations & Specialized Domains

| Crate | Path | Responsibility |
| :--- | :--- | :--- |
| `ide-vcs` | `crates/ide-vcs` | Git integration (`gix` + git CLI): repository discovery (`Repos`), status, line diff hunks, staging/patch staging, commit, branches, log graph, blame, stash, shelves, and changelists. |
| `ide-github` | `crates/ide-github` | GitHub CLI (`gh`) integration for Pull Requests, reviews, diff comments, and checkout. |
| `ide-terminal` | `crates/ide-terminal` | `TerminalManager`: cross-platform PTY sessions (`portable-pty`), shell integration, and image paste storage. |
| `ide-db` | `crates/ide-db` | `DbManager`: async SQL client (`sqlx` for Postgres, MySQL, SQLite; `tiberius` for MSSQL), SSH tunnel forwarding, schema introspection, table pagination, and DDL generation. |
| `ide-http` | `crates/ide-http` | Parses `.http` / `.rest` files, resolves environment variables (`http-client.env.json`), executes requests via `reqwest`, and persists response history. |
| `ide-notebook` | `crates/ide-notebook` | Native `.ipynb` document model (undo/redo, cell operations) and Jupyter kernel lifecycle over ZeroMQ (`jupyter-zmq-client`). |
| `ide-agent` | `crates/ide-agent` | `AgentManager`: coding agent conversations, ACP / provider adapters, isolated git worktrees per task (`~/.openidea/worktrees`), diff tracking, and skills/commands. |
| `ide-plugin` | `crates/ide-plugin` | Wasmtime Component Model host (`wit/plugin.wit`) for sandboxed WASM plugins and theme plugins. |
| `ide-ssh` | `crates/ide-ssh` | SSH session pool, SFTP client (`RemoteFs`), `SSH_ASKPASS` / `GIT_ASKPASS` IPC server (`AskpassServer`), and credential storage. |
| `ide-remote` | `crates/ide-remote` | SFTP deployment mappings, remote directory comparison/sync, and editing remote files locally. |
| `ide-docker` | `crates/ide-docker` | Docker CLI / Engine integration: containers, images, volumes, networks, Compose stacks, logs, and event streams. |
| `ide-web` | `crates/ide-web` | Built-in localhost static web server (`WebServer`) for "Open in Browser" and live preview. |
| `ide-clipboard` | `crates/ide-clipboard` | Native OS file-list clipboard operations (Copy/Paste files in Project tree). |
| `ide-fonts` | `crates/ide-fonts` | Enumerates system and bundled fonts and serves custom user font bytes. |

## Layer 5: Host & Shell

| Crate | Path | Responsibility |
| :--- | :--- | :--- |
| `ide-host` | `crates/ide-host` | Combines all domain crates into `Host` and `Project`. Implements shared command logic in `src/ops/*` and compiles the `openidea-server` remote backend binary (`src/bin/server.rs`). |
| `openidea` (`src-tauri`) | `src-tauri` | Tauri v2 desktop application. Wraps `Host` in `AppState`, generates TypeScript bindings (`export_bindings`), manages native windows/menus, and bridges remote connections (`remote_dev.rs`). |
