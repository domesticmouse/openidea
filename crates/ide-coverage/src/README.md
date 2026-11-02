# src

## Overview
Test coverage command-line instrumentation (`prepare`), report collection (`collect`), and CI tracefile import (`import`) for LCOV, JaCoCo XML, and Go cover profiles.

## Interface
- [lib.rs:LineCoverage] / [lib.rs:FileCoverage] / [lib.rs:CoverageSuite] / [lib.rs:CoverageReportSpec] / [lib.rs:InstallCommand] / [lib.rs:CoveragePrepared] — IPC coverage report and launch preparation types.
- [lib.rs:collect] — Reads and parses the coverage report described by `CoverageReportSpec` after a run finishes.
- [plan.rs:prepare] — Rewrites a `RunConfig` to collect coverage via `cargo-llvm-cov`, `go test -coverprofile`, Maven/Gradle JaCoCo, `vitest`/`jest`, `c8`, or `coverage.py`.
- [import.rs:import] — Sniffs report format (`Lcov`, `GoProfile`, `Jacoco`) from disk and relocates foreign CI file paths into the project root.

## Invariants
- [lib.rs:LineCoverage] — Line numbers are 0-based (`nr - 1`); a line with both taken and missed branches (`branches_covered < branches_total`) is marked partial even when `hits > 0`.
- [import.rs:relocate] — Relocates non-existent foreign paths to the longest suffix of path components that exists as a file under `root`.
- [jacoco.rs:locate] — Sorts `src/main` ahead of other source sets so classes compiled from main sources never resolve to identically named test files.

## Side Effects
- [plan.rs:prepare] — Probes installed coverage tools (`cargo llvm-cov --version`, `python -m coverage --version`) via subprocesses and writes `openidea-jacoco.init.gradle` into `out` for Gradle runs.
- [lib.rs:collect] / [import.rs:import] — Reads LCOV tracefiles, Go cover profiles, and JaCoCo XML reports from disk.

## Verification
- Run: `cargo test -p ide-coverage`
