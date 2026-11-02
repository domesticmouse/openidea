# macros

## Overview
Records, coalesces, persists, edits, and replays user action and editor keystroke macros (`Edit › Macros`), exposing saved macros as keybindable workbench actions.

## Interface
- [index.ts:activate], [index.ts:playMacro] — Registers macro actions (`macro.startRecording`, `macro.stopRecording`, `macro.playBack`, `macro.playSaved`, `macro.edit`), dynamic `macros.play.<name>` actions, CodeMirror `macros.recorder` extension, `macros.dialogs` overlay, and `macros.recording` status bar widget.
- [model.ts:STORAGE_KEY], [model.ts:MacroStep], [model.ts:Macro], [model.ts:MacrosState], [model.ts:useMacros], [model.ts:startRecording], [model.ts:stopRecording], [model.ts:recordStep], [model.ts:storableArg], [model.ts:saveMacro], [model.ts:renameMacro], [model.ts:deleteMacro], [model.ts:describeStep], [model.ts:playSteps], [model.ts:_reloadMacros] — Zustand store, step coalescing, `localStorage` persistence, and sequential macro playback.
- [editorKeys.ts:EDITOR_KEY_COMMANDS], [editorKeys.ts:editorKeyName] — Maps editor keyboard events (`Enter`, `Backspace`, `Delete`, `Tab`, arrows, `Home`, `End`) to CodeMirror commands.
- [MacrosDialog.tsx:MacroStatus], [MacrosDialog.tsx:MacrosDialogHost], [MacrosDialog.tsx:showEditMacros] — Status bar recording indicator and Edit Macros modal dialog.

## Invariants
- [model.ts:recordStep] — Ignores steps when `!recording` or `playing`, and coalesces consecutive `kind: "text"` steps into a single step.
- [index.ts:activate] — Excludes `macro.*` and `macros.*` actions and non-primitive action arguments (`storableArg(arg) === null`) from recording.
- [model.ts:playSteps] — Prevents re-entrant playback via `playing`, halts on the first step returning `false`, and yields a macrotask (`setTimeout(r, 0)`) between steps.
- [model.ts:renameMacro] — Rejects empty names or names colliding with an existing macro.

## Side Effects
- [model.ts:stopRecording], [model.ts:saveMacro], [model.ts:renameMacro], [model.ts:deleteMacro] — Reads and writes `localStorage` under `STORAGE_KEY` (`"openidea.macros.v1"`).
- [index.ts:activate], [index.ts:playMacro] — Updates context keys (`macroRecording`, `macroLastAvailable`, `macrosSaved`), dynamically registers `macros.play.<name>` actions, and dispatches text/command transactions to the active CodeMirror `EditorView`.

## Verification
- Run: `pnpm test`
