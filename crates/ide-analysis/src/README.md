# src

## Overview
Tree-sitter powered static analysis: token-based duplicate/clone detection (`duplicates`) and pattern-based Structural Search (`structural`).

## Interface
- [lib.rs:SourceFile] / [lib.rs:Loader] — Input file descriptor (`path`, `language`) and on-demand UTF-8 text loader callback.
- [duplicates.rs:DuplicateOptions] / [duplicates.rs:Fragment] / [duplicates.rs:DuplicateGroup] / [duplicates.rs:DuplicatesReport] / [duplicates.rs:find_duplicates] — Rolling-hash + suffix-extension duplicate code detector across project files.
- [structural.rs:StructuralQuery] / [structural.rs:StructuralMatch] / [structural.rs:StructuralFile] / [structural.rs:StructuralSummary] / [structural.rs:normalize_placeholders] / [structural.rs:validate_structural] / [structural.rs:search_structurally] — Tree-sitter AST pattern matcher supporting `$name$` single-node and `$...name$` variadic placeholders.

## Invariants
- [lib.rs:SourceFile] — All reported line numbers are 0-based and all columns are UTF-16 code units.
- [duplicates.rs:find_duplicates] — Enforces `min_tokens >= 5`, salts rolling hashes by language so cross-language matches never occur, skips buckets larger than `MAX_BUCKET` (64), and caps output at `MAX_GROUPS` (500).
- [structural.rs:search_structurally] — Defaults `max_matches == 0` to `DEFAULT_MAX_MATCHES` (5,000), caps matched snippet text at `MAX_MATCH_TEXT` (4,096 bytes), and substitutes `$name$` placeholders with valid per-language identifiers (`__openidea_sv_0_name`) before parsing.

## Side Effects
- [duplicates.rs:find_duplicates] / [structural.rs:search_structurally] — Executes parallel AST parsing and matching across the `rayon` global thread pool and invokes caller `Loader` and `on_file` callbacks.

## Verification
- Run: `cargo test -p ide-analysis`
