# benches

## Overview
Criterion benchmark suite measuring literal, regex, and dense-match project-wide search throughput over a synthetic 5,000-file workspace.

## Interface
- [search.rs:bench] — Registers the `search_5k` Criterion benchmark group (`literal`, `regex`, and `dense_limit`) against `ide_search::search`.

## Invariants
- [search.rs:make_project] Generates a deterministic 5,000-file temporary workspace (100 directories x 50 Rust files) so benchmark runs are reproducible.

## Side Effects
- [search.rs:make_project] Creates and populates a temporary directory (`tempfile::TempDir`) with 5,000 `.rs` files on disk during benchmark execution.

## Verification
- Run: `cargo test -p ide-search`
