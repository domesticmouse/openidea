# cargo

## Overview
Contains unit tests for `[super]` (`crates/ide-workspace/src/dependencies/cargo.rs`), covering crates.io sparse-index path layout, NDJSON index parsing, `Cargo.toml` dependency extraction, UTF-16 version spans, and semver upgrade selection.

## Interface
- [tests.rs]: Test suite verifying `index_path`, `parse_index`, `dependencies`, and `update`.

## Invariants
- [tests.rs]: Asserts that `dependencies` UTF-16 spans slice the exact version string contents (including across multi-byte UTF-16 characters) and that `update` preserves requirement operators (`^`, `~`, `=`, v-prefixes, and component count) without suggesting downgrades or unrequested prereleases.

## Side Effects
- None

## Verification
- Run: `cargo test -p ide-workspace`
