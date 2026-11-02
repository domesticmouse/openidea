# breakpoints

## Overview
Breakpoint state management and models covering line breakpoints, method (function) breakpoints, field watchpoints (data breakpoints), exception breakpoint filters, language placement guards, and smart declaration detection.

## Interface
- [model.ts:BreakpointEntry]
- [model.ts:BreakpointStatus]
- [model.ts:newBreakpointId]
- [model.ts:makeBreakpoint]
- [model.ts:suspends]
- [model.ts:hitMessage]
- [model.ts:logParts]
- [model.ts:interpolateLog]
- [model.ts:sortByLine]
- [model.ts:toggleLine]
- [model.ts:isConditional]
- [model.ts:mapLines]
- [model.ts:toBackend]
- [model.ts:sameEntries]
- [store.ts:useBreakpoints]
- [store.ts:sanitizeByFile]
- [store.ts:loadProjectBreakpoints]
- [store.ts:breakpointsOf]
- [store.ts:setFileBreakpoints]
- [store.ts:toggleBreakpoint]
- [store.ts:addBreakpoint]
- [store.ts:updateBreakpoint]
- [store.ts:removeBreakpoint]
- [store.ts:removeAllBreakpoints]
- [store.ts:setMuted]
- [store.ts:syncAllBreakpoints]
- [store.ts:refreshStates]
- [special.ts:MethodBreakpoint]
- [special.ts:AccessType]
- [special.ts:FieldWatchpoint]
- [special.ts:SpecialBreakpoint]
- [special.ts:useSpecialBreakpoints]
- [special.ts:sanitizeSpecial]
- [special.ts:toFunctionBreakpoints]
- [special.ts:toDataBreakpoints]
- [special.ts:syncSpecial]
- [special.ts:loadSpecialBreakpoints]
- [special.ts:addMethodBreakpoint]
- [special.ts:addFieldWatchpoint]
- [special.ts:updateSpecial]
- [special.ts:removeSpecial]
- [special.ts:removeAllSpecial]
- [special.ts:specialAt]
- [special.ts:toggleMethodAt]
- [special.ts:toggleFieldAt]
- [special.ts:setSpecialLines]
- [exceptions.ts:ExceptionFilterEntry]
- [exceptions.ts:ExceptionFilterGroup]
- [exceptions.ts:useExceptionBreakpoints]
- [exceptions.ts:sanitizeChoices]
- [exceptions.ts:enabledFilters]
- [exceptions.ts:exceptionGroups]
- [exceptions.ts:refreshExceptionFilters]
- [exceptions.ts:loadExceptionBreakpoints]
- [exceptions.ts:setExceptionFilter]
- [languages.ts:canPlaceBreakpoint]
- [symbols.ts:MethodTarget]
- [symbols.ts:FieldTarget]
- [symbols.ts:methodAt]
- [symbols.ts:fieldAt]
- [toggle.ts:toggleBreakpointSmart]

## Invariants
- [model.ts:mapLines] — Maps breakpoint lines through document edits using assoc=1 so text inserted at the start of a line pushes the breakpoint down, and deduplicates merged lines so the first breakpoint wins.
- [model.ts:toBackend] — Converts non-suspending breakpoints into DAP logpoints and disables any non-suspending breakpoint that has neither logHit nor logMessage.
- [store.ts:syncFile], [special.ts:syncSpecial], [exceptions.ts:syncAdapter] — Skips backend synchronization when the open project is untrusted (useProject.getState().project?.trusted === false).
- [languages.ts:canPlaceBreakpoint] — Rejects placing breakpoints in decompiled .class files (classFileBreakpointReason(path)) and restricts placement to debuggable languages unless workflowSettings().debugger.breakpointsAnyFile is enabled.

## Side Effects
- [store.ts:persist], [special.ts:persist], [exceptions.ts:persist] — Persists per-project line breakpoints (openidea.debugger.breakpoints:<root>), method/field breakpoints (openidea.debugger.specialBreakpoints:<root>), and exception filter choices (openidea.debugger.exceptions:<root>) in localStorage.
- [store.ts:syncFile], [special.ts:syncSpecial], [exceptions.ts:syncAdapter] — Synchronizes breakpoints with active debug sessions via commands.debugSetBreakpoints, commands.debugBreakpoints, commands.debugSetFunctionBreakpoints, commands.debugSetDataBreakpoints, commands.debugExceptionFilters, and commands.debugSetExceptionBreakpoints.

## Verification
- Run: `pnpm test`
