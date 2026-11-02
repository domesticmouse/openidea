# crates

## Overview
Rust workspace crates implementing OpenIDEA's platform-independent IDE core, virtual file system, Rope text buffer, tree-sitter syntax & trigram index, LSP/DAP/ACP clients, Git/database/HTTP/Docker/SSH/terminal services, WASM plugin host, and central IdeHost coordinator.

## Subdirectories & Boundaries
- [ide-agent](./ide-agent/README.md) - Crate root for `ide-agent`, providing AI coding agent management over ACP and native CLI shims.
- [ide-analysis](./ide-analysis/README.md) - Crate root for `ide-analysis`, providing Tree-sitter duplicate code detection and structural AST search.
- [ide-build](./ide-build/README.md) - Crate root for `ide-build`, providing Maven/Gradle project models, background sync, build execution, and JVM resource/WAR/Kotlin-bridge compilation.
- [ide-clipboard](./ide-clipboard/README.md) - Crate root for `ide-clipboard`, providing native OS file clipboard integration for macOS and Windows.
- [ide-core](./ide-core/README.md) - Crate root for `ide-core`, defining the shared IPC vocabulary, settings schemas, event bus, and process utilities for all OpenIDEA crates.
- [ide-coverage](./ide-coverage/README.md) - Crate root for `ide-coverage`, providing multi-language test coverage instrumentation, report parsing, and CI report importing.
- [ide-dap](./ide-dap/README.md) - Crate root for `ide-dap`, providing the Debug Adapter Protocol client, adapter process management, and multi-language debugging support.
- [ide-db](./ide-db/README.md) - Crate root for `ide-db`, providing database connections, SSH tunneling, schema introspection, SQL splitting, and table data editing across SQLite, PostgreSQL, MySQL, and SQL Server.
- [ide-docker](./ide-docker/README.md) - Crate root for `ide-docker`, driving the Docker CLI for the Services tool window.
- [ide-fonts](./ide-fonts/README.md) - Cross-platform system font enumeration and macOS user font bundling crate (`ide-fonts`).
- [ide-github](./ide-github/README.md) - GitHub CLI (`gh`) integration crate (`ide-github`) for pull request workflows and GitHub API queries.
- [ide-host](./ide-host/README.md) - Transport-agnostic IDE backend host crate (`ide-host`) shared by the Tauri desktop app and the headless `openidea-server` binary.
- [ide-http](./ide-http/README.md) - HTTP Client `.http`/`.rest` parser, environment variable resolver, request executor, and exchange history crate (`ide-http`).
- [ide-index](./ide-index/README.md) - Persistent (`redb`) and in-memory fuzzy file and symbol indexing crate (`ide-index`) for Go to File, Go to Class/Symbol, and Recent Files.
- [ide-jsonrpc](./ide-jsonrpc/README.md) - Bidirectional JSON-RPC 2.0 transport, framing, process supervision, and console output decoding crate (`ide-jsonrpc`) used by LSP, DAP, and ACP clients.
- [ide-language](./ide-language/README.md) - Crate root for `ide-language`, uniting declarative language definitions and file-type detection with the Tree-sitter syntax and query subsystem plus benchmarks and examples.
- [ide-lsp](./ide-lsp/README.md) - Crate root for `ide-lsp`, providing Language Server Protocol client management, capability negotiation, multi-server request merging, and language-specific adapters (JDTLS, TypeScript/Angular).
- [ide-notebook](./ide-notebook/README.md) - Crate root for `ide-notebook`, providing round-trip-safe `.ipynb` notebook editing and ZeroMQ-based local Jupyter kernel execution.
- [ide-plugin](./ide-plugin/README.md) - Crate root for `ide-plugin`, delivering manifest validation, filesystem-sandboxed asset loading, declarative language/theme contributions, and permission-gated WASI WebAssembly plugin execution.
- [ide-profiler](./ide-profiler/README.md) - Crate root for `ide-profiler`, providing multi-language CPU profiler command preparation and call-tree/flamegraph aggregation.
- [ide-remote](./ide-remote/README.md) - Crate root for `ide-remote`, coordinating SSH/SFTP workspace deployment, path mapping, OpenSSH config discovery, and remote run/debug target execution.
- [ide-run](./ide-run/README.md) - Crate root grouping run configuration detection, persistence, process execution, and CLI examples.
- [ide-sdk](./ide-sdk/README.md) - Crate root for SDK/toolchain discovery, external library resolution, and workspace SDK configuration overrides.
- [ide-search](./ide-search/README.md) - Crate root for project-wide text/regex search, scope pattern filtering, multi-file replacement, and search benchmarks.
- [ide-spell](./ide-spell/README.md) - Crate root for the identifier-aware spell checker and Levenshtein suggestion engine.
- [ide-ssh](./ide-ssh/README.md) - Crate root for SSH session management, SFTP/local remote filesystems, port forwarding tunnels, and askpass/Git credential stores.
- [ide-syntax](./ide-syntax/README.md) - Crate root for the Tree-sitter structure service (`StructureService`), bundled `locals.scm` queries, and CLI examples.
- [ide-terminal](./ide-terminal/README.md) - Crate root for PTY terminal session management, shell integration scripts, and streaming UTF-8 decoding.
- [ide-text](./ide-text/README.md) - Crate root for the `ropey`-backed `Document` text buffer and its Criterion performance benchmarks.
- [ide-tools](./ide-tools/README.md) - Crate root for managed external developer tools, language server/debugger installers, JDK downloads, and remote server binary fetching.
- [ide-vcs](./ide-vcs/README.md) - Crate root for `ide-vcs`, providing Git version control queries, CLI-backed repository mutations, changelists, and shelving for OpenIDEA.
- [ide-vfs](./ide-vfs/README.md) - Crate root for `ide-vfs`, providing virtual filesystem operations, text encoding detection/conversion, ZIP/JAR archive reading, directory comparison, and filesystem watching.
- [ide-web](./ide-web/README.md) - Crate root for `ide-web`, providing external browser discovery/launching and the built-in localhost static file server for web previews.
- [ide-workspace](./ide-workspace/README.md) - Crate root for `ide-workspace`, providing document buffers, layered settings, local history, project root detection, manifest dependency upgrades, and workspace state management.
