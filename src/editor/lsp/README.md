# lsp

## Overview
LSP-backed editor intelligence contributed through @/editor/api: completion, inline completion, diagnostics, hover/quick docs, signature help, navigation, intentions, in-place rename, formatting, inlay hints, code lenses, semantic highlighting, and spellcheck.

## Interface
- [index.ts:activate]: Registers LSP CodeMirror contributions, gutter line markers, overlays, status items, Documentation tool window, save participants, and LSP actions.
- [index.ts:lspExtensions]: Returns the full suite of LSP CodeMirror extensions for an EditorDocContext.
- [lsp.ts:lspDoc]: Facet attaching EditorDocContext to an EditorState.
- [lsp.ts:lspRequest]: Runs a cancellable backend LSP request tied to a RequestId.
- [positions.ts:offsetToPos]: Converts between CodeMirror UTF-16 document offsets and 0-based LSP Pos/Range coordinates (offsetToPos, posToOffset, rangeToOffsets, offsetsToRange).
- [popupStore.ts:openPopup]: Opens list, preview, or documentation popups anchored to the editor caret.
- [hint.ts:showHint]: Displays a transient editor hint tooltip at the caret.
- [format.ts:formatDocument]: Runs contributed or LSP document/range formatting after awaiting document sync.
- [intentions.ts:applyCodeAction]: Applies an LSP CodeAction or WorkspaceEdit and opens affected files.

## Invariants
- [index.ts:activate]: Never imports from @/editor/core/*; all editor integration goes through @/editor/api.
- [format.ts:formatDocument]: All position-dependent or edit-producing LSP requests must await whenSynced(view) (or whenAllSynced()) before invoking backend commands.

## Side Effects
- [index.ts:activate]: Subscribes to backend IdeEvent::Diagnostics and LSP availability events, invokes commands.lsp* IPC methods, and registers save participants for format-on-save and actions-on-save.

## Verification
- Run: `pnpm test`
