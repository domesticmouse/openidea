# examples

## Overview
Standalone example binaries (`dump.rs`, `query.rs`) for inspecting Tree-sitter syntax structure queries and grammar support.

## Interface
- [StructureService] — CLI example targets under `crates/ide-syntax/examples` invoked via `cargo run -p ide-syntax --example <name>` over `ide_syntax::StructureService`.

## Invariants
- Operates read-only over `ide_language::Syntax` and `ide_syntax::StructureService`.

## Side Effects
- Reads input source files from disk and prints structural analysis output to stdout.

## Verification
- Run: `cargo test -p ide-syntax`
