# bin

## Overview
Entrypoint for the headless `openidea-server` binary that serves `ide_host::Host` over stdio JSON-RPC 2.0.

## Interface
- `fn main() -> ExitCode` (`[server.rs:main]`)

## Invariants
- Accepts only `--stdio` or `--version` (`-V`), exiting with status code `2` on any other arguments (`[server.rs:main]`).
- Directs `tracing_subscriber` output to `stderr` so `stdout` remains reserved exclusively for the JSON-RPC stream (`[server.rs:main]`).
- Inherits the login shell environment and extends `PATH` with managed tools before spawning worker threads, and bounds `host.shutdown()` to 10 seconds before exit (`[server.rs:main]`, `[server.rs:run]`).

## Side Effects
- Mutates process environment variables via `ide_host::shell_env::inherit_login_shell_env` and `ide_tools::extend_process_path` (`[server.rs:main]`).
- Creates `<base>/config` and `<base>/data` directories under `~/.openidea-server` (or `OPENIDEA_SERVER_DATA_DIR`) and runs the stdio JSON-RPC loop (`[server.rs:run]`).

## Verification
- Run: `cargo test -p ide-host`
