# core

## Overview
Core text editor package orchestrating EditorController, DocumentManager, CodeMirror setup, editor actions, color scheme runtime, and workbench EditorService registration.

## Interface
- [index.ts:activate]: Activates the core editor, registers EditorService, EditorAreaView, document attacher, code preview provider, status bar items, overlays, and editor actions.
- [controller.ts:EditorController]: Singleton controller coordinating DocumentManager, TabsState, EditorSlots, NavigationHistory, EditHistory, autosave, and context keys.
- [controller.ts:editorController]: Returns the active EditorController singleton.
- [controller.ts:useTabs]: Zustand store holding the current TabsState across all editor panes.
- [controller.ts:useEditorStatus]: Zustand store holding caret/selection status for the active text editor.
- [actions.ts:registerEditorActions]: Registers Code, Edit, Folding, View, Find, and Navigate editor actions.
- [colorSchemeRuntime.ts:installColorScheme]: Injects and updates the live #openidea-editor-color-scheme stylesheet and font-weight CSS variables.
- [clipboardHistory.ts:installClipboardTracking]: Tracks copy/cut events up to MAX_CLIPBOARD_ENTRIES (30) for Paste from History.
- [cm](./cm/README.md) - Base CodeMirror 6 extensions, compartments, language mode loading, syntax highlighting, code folding, find/replace state, multi-caret/column selection, and smart editing commands.
- [model](./model/README.md) - Stateful models for open documents (DocModel, DocumentManager), pure tab/split layout transitions (TabsState), per-tab editor slots (EditorSlot), navigation/edit history, and session persistence.
- [sync](./sync/README.md) - Single-server operational transformation (OT) client synchronizing CodeMirror document changes with the Rust backend rope buffer.
- [view](./view/README.md) - React view components for the editor area, split panes, tab strips, in-file Find/Replace bar, breadcrumbs, status bar widgets, document banners, and Go to Line dialog.

## Invariants
- [index.ts:activate]: editor/core never imports from src/features/*; features interact exclusively through @/editor/api and @/workbench/contributions.
- [controller.ts:EditorController]: Project transitions must serialize with close-session guards and never autosave or prompt for discarded custom documents after the project root changes.
- [actions.ts:queueSelection]: Async Extend/Shrink Selection and structural edits are queued per EditorSlot so rapid repeated keystrokes execute sequentially.

## Side Effects
- [controller.ts:EditorController]: Subscribes to useProject, useTabs, window blur/pagehide/beforeunload, and backend filesystem/document events; writes session state to localStorage.
- [colorSchemeRuntime.ts:installColorScheme]: Appends <style id='openidea-editor-color-scheme'> to document.head and observes root theme attributes via MutationObserver.

## Verification
- Run: `pnpm test`
