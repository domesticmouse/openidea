# cache

## Overview
Contains unit tests for `[super::VersionCache]`, validating in-memory and disk-backed dependency version caching, TTL expiration, negative-lookup backoff, input validation, and concurrent fetch coalescing.

## Interface
- [tests.rs:Source]: Mock `DependencyVersionSource` implementation used by cache unit tests to count fetches and simulate transient registry failures.

## Invariants
- [tests.rs:Source]: Tracks exact per-package invocation counts via `AtomicUsize` to assert that `[super::VersionCache::versions]` coalesces concurrent lookups and respects positive and negative TTLs.

## Side Effects
- [tests.rs]: Creates and removes temporary cache directories under `tempfile::tempdir()` during test execution.

## Verification
- Run: `cargo test -p ide-workspace`
