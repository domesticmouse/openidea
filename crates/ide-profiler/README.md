# ide-profiler

## Overview
Crate root for `ide-profiler`, providing multi-language CPU profiler command preparation and call-tree/flamegraph aggregation.

## Subdirectories & Boundaries
- [src](./src/README.md) - CPU profiler command wrapping (`prepare`) and post-run profile collection (`collect`) across JVM `async-profiler`, Python `py-spy`, Node.js `--cpu-prof`, Cargo flamegraph, and Linux `perf`.
