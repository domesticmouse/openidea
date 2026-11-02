# ide-syntax

## Overview
Crate root for the Tree-sitter structure service (`StructureService`), bundled `locals.scm` queries, and CLI examples.

## Subdirectories & Boundaries
- [examples](./examples/README.md) - Standalone example binaries (`dump.rs`, `query.rs`) for inspecting Tree-sitter syntax structure queries and grammar support.
- [src](./src/README.md) - Tree-sitter structure service providing AST node/expression lookup, syntax error reporting, lexical local-variable scope resolution (`locals.scm`), identifier occurrence scanning, structural ranges, and JVM type presentation.
