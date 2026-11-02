# src

## Overview
Debug Adapter Protocol (DAP) client, adapter resolution and launching, breakpoint store, process attach discovery, and JVM/Kotlin JDWP relays.

## Interface
- [lib.rs:DebugManager] — Manages debug sessions, per-file/function/data/exception breakpoints, stepping, variable inspection, evaluation, and HotSwap (`new`, `with_launcher`, `sessions`, `start`, `start_with`, `set_breakpoints`, `refresh_source_mirrors`, `breakpoint_states`, `exception_filters`, `set_exception_breakpoints`, `set_variable`, `threads`, `stack_trace`, `scopes`, `variables`, `evaluate`, `evaluate_in`, `resume`, `pause`, `step`, `run_to`, `force_step_over`, `step_in_target`, `step_in_targets`, `restart_frame`, `capabilities`, `redefine_classes`, `set_function_breakpoints`, `set_data_breakpoints`, `data_breakpoint_info`, `stop`).
- [lib.rs:LaunchConfig] / [lib.rs:Breakpoint] / [lib.rs:FunctionBreakpoint] / [lib.rs:DataBreakpoint] / [lib.rs:DataBreakpointInfo] / [lib.rs:StepInTarget] / [lib.rs:DebugCapabilities] / [lib.rs:HotSwapResult] / [lib.rs:BreakpointState] / [lib.rs:ThreadInfo] / [lib.rs:StackFrame] / [lib.rs:Scope] / [lib.rs:Variable] / [lib.rs:StepKind] / [lib.rs:ExceptionFilter] / [lib.rs:SessionExceptionFilters] / [lib.rs:SessionInfo] / [lib.rs:SessionOwner] / [lib.rs:launch_arguments] — Public DAP session and breakpoint types.
- [adapter.rs:AdapterSpec] / [adapter.rs:Transport] / [adapter.rs:AdapterLauncher] / [adapter.rs:ProcessLauncher] / [adapter.rs:find_config] / [adapter.rs:resolve] / [adapter.rs:find_binary] / [adapter.rs:resolve_binary] / [adapter.rs:set_tools_root] / [adapter.rs:prepare_debugpy] — Resolves and launches stdio/TCP debug adapters.
- [attach.rs:AttachKind] / [attach.rs:AttachableProcess] / [attach.rs:RawProcess] / [attach.rs:attachable_processes] / [attach.rs:list_processes] / [attach.rs:enable_node_inspector] / [attach.rs:is_go_binary] / [attach.rs:jdwp_address] — Discovers and classifies local processes for Attach to Process.
- [connection.rs:Connection] / [connection.rs:DapError] / [connection.rs:Incoming] — Content-Length framed DAP wire connection.
- [jdwp.rs:listen_for_jvm] / [kotlin.rs:SourceMirror] — Remote JVM listen-mode relay and external package-aligned Kotlin source mirror.

## Invariants
- [lib.rs:DebugManager] / [lib.rs:frame_from] — Converts 0-based client lines/columns to 1-based DAP coordinates (`linesStartAt1: true`, `columnsStartAt1: true`) on outbound requests and back to 0-based on inbound responses/events.
- [kotlin.rs:SourceMirror::create] — Verifies that `std::env::temp_dir()` is outside the canonical project directory before creating the temporary Kotlin package mirror, never writing inside the project.
- [kotlin_jdwp.rs:forward] — Holds JDWP `VirtualMachine.Resume` (1/9) and `ThreadReference.Resume` (11/3) commands (up to 1,024 packets) until the Kotlin debug adapter finishes `attach` and installs its breakpoint listeners.

## Side Effects
- [adapter.rs:ProcessLauncher::launch] / [cargo.rs:build] — Spawns debug adapter subprocesses (`codelldb`, `lldb-dap`, `debugpy`, `js-debug-adapter`, `dlv`, `netcoredbg`, `kotlin-debug-adapter`), `cargo build` pre-launch jobs, and `runInTerminal` debuggees.
- [attach.rs:list_processes] / [attach.rs:enable_node_inspector] — Runs `ps` to enumerate user processes, reads `/proc/<pid>/exe` and binary headers, and sends `SIGUSR1` via `kill -USR1` to open the Node.js inspector.
- [jdwp.rs:listen_for_jvm] / [kotlin_jdwp.rs:StartupRelay::start] — Binds local TCP listeners and relays bidirectional JDWP socket streams.

## Verification
- Run: `cargo test -p ide-dap`
