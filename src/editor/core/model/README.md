# model

## Overview
Stateful models for open documents (DocModel, DocumentManager), pure tab/split layout transitions (TabsState), per-tab editor slots (EditorSlot), navigation/edit history, and session persistence.

## Interface
- [document.ts:DocumentManager]: Opens, caches, reconfigures, saves, and releases DocModel instances across tabs and attached views.
- [document.ts:DocModel]: Per-file document model owning DocSync, ModifiedRanges, language support, outline/folding refresh, and save cleanup.
- [document.ts:useDocMeta]: Zustand store mapping file paths to reactive DocMeta (dirty, encoding, lineEnding, readOnly, banner, indent).
- [document.ts:useOutline]: Zustand store mapping file paths to OutlineItem trees for breadcrumbs and structure navigation.
- [tabs.ts:TabsState]: Pure state model and transitions (openTab, closeTab, activateTab, splitPane, moveTab, enforceTabLimit, reopenClosed) for editor tabs and split panes.
- [slot.ts:EditorSlot]: Per-tab wrapper holding EditorState in background tabs and mounting/unmounting a live EditorView when visible.
- [attachment.ts:attachDocument]: Attaches a non-tab CodeMirror view (such as an editable diff side) to a shared DocModel.

## Invariants
- [document.ts:DocModel]: All split panes and attached views of the same path share one DocModel and DocSync instance; local edits mirror across views with mirroredChange and addToHistory:false.
- [document.ts:saveCleanupChanges]: Save cleanup never strips trailing whitespace on lines currently containing a visible caret when keepTrailingOnCaretLine is enabled.
- [tabs.ts:openTab]: At most one preview tab exists per pane; pinned tabs stay at the front of the tab strip and survive Close Others and tab-limit eviction.
- [slot.ts:EditorSlot]: An EditorView and TextEditorHandle exist only while the slot is mounted; unmounting snapshots scroll/topLine and retains EditorState.

## Side Effects
- [document.ts:DocumentManager]: Calls backend docOpen/docOpenVirtual/docSave/docClose/folding/outline/highlights IPC and emits EditorEvents via _emitEditorEvent.
- [session.ts:storeSession]: Persists and restores per-project tab/split/caret sessions in localStorage.

## Verification
- Run: `pnpm test`
