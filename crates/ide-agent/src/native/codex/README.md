# codex

## Overview
Native OpenAI Codex CLI (`codex app-server`) shim translating Codex JSON-RPC threads, turns, approvals, and command/file items into ACP.

## Interface
- [mod.rs:connect] — Spawns `codex app-server` via `Peer::spawn_framed` and bridges it to an in-process ACP `Peer` via `CodexShim`.
- [translate.rs:Mode] / [translate.rs:ModelInfo] / [translate.rs:Settings] — Codex approval/sandbox mode presets (`full-access`, `auto`, `read-only`), model metadata, and per-session settings.
- [translate.rs:models_of] / [translate.rs:config_options] — Parses `model/list` responses and builds ACP `configOptions` selectors for model, reasoning effort, and approval mode.
- [translate.rs:MAX_OUTPUT] — Maximum retained command execution output size (`64 * 1024` bytes).

## Invariants
- [mod.rs:OUTPUT_EVERY] / [translate.rs:MAX_OUTPUT] — Throttles `item/commandExecution/outputDelta` updates to 200 ms intervals and caps retained command output at 64 KiB.
- [mod.rs:EARLY_CANCEL] — Honours `session/cancel` notifications arriving up to 1 second before `session/prompt`.
- [mod.rs:CodexShim::turn_known] — Defers sending `turn/interrupt` until Codex confirms the turn has started (`turn_id` known), avoiding race conditions on early cancellation.

## Side Effects
- [mod.rs:connect] — Spawns `codex app-server` child processes over newline-delimited JSON-RPC stdio and runs background notification/request forwarding tasks.

## Verification
- Run: `cargo test -p ide-agent`
