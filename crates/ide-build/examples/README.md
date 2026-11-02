# examples

## Overview
Standalone CLI example (`model`) for inspecting and syncing a project's Maven or Gradle build model.

## Interface
- [model.rs:main] — CLI entry point (`cargo run -p ide-build --example model -- <dir> [cache-dir] [maven|gradle]`) that optionally runs `ide_build::sync_maven` or `ide_build::sync_gradle` and prints `ide_build::load` as pretty JSON.

## Invariants
- [model.rs:main] — Defaults `<dir>` to `"."` and `[cache-dir]` to `<temp_dir>/ide-build-model` when omitted.

## Side Effects
- [model.rs:main] — Invokes `ide_build::sync_maven` / `ide_build::sync_gradle` (spawning Maven/Gradle subprocesses and writing cache files) and prints JSON to stdout and timing/progress to stderr.

## Verification
- Run: `cargo test -p ide-build`
