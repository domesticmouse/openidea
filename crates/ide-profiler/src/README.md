# src

## Overview
CPU profiler command wrapping (`prepare`) and post-run profile collection (`collect`) across JVM `async-profiler`, Python `py-spy`, Node.js `--cpu-prof`, Cargo flamegraph, and Linux `perf`.

## Interface
- [lib.rs:ProfileFrame], [lib.rs:ProfileNode], [lib.rs:MethodStat], [lib.rs:Profile], [lib.rs:ProfileSpec], [lib.rs:ProfilerInstall], [lib.rs:ProfilePrepared], [lib.rs:collect]: Profile call-tree types and post-run profile collector.
- [plan.rs:prepare]: Rewrites a `RunConfig` to capture CPU profiles with the appropriate language profiler.

## Invariants
- `TreeBuilder::finish` renumbers call-tree nodes in depth-first order with children sorted heaviest-first (`total` weight descending) and computes per-method totals counting recursive frames only once per stack (`[tree.rs:TreeBuilder::finish]`, `[tree.rs:methods]`).
- When multiple V8 `.cpuprofile` files are emitted (e.g. `pnpm`/`npm` wrapper plus child Node process), `collect_cpuprofile_dir` prefers the profile containing frames under `project_root` outside `node_modules` (`[lib.rs:collect_cpuprofile_dir]`, `[formats.rs:cpuprofile_runs_code_of]`).
- `prepare` rejects commands that lack an executable or use unsupported profiler/OS combinations before spawning (`[plan.rs:prepare]`).

## Side Effects
- `prepare` creates temporary profile output files/directories under `std::env::temp_dir()` and probes profiler binaries on `PATH` or managed install paths (`[plan.rs:prepare]`, `[plan.rs:temp_output]`).
- `collect` reads profile artifacts from disk, runs `perf script` when collecting Linux `perf.data`, resolves JVM class frames against workspace `src/<set>/<lang>` trees, and deletes temporary profile artifacts (`[lib.rs:collect]`, `[jvm.rs:resolve_sources]`).

## Verification
- Run: `cargo test -p ide-profiler`
