# examples

## Overview
Standalone executable examples (`startup_cost.rs`, `syntax_dump.rs`) exercising `ide_language` Tree-sitter parsing, syntax queries, and language registry detection.

## Interface
- [startup_cost.rs:main]: Measures startup and grammar initialization costs across `ide_language`.
- [syntax_dump.rs:main]: Parses sample source files with `ide_language::Syntax` and dumps outline, folding, and highlight results.

## Invariants
- None

## Side Effects
- [startup_cost.rs:main], [syntax_dump.rs:main]: Parses inputs with `ide_language::Syntax` and writes diagnostic/benchmark output to standard output.

## Verification
- Run: `cargo test -p ide-language`
