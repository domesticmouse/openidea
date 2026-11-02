# editor

## Overview
Editor subsystem root defining the public CodeMirror 6 extension contract (api.ts) and housing core, lsp, navigation, refactor, and structure packages.

## Interface
- [api.ts:registerEditorContribution]: Registers per-editor CodeMirror extensions (and reconfigures live editors via Compartments).
- [api.ts:activeEditor]: Returns the focused (or last focused) TextEditorHandle, and editorsFor(path) returns all live handles for a file.
- [api.ts:whenSynced]: Awaits backend acknowledgment of all pending local edits for one EditorView (or whenAllSynced() for all open editors).
- [api.ts:isExternalChange]: Returns true if a transaction carries remoteChange or mirroredChange annotations.
- [api.ts:scrollbarMarks]: Facets and helpers for overview stripe marks (scrollbarMarks), VCS gutter bars (gutterLaneMarkers, LaneMarker), codeLenses, intentionProviders, quickFixProviders, and registerSaveParticipant.
- [api.ts:attachDocument]: Attaches a non-tab CodeMirror view to an open backend document.
- [core](./core/README.md) - Core text editor package orchestrating EditorController, DocumentManager, CodeMirror setup, editor actions, color scheme runtime, and workbench EditorService registration.
- [lsp](./lsp/README.md) - LSP-backed editor intelligence contributed through @/editor/api: completion, inline completion, diagnostics, hover/quick docs, signature help, navigation, intentions, in-place rename, formatting, inlay hints, code lenses, semantic highlighting, and spellcheck.
- [navigation](./navigation/README.md) - Editor structural and hierarchy navigation actions: Go to Test / Test Subject, Super Method, Next/Previous Method, and implementation/override gutter markers.
- [refactor](./refactor/README.md) - Refactor This popup, LSP code-action refactoring classification, syntax-fallback Introduce Variable/Constant, and Safe Delete with usage verification.
- [structure](./structure/README.md) - Tree-sitter syntactic structure client ('dumb mode' fallback) providing AST node, expression, local symbol, identifier, and structural range queries when LSP is unavailable or slow.

## Invariants
- [api.ts:registerEditorContribution]: Features outside src/editor/core must never import from @/editor/core/*; all editor extensions and queries go through @/editor/api.
- [api.ts:isExternalChange]: Editor update listeners must ignore transactions where isExternalChange(tr) is true so remote patches and split-pane mirrors do not trigger typing side effects.

## Side Effects
- [api.ts:useEditorRegistry]: Maintains global Zustand state of registered EditorContributions and live TextEditorHandles, reconfiguring embedded views on contribution changes.

## Verification
- Run: `pnpm test`
