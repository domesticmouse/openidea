# sync

## Overview
Single-server operational transformation (OT) client synchronizing CodeMirror document changes with the Rust backend rope buffer.

## Interface
- [DocSync.ts:DocSync]: Per-document edit synchronization state machine managing confirmed text, one in-flight edit, and buffered local ChangeSets.
- [DocSync.ts:frameScheduler]: Default scheduler batching local edits to the next animation frame (with a 50ms background fallback timeout).
- [DocSync.ts:toTextChanges]: Converts a CodeMirror ChangeSet into backend TextChange records.
- [diff.ts:minimalChange]: Computes the single minimal replacement TextChange between two strings in linear time for snapshot resyncs.

## Invariants
- [DocSync.ts:DocSync]: Preserves localText = confirmed ∘ inflight ∘ buffer at all times with at most one edit RPC in flight per document.
- [DocSync.ts:DocSync]: Remote patches with baseVersion === serverVersion rebase inflight and buffer via ChangeSet.map(..., true); after MAX_REBASES (5) consecutive outOfSync rejections, falls back to hardResync.

## Side Effects
- [DocSync.ts:DocSync]: Invokes SyncTransport.edit and SyncTransport.snapshot IPC methods, schedules animation-frame/timer flushes, and dispatches remote deltas via SyncHost.applyRemote.

## Verification
- Run: `pnpm test`
