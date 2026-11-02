# ide-search

## Overview
Crate root for project-wide text/regex search, scope pattern filtering, multi-file replacement, and search benchmarks.

## Subdirectories & Boundaries
- [benches](./benches/README.md) - Criterion benchmark suite measuring literal, regex, and dense-match project-wide search throughput over a synthetic 5,000-file workspace.
- [src](./src/README.md) - Ripgrep-backed (`grep-searcher` + `ignore`) project-wide Find in Files, context-filtered search (comments/strings/code literals), word candidate scanning, and multi-file Replace in Files with preview and undo support.
