# perf

## Overview
Memory indicator status bar widget, cache-clearing actions, and developer performance diagnostics overlay measuring keystroke-to-paint latency, IPC round-trip times, and browser long tasks.

## Interface
- [index.ts:activate] Registers perfActions, the perf.memory status bar widget (MemoryStatus), and the perf.overlay developer diagnostics overlay (PerfOverlay).
- [actions.ts:perfActions] Returns Action[] for perf.clearCaches, perf.showMemory, perf.toggleMemoryIndicator, and perf.toggleOverlay.
- [MemoryStatus.tsx:MEMORY_WIDGET] [MemoryStatus.tsx:MemoryStatus] [MemoryStatus.tsx:MemoryDetails] Status bar widget (id: "perf.memory", hidden by default) polling backend metrics and rendering a memory breakdown popup.
- [PerfOverlay.tsx:PerfOverlay] Developer diagnostics overlay displaying rolling window summaries (last, p50, p95, max, overBudget) for keystroke latency, IPC ping RTT, and long tasks.
- [store.ts:usePerf] [store.ts:METRICS_INTERVAL_MS] [store.ts:refreshMetrics] [store.ts:clearCaches] Zustand store and IPC wrappers calling commands.appMetrics() and commands.clearCaches().
- [memory.ts:MemorySummary] [memory.ts:BreakdownRow] [memory.ts:BreakdownSection] [memory.ts:summarizeMemory] [memory.ts:indicatorLabel] [memory.ts:indicatorTooltip] [memory.ts:memorySections] Aggregates AppMetrics.memory across app, languageServer, and process categories.
- [events.ts:TYPING_EVENTS] [events.ts:FRAME_BUDGET_MS] [events.ts:EventEntryLike] [events.ts:isEditorTypingEntry] [events.ts:keystrokeLatencies] [events.ts:supportsEntryType] Filters Event Timing API entries inside .cm-editor and collapses durations per interactionId against a 16ms frame budget.
- [stats.ts:Summary] [stats.ts:percentile] [stats.ts:RollingWindow] Fixed-capacity ring buffer (default 200) computing nearest-rank p50/p95, max, and overBudget counts.
- [poll.ts:PollOptions] [poll.ts:startPolling] Visibility-aware interval timer that pauses while document.visibilityState === "hidden".
- [format.ts:formatBytes] [format.ts:formatSize] [format.ts:formatMs] [format.ts:formatUptime] [format.ts:formatCount] Compact binary byte, duration, uptime, and comma-grouped count formatters.

## Invariants
- [poll.ts:startPolling] Polling interval is cleared whenever doc.visibilityState === "hidden" and restarted with an immediate tick when visibility returns.
- [stats.ts:RollingWindow] Ignores non-finite sample values (!Number.isFinite(value)) and overwrites oldest samples in ring-buffer order once capacity is reached.
- [events.ts:keystrokeLatencies] Collapses multiple event entries sharing a non-zero interactionId to their maximum duration while retaining interactionId === 0 entries individually.

## Side Effects
- [store.ts:refreshMetrics] [store.ts:clearCaches] Invokes Tauri IPC commands commands.appMetrics() and commands.clearCaches() when isTauri() is true and mutates usePerf state.
- [PerfOverlay.tsx:PerfOverlay] Registers browser PerformanceObserver instances (event and longtask entry types) and intervals (IPC_PING_MS = 2000, REPAINT_MS = 500).
- [actions.ts:perfActions] Mutates status bar widget visibility via setStatusWidgetVisible and emits notifications via notifications().notify.

## Verification
- Run: `pnpm test`
