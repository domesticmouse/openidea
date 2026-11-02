# debugger

## Overview
Debug Adapter Protocol (DAP) frontend orchestrating debug session lifecycles, launch and attach configurations (Java/Kotlin, Python, JS/TS/Deno, Go, Rust/C/C++, .NET, Godot), JVM HotSwap class reloading, and Services tool window integration.

## Interface
- [index.ts:activate]
- [state.ts:SessionStatus]
- [state.ts:ConsoleCategory]
- [state.ts:ConsoleLine]
- [state.ts:DebugSession]
- [state.ts:StartSessionOptions]
- [state.ts:useDebug]
- [state.ts:useExecutionPoint]
- [state.ts:activeSession]
- [state.ts:isLive]
- [state.ts:sessionSupports]
- [state.ts:publishContext]
- [state.ts:startSession]
- [state.ts:stopSession]
- [state.ts:rerunSession]
- [state.ts:removeSession]
- [state.ts:selectSession]
- [state.ts:selectThread]
- [state.ts:selectFrame]
- [state.ts:revealFrame]
- [state.ts:loadThreads]
- [state.ts:loadFrames]
- [state.ts:resumeExecution]
- [state.ts:appendConsole]
- [state.ts:clearConsole]
- [state.ts:addWatch]
- [state.ts:removeWatch]
- [state.ts:loadWatches]
- [state.ts:setVariableValue]
- [state.ts:evaluate]
- [state.ts:handleDebugEvent]
- [state.ts:resetSessions]
- [state.ts:sessionsRunning]
- [actions.ts:debuggerActions]
- [actions.ts:startDebugging]
- [actions.ts:debugRunConfiguration]
- [actions.ts:runToLine]
- [actions.ts:smartStepInto]
- [actions.ts:quickEvaluate]
- [launch.ts:debugUnsupportedReason]
- [launch.ts:godotScene]
- [launch.ts:launchConfigFor]
- [java.ts:JAVA_ADAPTER]
- [java.ts:KOTLIN_ADAPTER]
- [java.ts:JavaAttachTarget]
- [java.ts:isJavaDebuggable]
- [java.ts:moduleHasKotlinSources]
- [java.ts:javaLaunchConfig]
- [java.ts:isJavaAttachTarget]
- [java.ts:javaAttachConfig]
- [attach.ts:KIND_LABELS]
- [attach.ts:AttachRow]
- [attach.ts:matchesFilter]
- [attach.ts:attachRows]
- [attach.ts:attachDetail]
- [attach.ts:attachConfig]
- [remote.ts:remoteDebugLaunch]
- [hotswap.ts:redefineJavaClasses]
- [hotswap.ts:reloadChangedClasses]
- [hotswap.ts:onJavaFileSaved]
- [missingAdapter.ts:MissingAdapter]
- [missingAdapter.ts:missingAdapter]
- [frames.ts:FrameItem]
- [frames.ts:isLibraryFrame]
- [frames.ts:groupFrames]
- [frames.ts:frameLabel]
- [values.ts:ValueKind]
- [values.ts:classifyValue]
- [values.ts:inlineValue]
- [values.ts:identifiersIn]
- [values.ts:sortVariables]
- [breakpoints](./breakpoints/README.md) - Breakpoint state management and models covering line breakpoints, method (function) breakpoints, field watchpoints (data breakpoints), exception breakpoint filters, language placement guards, and smart declaration detection.
- [editor](./editor/README.md) - CodeMirror 6 editor extensions for the debugger providing breakpoint gutter markers and interactions, execution-point line highlighting and inline variable values, hover/selection value tooltips, Quick Evaluate mouse shortcuts, inlay Run to Cursor buttons, and inline Smart Step Into target picking.
- [ui](./ui/README.md) - Debugger user interface components providing the Debug tool window (Threads & Variables and Console tabs), lazy Variables tree with inline value editing and change highlighting, interactive Debug Console REPL, and debugger dialogs/popovers (Breakpoint properties, View Breakpoints, Evaluate Expression, Attach to Process, Smart Step chooser).

## Invariants
- [state.ts:ownSession] — Multi-window isolation: ignores DebugEvent notifications for sessions not in the window's owned set unless event.kind === 'started' and its parent is already owned by this window.
- [state.ts:appendConsole] — Caps per-session console history at MAX_CONSOLE (5000) lines and batches streamed output on animation frames.
- [state.ts:onBreakpointStop] — Automatically removes temporary breakpoints on first hit, evaluates logParts templates, and resumes execution automatically when all hit breakpoints have suspend === false.
- [hotswap.ts:redefineJavaClasses] — Prevents concurrent HotSwap redefinitions via redefining guard and only targets live java-debug or kotlin-debug-adapter sessions.
- [java.ts:moduleHasKotlinSources] — Does not follow directory symlinks while scanning src/ for .kt files so cyclic symlinks cannot cause infinite traversal.

## Side Effects
- [index.ts:activate], [state.ts:startSession], [state.ts:stopSession] — Listens to IdeEvent (debug, fileSaved) and invokes commands.debugStart, commands.debugStop, commands.debugDisconnect, commands.debugTerminate, commands.debugPause, commands.debugContinue, commands.debugStep, commands.debugStepInTargets, commands.debugStepInTarget, commands.debugGotoTargets, commands.debugGoto, commands.debugRestartFrame, commands.debugThreads, commands.debugStackTrace, commands.debugScopes, commands.debugVariables, commands.debugSetVariable, commands.debugEvaluateIn, and commands.debugAttachableProcesses.
- [state.ts:persistWatches] — Persists watch expressions in localStorage['openidea.debugger.watches'].
- [java.ts:debugServer], [hotswap.ts:redefineJavaClasses] — Starts the JDT LS debug server via commands.javaDebugServer and hot-reloads compiled classes via commands.javaBuildWorkspace and commands.debugRedefineClasses.

## Verification
- Run: `pnpm test`
