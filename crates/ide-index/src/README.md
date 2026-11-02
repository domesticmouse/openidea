# src

## Overview
Maintains the persistent `redb`-backed and in-memory fuzzy file and symbol index (`ProjectIndex`) with parallel directory walking and tree-sitter symbol extraction.

## Interface
- `pub const MAX_SYMBOL_FILE: u64 = 2 * 1024 * 1024` (`[lib.rs:MAX_SYMBOL_FILE]`)
- `pub struct ProjectIndex` with `ProjectIndex::start`, `ProjectIndex::find_files`, `ProjectIndex::find_symbols`, `ProjectIndex::status`, `ProjectIndex::all_files`, `ProjectIndex::file_count`, `ProjectIndex::symbol_count`, `ProjectIndex::mark_recent`, `ProjectIndex::remove_recent`, `ProjectIndex::recent_files`, `ProjectIndex::update`, `ProjectIndex::set_excludes`, `ProjectIndex::set_scope`, `ProjectIndex::languages_changed`, `ProjectIndex::rescan` (`[lib.rs:ProjectIndex]`)
- `pub struct IndexRoots`, `pub struct FileMatch`, `pub struct SymbolMatch`, `pub struct SymbolFilter`, `pub struct IndexStatus` (`[lib.rs:IndexRoots]`, `[lib.rs:FileMatch]`, `[lib.rs:SymbolMatch]`, `[lib.rs:SymbolFilter]`, `[lib.rs:IndexStatus]`)
- `pub mod bench_support` with `pub struct SyntheticIndex` (`[lib.rs:bench_support::SyntheticIndex]`)

## Invariants
- Directory walking always skips `.git`, `.hg`, `.svn`, and `.jj` regardless of settings (`[filter.rs:ALWAYS_SKIP]`, `[filter.rs:PathFilter::is_skipped_Entry]`).
- Files larger than `MAX_SYMBOL_FILE` (2 MiB) or containing a NUL byte in the first 8 KiB are indexed by path for `find_files` but skipped for symbol extraction (`[worker.rs:MAX_SYMBOL_FILE]`, `[worker.rs:Shared::extract]`).
- Files under extra content `folders` outside `root` are indexed in memory with absolute display paths and are never written to `index.redb` (`[filter.rs:PathFilter::store_key]`, `[worker.rs:Shared::full_scan]`).
- `Store::open` wipes and recreates tables when `SCHEMA` (`"openidea-index-2"`), the syntax fingerprint, or the project root changes, and degrades to in-memory indexing if `index.redb` is locked or unreadable (`[store.rs:SCHEMA]`, `[store.rs:Store::open]`).
- Recent files are deduplicated and capped at `MAX_RECENT` (50) entries (`[data.rs:MAX_RECENT]`, `[data.rs:IndexData::mark_recent]`).

## Side Effects
- Spawns the `"openidea-index"` background worker thread (`[lib.rs:ProjectIndex::start]`) and parallel `ignore::WalkBuilder` / `rayon` worker threads (`[filter.rs:PathFilter::walk]`, `[worker.rs:Shared::index_files]`).
- Creates, reads, and compacts `<cache_dir>/index.redb` (`[store.rs:Store::open]`, `[store.rs:Store::apply]`) and emits `IdeEvent::IndexState` and `IdeEvent::Task` events (`[worker.rs:Shared::emit_state]`, `[worker.rs:Shared::full_scan]`).

## Verification
- Run: `cargo test -p ide-index`
