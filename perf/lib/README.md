# lib

## Overview
Shared Playwright/CDP instrumentation, in-page frame and input latency probes, CPU profile aggregation, statistical summaries, and budget evaluation library for OpenIDEA performance gates.

## Interface
- [harness.ts:Harness] — Per-run Playwright `Page` and `CDPSession` wrapper (`load`, `inPage`, `keysFor`, `pressAction`, `measure`, `typeMeasured`, `pressMeasured`, `refreshMs`, `counters`, `delta`, `gc`, `heapMb`, `longTasksSince`, `seed`, `openFile`, `parserIdle`, `setCursor`, `editorState`)
- [harness.ts:Scenario] — Scenario contract (`id`, `title`, `blank?`, `run(h: Harness): Promise<Metrics>`)
- [inpage.ts:installInPage] — Init-script probe installer attaching `window.__perf` (`[inpage.ts:InPage]`) to measure keydown-to-rAF latency, frame intervals, element visibility, and `PerformanceObserver` long tasks
- [profile.ts:topFunctions] — Aggregates CDP `CpuProfile` (`[profile.ts:CpuProfile]`) samples into top functions sorted by self time (`[profile.ts:FunctionCost]`)
- [report.ts:loadBudgets] — Reads `perf/budgets.json` into `Budgets` (`ui`, `rust`, `regressionTolerance`, `regressionFloor`)
- [report.ts:evaluate] — Evaluates `ScenarioResult[]` against budgets and optional baseline into a `Report` (`rows`, `errors`, `failures`, `regressions`)
- [report.ts:printReport] — Logs tabular results to stdout and returns a Markdown report string
- [stats.ts:percentile] — Nearest-rank percentile helper alongside `median`, `latencySummary`, `frameSummary`, `keySummary`, and `totalBlockingTime`

## Invariants
- [inpage.ts:installInPage] — Must remain completely self-contained without outer lexical references because Playwright serializes the function source via `context.addInitScript`
- [report.ts:evaluate] — Marks any budgeted metric for a scenario that ran but failed to produce a finite value (or went missing) as `"error"` and increments `failures`; only enforces `"over-budget"` failures when `opts.enforceBudgets` is true

## Side Effects
- [harness.ts:Harness] — Drives Chromium via Playwright and CDP (`Performance.getMetrics`, `HeapProfiler.collectGarbage`, `Runtime.getHeapUsage`, `window.__openideaPerf`)
- [inpage.ts:installInPage] — Mutates `window.__perf`, registers capture-phase DOM listeners (`keydown`, `pointerdown`, `wheel`, `input`), and starts a `longtask` `PerformanceObserver`
- [report.ts:printReport] — Reads budget JSON files via `readFileSync` (`[report.ts:loadBudgets]`) and writes formatted summary tables to `console.log`

## Verification
- Run: `pnpm test`
