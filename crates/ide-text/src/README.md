# src

## Overview
Rope-backed (`ropey::Rope`) text buffer (`Document`) with line-ending normalization, incremental `TextChange` and `ByteEdit` application, Myers line diffing (`imara-diff`), and O(log N) conversions between byte offsets, UTF-16 code units, and 0-based `(line, UTF-16 col)` positions.

## Interface
- [lib.rs:LineEnding] — Line-ending enum (`Lf`, `CrLf`, `Cr`) with `as_str` and `detect`.
- [lib.rs:normalize_line_endings] — Detects the first line ending in raw text and normalizes all `\r\n` and lone `\r` sequences to `\n` (`Cow::Borrowed` when no `\r` is present).
- [lib.rs:AppliedChange], [lib.rs:BytePoint], [lib.rs:ByteEdit] — Incremental edit descriptors carrying UTF-16 `Range`s, byte ranges, and Tree-sitter `(row, column)` byte points.
- [lib.rs:Document] — Versioned rope buffer (`new`, `version`, `line_ending`, `set_line_ending`, `text`, `text_for_save`, `len_utf16`, `line_count`, `line`, `slice`, `apply`, `apply_detailed`, `apply_edits`, `apply_edits_detailed`, `replace_all`, `diff`, `offset_to_pos`, `pos_to_offset`, `byte_to_pos`, `pos_to_byte`, `offset_to_byte`, `byte_to_offset`, `rope`).
- [lib.rs:ropey] — Re-exports `ropey` for callers that inspect rope chunks directly.

## Invariants
- [lib.rs:Document::new] Internal rope storage is always normalized to `\n` (`LF`); the original `LineEnding` is recorded separately and restored only by `Document::text_for_save`.
- [lib.rs:Document::apply_detailed], [lib.rs:Document::apply_edits_detailed] Increments `Document::version` by 1 only when the changes batch is non-empty (or when `replace_all` changes buffer contents).
- [lib.rs:Document::offset_to_char_clamped] Clamps out-of-bounds UTF-16 offsets and `Pos` coordinates to valid character boundaries and rounds offsets inside surrogate pairs down to the leading surrogate.

## Side Effects
- None

## Verification
- Run: `cargo test -p ide-text`
