# debug

## Overview
Coalesces high-frequency debugger `DebugEvent::Output` chunks per session before emitting `IdeEvent::Debug` notifications.

## Interface
- `pub(super) fn coalescing_sink(sink: SharedSink) -> DebugSink` (`[output.rs:coalescing_sink]`)

## Invariants
- Batches `DebugEvent::Output` per session within a 50ms window (`[output.rs:WINDOW]`) up to 64 KiB (`[output.rs:MAX_BYTES]`), splitting oversized chunks only on valid UTF-8 character boundaries (`[output.rs:coalescing_sink]`).
- Flushes any buffered output for a session before forwarding non-output `DebugEvent` control events (`Stopped`, `Terminated`, etc.) or when the output `category` changes (`[output.rs:coalescing_sink]`).

## Side Effects
- Spawns 50ms timer flush tasks on the active Tokio runtime when available and emits batched `IdeEvent::Debug` events to `SharedSink` (`[output.rs:coalescing_sink]`).

## Verification
- Run: `cargo test -p ide-host`
