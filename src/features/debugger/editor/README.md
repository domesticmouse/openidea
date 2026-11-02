# editor

## Overview
CodeMirror 6 editor extensions for the debugger providing breakpoint gutter markers and interactions, execution-point line highlighting and inline variable values, hover/selection value tooltips, Quick Evaluate mouse shortcuts, inlay Run to Cursor buttons, and inline Smart Step Into target picking.

## Interface
- [breakpointGutter.ts:BreakpointLook]
- [breakpointGutter.ts:lookOf]
- [breakpointGutter.ts:specialLook]
- [breakpointGutter.ts:breakpointGutter]
- [execution.ts:inlineValuesFor]
- [execution.ts:executionPoint]
- [valueTooltip.ts:expressionAt]
- [valueTooltip.ts:valueHint]
- [valueTooltip.ts:valueTooltip]
- [valueTooltip.ts:selectionExpression]
- [valueTooltip.ts:selectionValueTooltip]
- [quickEvaluate.ts:mouseEvaluateTarget]
- [quickEvaluate.ts:quickEvaluateMouse]
- [quickEvaluate.ts:quickEvaluateMouseDown]
- [runToCursor.ts:hoveredLine]
- [runToCursor.ts:runToCursorInlay]
- [smartStep.ts:calleeName]
- [smartStep.ts:locateStepTargets]
- [smartStep.ts:targetsOnLine]
- [smartStep.ts:pausedLineTargets]
- [smartStep.ts:startInlineSmartStep]
- [smartStep.ts:cancelSmartStep]
- [smartStep.ts:chooseStepTarget]
- [smartStep.ts:installSmartStepPicking]
- [smartStep.ts:smartStepTargets]

## Invariants
- [breakpointGutter.ts:isWriter] — When a document is open in multiple split editor panes, only the originating or designated writer view updates stored breakpoint line numbers on document changes.
- [execution.ts:inlineValuesFor] — Renders at most MAX_PER_LINE (4) local variable inline values per line within INLINE_SPAN (60) lines above the current execution point.
- [valueTooltip.ts:valueTooltip], [valueTooltip.ts:selectionExpression] — Suppresses debugger hover tooltips while the navigation modifier (Cmd/Ctrl) is held, and limits selection evaluation to single-line selections of at most MAX_SELECTION (200) characters.
- [smartStep.ts:startInlineSmartStep] — Activates inline Smart Step Into picking only when the execution point is at the top stack frame, open in an editor, and at least 2 targets can be unambiguously located on the line.

## Side Effects
- [valueTooltip.ts:valueTooltip], [valueTooltip.ts:selectionValueTooltip] — Evaluates hovered or selected expressions in the active paused session via evaluate(expr.text, 'hover', session) and mounts expandable React value trees into tooltip DOM nodes.
- [smartStep.ts:installSmartStepPicking] — Suspends global keyboard shortcut dispatch (suspendKeyboardDispatch()) and attaches window capture listeners for keyboard/mouse target selection while inline Smart Step Into is active.
- [runToCursor.ts:runToCursorInlay] — Mounts an inlay button into view.dom on line-start hover that dispatches 'debug.resume' or runToLine(path, line).

## Verification
- Run: `pnpm test`
