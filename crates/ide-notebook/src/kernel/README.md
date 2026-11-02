# kernel

## Overview
Jupyter kernel child-process lifecycle (`Process`) and HMAC-SHA256 authenticated ZeroMQ Wire Protocol v5.3 session (`Running`) over Shell, IOPub, Stdin, and Control channels.

## Interface
- [process.rs:Process]: Jupyter kernel child-process wrapper managing runtime directories, connection files, and process-group signals.
- [transport.rs:Running]: Active ZeroMQ Wire Protocol v5.3 session handling cell execution and output streaming.

## Invariants
- `Process::spawn` requires an absolute Python interpreter path, generates a random 32-byte HMAC-SHA256 signing key, and places the child in an isolated Unix process group or Windows Job Object (`[process.rs:Process::spawn]`).
- `Running::execute` allows at most one concurrent cell execution via an atomic `busy` flag (`[transport.rs:Running::execute]`).
- ZeroMQ frames and cell outputs are bounded by `MAX_MESSAGE_BYTES` (8 MiB), `QUEUED_BYTES` (16 MiB), `MAX_OUTPUT_BYTES` (32 MiB), and `MAX_OUTPUT_MESSAGES` (10,000), and all incoming messages must pass HMAC-SHA256 signature verification (`[transport.rs:receive]`, `[transport.rs:Budget::add]`).
- A cell execution finishes only after receiving both the `execute_reply` on Shell and the `status: idle` broadcast on IOPub for the active `request_id` (`[transport.rs:Completion::complete]`).

## Side Effects
- Creates a restricted (`0700` / owner-only ACL) temporary runtime directory containing `connection.json` and `kernel.json`, binds ephemeral `127.0.0.1` TCP ports, and spawns `python -m ipykernel_launcher` (`[process.rs:Process::spawn]`, `[process.rs:restrict_dir]`).
- Connects ZeroMQ dealer/subscriber sockets to localhost kernel ports and sends `SIGINT`/Job termination or `SIGKILL` to the kernel process group on interrupt/drop (`[transport.rs:Session::connect]`, `[process.rs:Process::interrupt]`, `[process.rs:Process::kill_tree]`).

## Verification
- Run: `cargo test -p ide-notebook`
