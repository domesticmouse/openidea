# acp

## Overview
Agent Client Protocol (ACP) v1 and v2 wire dialect adapter and session-update event normalizer over JSON-RPC.

## Interface
- [mod.rs:Conn] — ACP JSON-RPC connection wrapper tracking negotiated protocol generation (`Conn::new`, `Conn::initialize`, `Conn::request`).
- [mod.rs:Dialect] — `ide_jsonrpc::Handler` wrapper translating ACP v2 server notifications and requests (`session/event`, `session/request_permission`, `terminal/run`) into ACP v1 form (`Dialect::wrap`, `Dialect::generation`).
- [mod.rs:Negotiated] — Parsed `initialize` response helper translating `session/load`, `session/prompt`, and `session/new` payloads (`Negotiated::from_initialize`, `Negotiated::load_session`, `Negotiated::prompt`, `Negotiated::session`, `Negotiated::meta`).
- [mod.rs:initialize_params] — Builds dual ACP v1/v2 `initialize` request parameters advertising client filesystem and terminal capabilities.
- [normalize.rs:Normalizer] — Stateful per-task translator normalizing ACP v1 and v2 `session/update` notifications into `AgentEntry` items (`Normalizer::normalize`).
- [normalize.rs:v1_content] — Extracts plain text and diff content blocks from ACP v1 tool call outputs.

## Invariants
- [mod.rs:Negotiated::from_initialize] — Distinguishes ACP v2 from v1 by the presence of a top-level `info` object (`info.version` / `info.name`), because some v2 servers echo `protocolVersion: 1`.
- [mod.rs:Dialect::on_state] — Ignores `idle` `state_update` notifications before `turn.started` unless `stopReason` is present, preventing premature turn completion.
- [normalize.rs:MAX_TERMINAL] — Caps buffered terminal output per terminal ID at 64 KiB (`64 * 1024` bytes) and strips ANSI escape sequences via `[normalize.rs:strip_ansi]`.

## Side Effects
- [mod.rs:Conn::initialize] — Sends the `initialize` JSON-RPC request over `ide_jsonrpc::Peer` and stores the negotiated protocol generation in an `AtomicU8`.
- [mod.rs:Conn::request] — Translates and sends `session/prompt`, `session/load` (`session/resume`), and `authenticate` (`auth/login`) JSON-RPC requests over the underlying `Peer`.

## Verification
- Run: `cargo test -p ide-agent`
