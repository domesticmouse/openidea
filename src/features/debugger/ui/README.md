# ui

## Overview
Debugger user interface components providing the Debug tool window (Threads & Variables and Console tabs), lazy Variables tree with inline value editing and change highlighting, interactive Debug Console REPL, and debugger dialogs/popovers (Breakpoint properties, View Breakpoints, Evaluate Expression, Attach to Process, Smart Step chooser).

## Interface
- [DebugToolWindow.tsx:DebugTabs]
- [DebugToolWindow.tsx:DebugToolWindow]
- [DebugConsole.tsx:DebugServiceConsole]
- [DebugConsole.tsx:DebugConsole]
- [VariablesView.tsx:VarRoot]
- [VariablesView.tsx:ValueChanges]
- [VariablesView.tsx:VariablesViewProps]
- [VariablesView.tsx:VariablesView]
- [ValueHintTree.tsx:mountValueTree]
- [Overlays.tsx:DebuggerOverlays]
- [popoverStore.ts:InlineStepTarget]
- [popoverStore.ts:useOverlays]
- [popoverStore.ts:openBreakpointPopover]
- [popoverStore.ts:closeBreakpointPopover]
- [popoverStore.ts:openEvaluateDialog]
- [popoverStore.ts:openBreakpointsDialog]
- [popoverStore.ts:openConfigChooser]
- [popoverStore.ts:openSmartStepChooser]
- [focusedView.ts:DebugViewCommands]
- [focusedView.ts:focusDebugView]
- [focusedView.ts:blurDebugView]
- [focusedView.ts:focusedDebugView]
- [expressionHistory.ts:HistoryKind]
- [expressionHistory.ts:readHistory]
- [expressionHistory.ts:pushHistory]
- [expressionHistory.ts:setConsoleHistoryBrowser]
- [expressionHistory.ts:browseFocusedConsoleHistory]

## Invariants
- [VariablesView.tsx:ValueChanges] — Tracks variable value changes per scope (session:thread:frameDepth) across stop epochs and evicts the oldest entry once 64 scopes are cached.
- [VariablesView.tsx:VarEditRow] — Acquires a popup layer token (acquirePopup()) while editing a variable value so pressing Escape cancels the inline edit rather than triggering the global 'focus editor' shortcut.
- [expressionHistory.ts:pushHistory] — Deduplicates expressions and retains at most MAX (50) recent entries per HistoryKind ('condition', 'evaluate', 'console') and project root.

## Side Effects
- [DebugToolWindow.tsx:VariablesPane], [VariablesView.tsx:VariablesView] — Fetches frame scopes, child variables, and data breakpoint info via commands.debugScopes, commands.debugVariables, and commands.debugDataBreakpointInfo, and mutates variable values via setVariableValue.
- [expressionHistory.ts:pushHistory] — Persists per-project expression history under localStorage['openidea.debugger.history.<kind>:<root>'].
- [focusedView.ts:focusDebugView], [focusedView.ts:blurDebugView] — Updates 'debugFramesFocus' and 'debugVariablesFocus' context keys via setContext.

## Verification
- Run: `pnpm test`
