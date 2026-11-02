# src

## Overview
Language configuration model, built-in and user-defined TOML language registry, file-type and hashbang/first-line language detection, project-root marker discovery, and top-level re-exports of the Tree-sitter syntax engine.

## Interface
- [lib.rs:LanguageConfig], [lib.rs:FormatterConfig], [lib.rs:FrameworkUiMode], [lib.rs:IndentConfig], [lib.rs:LanguageServerConfig], [lib.rs:DebuggerConfig], [lib.rs:LanguageInfo]: Deserialized language configuration and frontend summary types.
- [lib.rs:FoldKind], [lib.rs:FoldRange], [lib.rs:SymbolTag]: Folding range and workspace symbol tag structures.
- [markers.rs:marked_root]: Finds the nearest ancestor directory matching configured project root markers.
- [registry.rs:LanguageRegistry], [registry.rs:FileTypes]: Language lookup, file-type association overrides, and path/first-line language detection.
- [syntax](./syntax/README.md) - Tree-sitter grammar registry, incremental syntax-tree and query-result LRU cache, and AST-driven queries for outlines, symbol tags, folding ranges, semantic highlights, selection expansion, literal/spelling regions, and HTML/CSS web references.

## Invariants
- Built-in `languages/*.toml` configurations (`[registry.rs:BUILTIN]`) must have unique `LanguageId`s, lowercase dot-free extensions, and non-colliding filenames/extensions (`[registry.rs:LanguageRegistry::from_configs]`).
- `LanguageRegistry::detect` resolves language identity in strict precedence order: per-file user override -> exact filename -> wildcard filename glob (longest pattern first, user before built-in) -> compound extension (longest first) -> user hashbang -> `first_line` regex/shebang -> `plaintext` (`[registry.rs:LanguageRegistry::detect]`, `[registry.rs:Tables::build]`).
- `marked_root` walks upward from `file` and never ascends above `project_root` when a bounding root is supplied (`[markers.rs:marked_root]`).

## Side Effects
- `LanguageRegistry::load` reads and parses `*.toml` user language override files from `user_dir` on the filesystem (`[registry.rs:LanguageRegistry::load]`).
- `marked_root` inspects ancestor directories on disk for exact or wildcard project marker entries (`[markers.rs:has_marker]`).

## Verification
- Run: `cargo test -p ide-language`
