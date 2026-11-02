# keymap

## Overview
Keymap settings page, pure keymap override/conflict/preset model with when-clause overlap analysis, keystroke/mouse shortcut recorder dialog, and Search Everywhere "Assign Shortcut" overlay.

## Interface
- [model.ts:KeymapDraft] [model.ts:presetBindings] [model.ts:resolveBindings] [model.ts:isModified] [model.ts:withBindings] [model.ts:addBinding] [model.ts:removeBinding] [model.ts:resetBindings] [model.ts:switchPreset] [model.ts:whensOverlap] [model.ts:Conflict] [model.ts:findConflicts] [model.ts:removeConflicts] [model.ts:bindingMatches] [model.ts:CustomKeymap] [model.ts:CustomKeymaps] [model.ts:activeDraft] [model.ts:keymapName] [model.ts:copyName] [model.ts:validateKeymapName] [model.ts:duplicateKeymap] [model.ts:renameKeymap] [model.ts:deleteKeymap] [model.ts:withCustomOverrides] Pure keymap resolution, conflict detection, and custom keymap scheme operations.
- [groups.ts:MAIN_MENU] [groups.ts:EDITOR_ACTIONS] [groups.ts:TOOL_WINDOWS] [groups.ts:OTHER] [groups.ts:PLUGINS] [groups.ts:ROOT_ORDER] [groups.ts:MAIN_MENU_ORDER] [groups.ts:groupPath] [groups.ts:groupRank] [groups.ts:groupIcon] Action category hierarchy and ordering for the Keymap tree.
- [KeymapPage.tsx:keymapSearchLabels] [KeymapPage.tsx:buildKeymapTree] [KeymapPage.tsx:KeymapPage] Keymap settings page and action tree builder.
- [RecordShortcutDialog.tsx:RecordShortcutDialogProps] [RecordShortcutDialog.tsx:RecordShortcutDialog] [StrokeRecorder.tsx:StrokeRecorderProps] [StrokeRecorder.tsx:StrokeRecorder] [KeymapDialogs.tsx:KeymapNameDialog] Keyboard/mouse shortcut recorder modal, two-stroke chord input, and keymap rename/duplicate dialog.
- [AssignShortcut.tsx:useAssignShortcut] [AssignShortcut.tsx:showAssignShortcut] [AssignShortcut.tsx:draftOf] [AssignShortcut.tsx:assignedSetting] [AssignShortcut.tsx:AssignShortcutOverlay] Standalone shortcut assignment overlay invoked from Search Everywhere / Find Action.

## Invariants
- [model.ts:withBindings] [model.ts:switchPreset] Drops an action's override entry whenever its normalized bindings equal the preset's defaults.
- [model.ts:whensOverlap] [model.ts:findConflicts] Detects "same" and "prefix" chord conflicts only when two actions' when clauses can simultaneously hold under mutually exclusive focus locations (none, editor, toolWindow, view keys), conservatively returning true when combinations exceed MAX_COMBINATIONS (4096).
- [StrokeRecorder.tsx:StrokeRecorder] [RecordShortcutDialog.tsx:MouseRecorder] Suspends global keyboard shortcut dispatch (suspendKeyboardDispatch()) while focused or open so recorded keys do not trigger IDE actions.

## Side Effects
- [AssignShortcut.tsx:AssignShortcutOverlay] Persists shortcut assignments directly to "global" settings (keymapOverrides or customKeymaps) via commands.settingsSet.

## Verification
- Run: `pnpm test`
