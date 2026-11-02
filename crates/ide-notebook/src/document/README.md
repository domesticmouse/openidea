# document

## Overview
Format-preserving JSON AST (`Node`, `Member`, `Layout`) for Jupyter `.ipynb` files that keeps original byte ranges of untouched values so edits only rewrite modified subtrees.

## Interface
- [raw.rs:Node]: Format-preserving JSON AST node retaining raw byte slices for unmodified values.
- [raw.rs:Member]: Object key/value member with layout trivia.
- [raw.rs:Layout]: Indentation and whitespace layout descriptor.

## Invariants
- Unmodified JSON values retain their original source byte slice (`Node::Raw`) so `Node::write` and `Node::same_bytes` preserve exact key order, number formatting, escape sequences, and whitespace of untouched regions (`[raw.rs:Node]`, `[raw.rs:Node::same_bytes]`, `[raw.rs:Node::write]`).
- `Parser::node` rejects JSON objects containing duplicate keys with `IdeError::invalid` so round-trip serialization cannot silently drop fields (`[raw.rs:Parser::node]`).

## Side Effects
- None

## Verification
- Run: `cargo test -p ide-notebook`
