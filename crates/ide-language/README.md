# ide-language

## Overview
Crate root for `ide-language`, uniting declarative language definitions and file-type detection with the Tree-sitter syntax and query subsystem plus benchmarks and examples.

## Subdirectories & Boundaries
- [benches](./benches/README.md) - Criterion benchmark suite measuring uncached, cached-hit, and incremental-keystroke Tree-sitter outline, folding, and selection queries on synthetic 2 MiB Rust sources.
- [examples](./examples/README.md) - Standalone executable examples (`startup_cost.rs`, `syntax_dump.rs`) exercising `ide_language` Tree-sitter parsing, syntax queries, and language registry detection.
- [src](./src/README.md) - Language configuration model, built-in and user-defined TOML language registry, file-type and hashbang/first-line language detection, project-root marker discovery, and top-level re-exports of the Tree-sitter syntax engine.
