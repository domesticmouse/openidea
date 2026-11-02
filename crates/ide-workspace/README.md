# ide-workspace

## Overview
Crate root for `ide-workspace`, providing document buffers, layered settings, local history, project root detection, manifest dependency upgrades, and workspace state management.

## Subdirectories & Boundaries
- [examples](./examples/README.md) - Contains standalone example binary (`open_large.rs`) demonstrating and benchmarking `ide-workspace` large document opening.
- [src](./src/README.md) - Implements OpenIDEA's core workspace state: rope-backed open documents with UTF-16 incremental patching and undo/redo, layered TOML settings, content-addressed local history, `.editorconfig` evaluation, DevContainer configuration parsing, atomic multi-file moves with import/path rewriting, multi-root workspace files, and OS process/memory attribution.
