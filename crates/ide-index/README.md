# ide-index

## Overview
Persistent (`redb`) and in-memory fuzzy file and symbol indexing crate (`ide-index`) for Go to File, Go to Class/Symbol, and Recent Files.

## Subdirectories & Boundaries
- [benches](./benches/README.md) - Criterion benchmark suite measuring `ide_index` file and symbol search latency across a synthetic 100,000-file repository.
- [src](./src/README.md) - Maintains the persistent `redb`-backed and in-memory fuzzy file and symbol index (`ProjectIndex`) with parallel directory walking and tree-sitter symbol extraction.
