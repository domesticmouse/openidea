# java

## Overview
Caches and snapshots compiled Maven build outputs per launch session so repeated Java launches reuse unchanged classpaths without overlapping active processes.

## Interface
- `pub(crate) struct Session` with `Session::new`, `Session::acquire`, and `Session::build` (`[cache.rs:Session]`)
- `pub(super) struct Outputs { pub dirs: Vec<PathBuf>, pub fingerprint: Option<LaunchFingerprint> }` (`[cache.rs:Outputs]`)
- `pub(super) fn has_record(cache_dir: &Path, root: &Path, pom_dir: &Path, include_tests: bool) -> bool` (`[cache.rs:has_record]`)

## Invariants
- Reuses a cached launch snapshot only when pre-build and post-build `LaunchFingerprint`s match and all snapshot subdirectories exist (`[cache.rs:Outputs::reusable]`, `[cache.rs:Session::build]`).
- Writes `"null"` to the record file before compiling so failed builds or deleted mixed-language sources force a fresh Maven build on the next launch (`[cache.rs:Session::build]`).
- `remove_unreferenced` only deletes direct `launch-*` child directories inside `java-launch/snapshots` and never follows symlinks (`[cache.rs:remove_unreferenced]`, `[cache.rs:remove_tree_nofollow]`).

## Side Effects
- Creates, copies, and sweeps snapshot directories and JSON records under `<cache_dir>/java-launch/{records,snapshots}` (`[cache.rs:Session::build]`, `[cache.rs:sweep]`, `[cache.rs:Session::drop]`).
- Invokes `ide_build::compile::build_for_launch` when cached outputs are stale or missing (`[cache.rs:Session::build]`).

## Verification
- Run: `cargo test -p ide-host`
