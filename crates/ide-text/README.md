# ide-text

## Overview
Crate root for the `ropey`-backed `Document` text buffer and its Criterion performance benchmarks.

## Subdirectories & Boundaries
- [benches](./benches/README.md) - Criterion benchmarks for `ide_text::Document` measuring 1 MB file open, single-keystroke edit, 100-change batch edit, UTF-16/Pos/byte coordinate conversions, and CRLF save serialization.
- [src](./src/README.md) - Rope-backed (`ropey::Rope`) text buffer (`Document`) with line-ending normalization, incremental `TextChange` and `ByteEdit` application, Myers line diffing (`imara-diff`), and O(log N) conversions between byte offsets, UTF-16 code units, and 0-based `(line, UTF-16 col)` positions.
