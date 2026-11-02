# benches

## Overview
Criterion benchmarks for `ide_text::Document` measuring 1 MB file open, single-keystroke edit, 100-change batch edit, UTF-16/Pos/byte coordinate conversions, and CRLF save serialization.

## Interface
- [text.rs:bench] — Registers Criterion benchmarks `text/open_1mb`, `text/apply_keystroke_1mb`, `text/apply_100_changes_1mb`, `text/offset_to_pos_round_trip`, `text/pos_to_byte`, and `text/text_for_save_crlf_1mb`.

## Invariants
- [text.rs:sample_1mb] Generates a deterministic ~1 MB mixed ASCII + multi-byte UTF-8 (`α`, `😀`) source string for reproducible rope benchmarks.

## Side Effects
- None

## Verification
- Run: `cargo test -p ide-text`
