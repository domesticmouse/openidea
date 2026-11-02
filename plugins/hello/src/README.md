# src

## Overview
Rust WebAssembly guest implementation for the sample `hello` plugin (`openidea:plugin/plugin@0.1.0`), providing the `insert-date` and `word-count` IDE commands.

## Interface
- [lib.rs:Hello] — WASI preview 2 plugin struct exported via `export!(Hello)` implementing `Guest`
- [lib.rs:Hello::activate] — Registers `insert-date` (`Hello: Insert Date`) and `word-count` (`Hello: Word Count of Active File`) commands
- [lib.rs:Hello::deactivate] — Logs deactivation message via `host::log`
- [lib.rs:Hello::run_command] — Executes `insert-date` or `word-count` command IDs and returns `Result<Option<String>, String>`

## Invariants
- [lib.rs:Hello::run_command] — Returns `Err("no file is open in the editor")` when `word-count` is invoked without an active editor file, and `Err("unknown command: ...")` for unrecognized command IDs
- [lib.rs:today] — Formats current WASI wall-clock UTC time as `YYYY-MM-DD` using Howard Hinnant's days-to-civil algorithm (`[lib.rs:civil_from_days]`)

## Side Effects
- [lib.rs:Hello::activate] — Calls `host::log` and `host::register_command` in the IDE plugin host
- [lib.rs:Hello::run_command] — Calls `host::insert_text`, `host::active_file`, `host::read_file`, and `host::show_notification` on the IDE host

## Verification
- Run: `plugins/build.sh hello && cargo test -p ide-plugin`
