# benches

## Overview
Criterion benchmark suite measuring uncached, cached-hit, and incremental-keystroke Tree-sitter outline, folding, and selection queries on synthetic 2 MiB Rust sources.

## Interface
- [syntax.rs:rust_source]: Builds a deterministic Rust source string of at least `target_bytes` bytes for benchmarking.
- [syntax.rs:bench]: Registers the `syntax_2mb` Criterion benchmark group.

## Invariants
- Synthetic benchmark input (`rust_source`) is deterministically padded to the target byte size (~2 MiB) so parse and query timings are reproducible across runs (`[syntax.rs:rust_source]`).

## Side Effects
- Executes Criterion benchmark group `syntax_2mb` (`outline_uncached`, `folding_uncached`, `outline_cached_hit`, `folding_cached_hit`, `selection_cached_hit`, `folding_after_keystroke_incremental`, `outline_after_keystroke_incremental`) and writes timing reports (`[syntax.rs:bench]`).

## Verification
- Run: `cargo test -p ide-language`
