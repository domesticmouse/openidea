# dependencies

## Overview
Parses manifest dependencies, caches registry version lists in memory and on disk, and calculates `Cargo.toml` dependency version upgrades.

## Interface
- [mod.rs:DependencyVersionSource], [mod.rs:DependencyUpdate], [mod.rs:ManifestDependency], [mod.rs:DependencyVersions]: Trait for registry version providers and data structures for parsed manifest dependencies and available version upgrades.
- [cache.rs:VersionCache]: Two-tier (in-memory + `<dir>/<source>-<package>.json` disk) cache for registry version lists with per-key request coalescing.
- [cargo.rs:index_path], [cargo.rs:parse_index], [cargo.rs:dependencies], [cargo.rs:update]: Crates.io sparse-index path builder, NDJSON index parser (filtering yanked versions and sorting descending by SemVer), `Cargo.toml` dependency parser with UTF-16 spans, and upgrade recommender.
- [cache](./cache/README.md) - Contains unit tests for `[super::VersionCache]`, validating in-memory and disk-backed dependency version caching, TTL expiration, negative-lookup backoff, input validation, and concurrent fetch coalescing.
- [cargo](./cargo/README.md) - Contains unit tests for `[super]` (`crates/ide-workspace/src/dependencies/cargo.rs`), covering crates.io sparse-index path layout, NDJSON index parsing, `Cargo.toml` dependency extraction, UTF-16 version spans, and semver upgrade selection.

## Invariants
- [cache.rs:VersionCache::versions]: Validates that `source.id()` and `package` are 1..=64 ASCII alphanumeric, `-`, or `_` characters (preventing path traversal); serializes same-key fetches under a per-key `Slot` mutex; caches successful lookups for `TTL` (1 hour) and failed lookups in memory only for `FAILURE_TTL` (30 seconds); and ignores disk cache files exceeding `MAX_DISK_BYTES` (2 MiB).
- [cargo.rs:dependencies]: Extracts dependencies from `[dependencies]`, `[dev-dependencies]`, `[build-dependencies]`, `[workspace.dependencies]`, and `[target.'...'.*dependencies]`, skipping `path`, `git`, `workspace = true`, and custom `registry` entries, and reports UTF-16 code-unit spans covering only the inner version string.
- [cargo.rs:update]: Never recommends a version `<=` the current version or a prerelease when the current requirement is stable, and preserves leading operators (`^`, `~`, `=`) and version component granularity when formatting the replacement requirement.

## Side Effects
- [cache.rs:VersionCache::versions]: Reads and atomically writes `<directory>/<source>-<package>.json` via a `.json.tmp` sibling file, and invokes `DependencyVersionSource::fetch`.

## Verification
- Run: `cargo test -p ide-workspace`
