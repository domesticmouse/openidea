# examples

## Overview
Command-line example binary that detects and prints run configurations for a target project directory.

## Interface
- [detect.rs:main] — CLI entry point (`cargo run -p ide-run --example detect -- <dir>`) that runs `ide_run::detect` on the given path (defaulting to `.`) and prints each detected `RunConfig` id, command, arguments, and working directory.

## Invariants
- [detect.rs:main] Defaults the target directory to `.` when no CLI argument is provided.

## Side Effects
- [detect.rs:main] Reads project build files from disk via `ide_run::detect` and writes formatted run configurations to stdout.

## Verification
- Run: `cargo test -p ide-run`
