# perf

## Overview
UI and Rust backend performance harness (`pnpm perf`, `pnpm perf:rust`), interaction scenario suite (`scenarios.ts`), and performance budget manifest (`budgets.json`).

## Interface
- [run.ts:main] — UI performance runner building the workbench in `--mode perf`, serving `target/perf/dist`, running Playwright Chromium scenarios, and evaluating medians against `budgets.json`
- [rust.ts:collect] — Backend performance runner executing `cargo bench` across `ide-text`, `ide-language`, `ide-index`, and `ide-search`, collecting Criterion mean estimates (µs) against `budgets.json`
- [scenarios.ts:SCENARIOS] — Exported UI benchmark scenario suite (`startup`, `typing`, `typingLarge`, `typingAngular`, `keyRepeat`, `scroll`, `tabs`, `popups`, `toolWindows`, `projectTree`, `leaks`)
- [compare](./compare/README.md) - Cross-platform black-box IDE benchmark runner (`bench.py`) and application configuration (`apps.toml`) comparing startup, readiness, memory (RSS/USS/PSS), CPU, exit time, and disk cache size across OpenIDEA and IntelliJ IDEA.
- [lib](./lib/README.md) - Shared Playwright/CDP instrumentation, in-page frame and input latency probes, CPU profile aggregation, statistical summaries, and budget evaluation library for OpenIDEA performance gates.

## Invariants
- [run.ts:main] — Enforces UI budgets (`enforceBudgets: cpu === 1`) only at 1x CPU speed and skips gzip bundle size checks when `--unminified` is active
- [rust.ts:collect] — Only collects Criterion `benchmark.json`/`estimates.json` files modified at or after the run start timestamp unless `--no-run` is specified; exits with code 1 on budget failures or gated regressions

## Side Effects
- [run.ts:build] — Spawns `pnpm exec vite build --mode perf`, starts a local HTTP server on `127.0.0.1:0`, launches headless/headed Chromium via Playwright, and writes `target/perf/report.json`, `target/perf/report.md`, baselines, traces, and CPU profiles
- [rust.ts:main] — Spawns `cargo bench`, reads `target/criterion/**/new/{benchmark,estimates}.json`, and writes `target/perf/report-rust.json`, `target/perf/report-rust.md`, and `target/perf/baseline-rust.json`

## Verification
- Run: `pnpm test`
