# src

## Overview
Core AI coding agent manager, ACP/native provider orchestration, task persistence, git worktree isolation, and ACP registry installer.

## Interface
- [lib.rs:AgentManager] — Manages agent sessions, prompts, queues, permissions, worktrees, diffs, and lifecycle (`new`, `subscribe`, `providers`, `commands`, `entries`, `create`, `prompt`, `unqueue`, `send_queued_now`, `set_config`, `fork`, `rollback`, `probe`, `cancel`, `respond`, `archive`, `unarchive`, `delete`, `changes`, `base_text`, `shutdown`).
- [lib.rs:AgentPaths] / [lib.rs:AgentSink] — Storage path configuration and broadcast/UI event sink.
- [model.rs:AgentTask] / [model.rs:AgentStatus] / [model.rs:AgentCapabilities] / [model.rs:Isolation] / [model.rs:AgentEntry] / [model.rs:AgentEvent] / [model.rs:AgentPrompt] / [model.rs:AgentProvider] / [model.rs:AgentProbe] / [model.rs:NewAgentTask] / [model.rs:AgentChange] — Public IPC data model for agent tasks, timeline entries, tool calls, plans, and file diffs.
- [registry.rs:RegistryPaths] / [registry.rs:RegistryAgent] / [registry.rs:RegistryDistribution] / [registry.rs:AgentRegistry] / [registry.rs:list] / [registry.rs:mark_updates] / [registry.rs:install] — ACP agent registry fetching, update detection, and binary/npx/uvx installation.
- [acp](./acp/README.md) - Agent Client Protocol (ACP) v1 and v2 wire dialect adapter and session-update event normalizer over JSON-RPC.
- [native](./native/README.md) - Shared in-process JSON-RPC duplex bridge (`Shim`) and binary version resolver for native CLI agents (`claude` and `codex`).

## Invariants
- [lib.rs:MAX_EMBED] — Caps embedded file context attachments in prompts at 256 KiB (`256 * 1024` bytes) of UTF-8 text.
- [lib.rs:Task::persist] — Holds the `deleted` mutex across state snapshot and atomic file rename (`<id>.json.tmp` -> `<id>.json`) so no task file is recreated after `AgentManager::delete`.
- [registry.rs:check_url] / [registry.rs:safe_segment] — Rejects non-`https://` download URLs and path-traversal segments (`..`, `/`, `\`) in registry agent IDs and versions.

## Side Effects
- [lib.rs:Task::persist] / [lib.rs:Shared::remember] — Writes per-task JSON state under `store_dir` and caches discovered models/commands in `models_file`.
- [worktree.rs:create] / [worktree.rs:remove] — Creates and removes isolated Git worktrees on `openidea/<slug>` branches via `ide_vcs::Repo`.
- [registry.rs:install] / [registry.rs:list] — Downloads registry manifests and archives via `curl` and installs agent binaries under `agents_dir`.

## Verification
- Run: `cargo test -p ide-agent`
