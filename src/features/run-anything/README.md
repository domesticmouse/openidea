# run-anything

## Overview
Run Anything popup (⌃⌃) for executing arbitrary shell commands in the project directory, run configurations, or build tool goals contributed via registerRunAnythingProvider.

## Interface
- [index.ts:activate] Registers runAnything.popup overlay (RunAnythingHost) and action runAnything.show.
- [RunAnything.tsx:useRunAnything] [RunAnything.tsx:showRunAnything] [RunAnything.tsx:runCommandLine] [RunAnything.tsx:RunAnythingHost] Zustand open state, popup component, and command line dispatcher that delegates to matching runAnythingProviderList() providers or falls back to run.runConfig with a shell configuration.
- [model.ts:MAX_HISTORY] [model.ts:readHistory] [model.ts:pushHistory] [model.ts:clearHistory] [model.ts:Row] [model.ts:matches] [model.ts:buildRows] [model.ts:shellCommand] [model.ts:shellRunConfig] Per-project command history (capped at MAX_HISTORY = 30), row builder (up to 8 items per group), and platform shell command builder (cmd.exe /d /s /c on Windows, /bin/sh -c on POSIX).

## Invariants
- [model.ts:pushHistory] [model.ts:readHistory] Deduplicates command history entries per project scope and caps stored entries at MAX_HISTORY (30).
- [RunAnything.tsx:runCommandLine] Ignores empty command lines or calls when no project root is open.

## Side Effects
- [model.ts:readHistory] [model.ts:pushHistory] [model.ts:clearHistory] Reads and writes localStorage under openidea.runAnything.history.v1:<scope>.
- [RunAnything.tsx:runCommandLine] Invokes provider execute(text) callbacks or executes run.runConfig action.

## Verification
- Run: `pnpm test`
