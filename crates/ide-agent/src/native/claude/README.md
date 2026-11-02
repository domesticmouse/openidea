# claude

## Overview
Native Claude Code CLI (`claude -p --input-format stream-json --output-format stream-json`) shim translating stream-json events, tool calls, settings, and `.jsonl` transcripts into the ACP session model.

## Interface
- [mod.rs:connect] — Connects an in-process `ide_jsonrpc::Peer` to `Shim` backed by the newest installed `claude` CLI binary.
- [mod.rs:Shim] — `native::Shim` implementation managing active Claude Code sessions, control requests, permission prompts, and pending session starts (`Shim::new`, `Shim::with_cli`).
- [cli.rs:Cli] / [cli.rs:Process] — Trait and subprocess implementation spawning `claude` (`[cli.rs:BASE_ARGS]`, `[cli.rs:Start]`, `[cli.rs:Launch]`, `[cli.rs:CliIo]`, `[cli.rs:config_dir]`).
- [history.rs:read] / [history.rs:chain] / [history.rs:cut] — Reads, linearizes, and truncates Claude `.jsonl` session transcripts (`[history.rs:STEER_MARK]`, `[history.rs:transcript]`, `[history.rs:escape]`, `[history.rs:is_turn_start]`, `[history.rs:title]`).
- [settings.rs:Settings] / [settings.rs:Model] / [settings.rs:commands] — Reads Claude `settings.json`, model pickers, and custom slash commands.
- [tools.rs:call_fields] / [tools.rs:result_fields] / [tools.rs:todo_entries] / [tools.rs:is_agent] / [tools.rs:is_plan] — Maps Claude tool invocations (`Bash`, `Edit`, `Write`, `Read`, `TodoWrite`, `ExitPlanMode`) to ACP tool call kinds and diffs.
- [translate.rs:Translator] / [translate.rs:Update] — Stateful stream-json event translator converting assistant/user messages and stream deltas into ACP `session/update` payloads.

## Invariants
- [mod.rs:CONTROL_TIMEOUT] — Bounds CLI `control_request` round-trips (`initialize`, `set_model`, `set_permission_mode`, `interrupt`) to 60 seconds.
- [mod.rs:EARLY_CANCEL] — Honours `session/cancel` notifications arriving up to 2 seconds before `session/prompt`.
- [history.rs:STEER_MARK] — Marks mid-turn steered user message UUIDs with suffix `"57ee"` so `[history.rs:is_turn_start]` does not count them as turn boundaries during rollback.
- [cli.rs:Process::launch] — Removes the `CLAUDECODE` environment variable from child processes so Claude Code does not refuse to start inside an existing session.

## Side Effects
- [cli.rs:Process::launch] — Spawns `claude` subprocesses with piped stdin/stdout/stderr and background reader tasks.
- [mod.rs:Shim::save_pending] / [mod.rs:Shim::remove_pending] — Reads and writes `<temp>/openidea-claude/<sessionId>.json` files to persist pending session starts across restarts.
- [history.rs:read] / [history.rs:cut] — Reads and rewrites Claude transcript files under `<config_dir>/projects/<escaped_cwd>/<id>.jsonl`.

## Verification
- Run: `cargo test -p ide-agent`
