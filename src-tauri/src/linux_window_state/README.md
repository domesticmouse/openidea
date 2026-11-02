# linux_window_state

## Overview
Implements pure geometry and window-state transition calculations for Linux (`gtk`) windows so normal window sizes survive monitor scale-factor changes and maximize/fullscreen transitions without size drift.

## Interface
- [geometry.rs:Size], [geometry.rs:Mode], [geometry.rs:ModeHistory], [geometry.rs:NormalTransition], [geometry.rs:Limits], [geometry.rs:ModeAction], [geometry.rs:Geometry]: Data types representing physical/logical dimensions, maximized/fullscreen/minimized mode history, work-area limits, and restored window geometry.
- [geometry.rs:restore_size], [geometry.rs:remember_size], [geometry.rs:mode_action]: Pure functions that compute logical and physical restore sizes, filter out transient or non-normal resize events, and determine post-state-change restoration actions.

## Invariants
- [geometry.rs:restore_size]: Converts saved dimensions to the target monitor scale by preserving physical pixel size (`width * saved_scale`), rounding logical coordinates to integers (`gtk_window_resize` contract), and clamping to `Limits::in_work_area` (`min 200x120`).
- [geometry.rs:remember_size]: Rejects zero-dimension frames, frames observed while `restoring` is active, and frames while maximized, fullscreen, or minimized; preserves the previously saved physical dimensions when a resize matches the rounded logical restore request.
- [geometry.rs:ModeHistory::observe]: When leaving maximized/fullscreen mode via a move event (`configure-event` before resize), returns `NormalTransition::WaitForResize` so normal size restoration is deferred until the unmaximize resize arrives.

## Side Effects
- None

## Verification
- Run: `cargo test -p openidea`
