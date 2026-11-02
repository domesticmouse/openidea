# native

## Overview
Shared in-process JSON-RPC duplex bridge (`Shim`) and binary version resolver for native CLI agents (`claude` and `codex`).

## Interface
- [mod.rs:Shim] — Trait implemented by native CLI translators (`Shim::attach`, `Shim::request`, `Shim::notify`).
- [mod.rs:serve] — Bridges a client `ide_jsonrpc::Handler` and a `Shim` over an in-memory `tokio::io::duplex` stream.
- [mod.rs:newest_install] — Finds the highest-versioned installation of a CLI binary across `ide_core::tool_path::search_path()`.
- [claude](./claude/README.md) - Native Claude Code CLI (`claude -p --input-format stream-json --output-format stream-json`) shim translating stream-json events, tool calls, settings, and `.jsonl` transcripts into the ACP session model.
- [codex](./codex/README.md) - Native OpenAI Codex CLI (`codex app-server`) shim translating Codex JSON-RPC threads, turns, approvals, and command/file items into ACP.

## Invariants
- [mod.rs:newest_install_in] — Deduplicates candidate executables on `PATH` by canonical path and selects the highest `(major, minor, patch)` version parsed by `[mod.rs:parse_version]`.

## Side Effects
- [mod.rs:version_of] — Spawns `<program> --version` subprocesses via `ide_core::process::prepare` to probe installed CLI versions.
- [mod.rs:serve] — Allocates a 1 MiB (`1024 * 1024`) `tokio::io::duplex` stream and spawns async request/notification dispatchers.

## Verification
- Run: `cargo test -p ide-agent`
