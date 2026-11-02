# scripts

## Overview
Worktree-isolated development session manager (`dev-session.ts`), Tauri/Vite dev runner (`tauri.ts`), MCP bridge status inspector (`dev-status.ts`), and theme tooling (`scripts/theme`).

## Interface
- [dev-session.ts:worktreeRoot] — Resolved filesystem root of the current git worktree and its FNV-1a 64-bit identifier `ai.l0ng.openidea.dev.w<hex>` (`[dev-session.ts:worktreeId]`)
- [dev-session.ts:DevSession] — Session metadata interface (`worktree`, `identifier`, `pid`, `devUrl?`)
- [dev-session.ts:preferredVitePort] — Deterministic preferred Vite port (`15000..34999`) derived from the worktree identifier
- [dev-session.ts:claimSession] — Acquires a live localhost TCP listener lock (`43000..53099`) for the worktree or returns `{ existing: DevSession }` if already running
- [dev-session.ts:withDevConfig] — Injects `--config {"build":{"beforeDevCommand":"","devUrl":...}}` into Tauri CLI arguments
- [tauri.ts:main] — CLI wrapper (`pnpm tauri` / `pnpm dev:app`) that claims a worktree dev session, starts an in-process Vite dev server, and spawns `@tauri-apps/cli/tauri.js`
- [dev-status.ts:inspectBridge] — CLI script (`pnpm dev:status`) probing localhost WebSocket ports `9223..9322` for `plugin:mcp-bridge|get_backend_state` matching the current worktree identifier
- [theme](./theme/README.md) - Theme token extraction, documentation generator (`pnpm gen:theme-docs`), and IntelliJ `.theme.json` / `.icls` theme importer (`pnpm theme:import-intellij`).

## Invariants
- [dev-session.ts:claimSession] — Uses a live OS TCP server socket on `127.0.0.1` as the session lock so crashed processes never leave stale lockfiles; throws if 100 consecutive ports are exhausted
- [dev-status.ts:bridges] — Exits with code `1` if no running MCP bridge on ports `9223..9322` matches `worktreeId(worktreeRoot)`

## Side Effects
- [dev-session.ts:claimSession] — Binds a TCP server on `127.0.0.1` or connects to existing ports to read active `DevSession` JSON payloads
- [tauri.ts:main] — Starts a Vite dev server, spawns `@tauri-apps/cli/tauri.js` in a child process group, and forwards `SIGINT`/`SIGTERM` signals (`taskkill` on Windows, process-group `SIGTERM`/`SIGKILL` on Unix)
- [dev-status.ts:inspectBridge] — Opens up to 100 concurrent WebSocket connections to `ws://127.0.0.1:9223..9322` with a 2000ms timeout

## Verification
- Run: `pnpm test`
