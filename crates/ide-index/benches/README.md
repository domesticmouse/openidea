# benches

## Overview
Criterion benchmark suite measuring `ide_index` file and symbol search latency across a synthetic 100,000-file repository.

## Interface
- `criterion_group!(benches, bench)` (`[index.rs:benches]`)

## Invariants
- Asserts `SyntheticIndex::generate(100_000)` produces exactly 100,000 indexed files before benchmarking `find_files` and `find_symbols` (`[index.rs:bench]`).

## Side Effects
- Builds an in-memory `SyntheticIndex` and executes Criterion benchmark iterations (`[index.rs:bench]`).

## Verification
- Run: `cargo test -p ide-index`
