# examples

## Overview
Contains standalone example binary (`open_large.rs`) demonstrating and benchmarking `ide-workspace` large document opening.

## Interface
- [open_large.rs:main]: Example entrypoint opening and measuring large file loads in `ide_workspace::Documents`.

## Invariants
- None

## Side Effects
- [open_large.rs:main]: Reads target files from disk into `ide_workspace::Documents` and prints timing and memory metrics to stdout.

## Verification
- Run: `cargo test -p ide-workspace`
