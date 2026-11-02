# profiler

## Overview
Sampling profiler run executor (profiler) and Profiler tool window rendering interactive flame graphs, top-down call trees, and sortable method lists.

## Interface
- [index.ts:activate] Registers profilerExecutor, the profiler bottom tool window (ProfilerView, ProfilerTabs), actions run.profile and run.profileContext, and resets snapshots on project change.
- [executor.ts:profilerExecutor] RunExecutorContribution (id: "profiler") that prepares profiler launches via commands.profilerPrepare and collects profiles on exit via commands.profilerCollect.
- [ProfilerView.tsx:ProfilerTabs] [ProfilerView.tsx:ProfilerView] Tool window header tabs and main view switching between Flame Graph, Call Tree, and Method List.
- [store.ts:MAX_SNAPSHOTS] [store.ts:Snapshot] [store.ts:ProfilerTab] [store.ts:useProfiler] [store.ts:addSnapshot] [store.ts:activeSnapshot] [store.ts:closeSnapshot] [store.ts:resetProfiler] Zustand store retaining up to MAX_SNAPSHOTS (5) profile snapshots per project window.
- [model.ts:profilerSupports] [model.ts:formatWeight] [model.ts:share] [model.ts:FlameRect] [model.ts:flameLayout] [model.ts:parentIndex] [model.ts:matchingFrames] [model.ts:frameLocation] [model.ts:colorSlot] Pure profile layout, frame search, weight/percentage formatting, and run configuration support detection.

## Invariants
- [store.ts:addSnapshot] Prepends new snapshots and truncates snapshots to MAX_SNAPSHOTS (5).
- [model.ts:flameLayout] Drops flame graph rectangles narrower than minWidth (default 0.002 of visible width) and renders focused node ancestors full-width (width: 1) below the focused subtree.

## Side Effects
- [executor.ts:profilerExecutor] Calls backend IPC commands.profilerPrepare(config) and commands.profilerCollect(spec), opens external download URLs via @tauri-apps/plugin-opener, shows the profiler tool window, and emits notifications.
- [ProfilerView.tsx:ProfilerView] Double-clicking a frame opens its source file and line via editorService().openFile.

## Verification
- Run: `pnpm test`
