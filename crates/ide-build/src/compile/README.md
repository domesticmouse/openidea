# compile

## Overview
Project build planner, compiler output parser, and incremental launch fingerprinter across Maven, Gradle, Cargo, Go, and TypeScript.

## Interface
- [mod.rs:DIAGNOSTICS_SOURCE] — Diagnostic source identifier (`"build"`) for `IdeEvent::Diagnostics`.
- [mod.rs:BuildKind] / [mod.rs:BuildSystem] / [mod.rs:BuildStep] / [mod.rs:BuildPlan] / [mod.rs:BuildMessage] / [mod.rs:BuildEvent] / [mod.rs:BuildResult] — Build plan, streaming event, and compiler diagnostic types.
- [mod.rs:plan] / [mod.rs:run] / [mod.rs:diagnostics] — Plans build steps for a project or module, runs them with cancellation and output streaming, and converts `BuildMessage`s into `ide_core::Diagnostic`s.
- [mod.rs:has_non_java_sources] / [mod.rs:launch_has_non_java_sources] / [mod.rs:build_for_launch] — Detects non-Java JVM sources (Kotlin/Groovy/Scala) and runs pre-launch builds when inputs changed.
- [fingerprint.rs:LaunchFingerprint] / [fingerprint.rs:launch_fingerprint] — Computes FNV-1a fingerprints of launch inputs (`pom.xml`, `build.gradle*`, `src/main`, `src/test`) to skip redundant pre-launch builds.

## Invariants
- [mod.rs:resolve] — Converts 1-based compiler line/column numbers (`ColUnit::Char`, `ColUnit::Utf16`, `ColUnit::Byte`) into 0-based lines and UTF-16 columns.
- [fingerprint.rs:scan] — Rejects cyclic symlinked input directories (`MAX_SCAN_DEPTH = 64`) and hashes only regular file paths, lengths, and mtimes so directory mtime changes do not invalidate Maven launch fingerprints.

## Side Effects
- [mod.rs:run] — Spawns external build tools (`mvn`, `gradle`, `cargo`, `go`, `tsc`/`npm`/`pnpm`/`yarn`/`bun`) in their own process group on Unix and kills the process tree on cancellation (`[mod.rs:kill_tree]`).
- [mod.rs:Lines::line] — Reads source files from disk to convert compiler byte/char columns into UTF-16 offsets.

## Verification
- Run: `cargo test -p ide-build`
