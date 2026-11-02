# refactor

## Overview
Refactor This popup, LSP code-action refactoring classification, syntax-fallback Introduce Variable/Constant, and Safe Delete with usage verification.

## Interface
- [index.ts:refactorThis]: Queries applicable LSP and syntax refactorings at the caret and opens the numbered Refactor This popup.
- [index.ts:runRefactoring]: Executes a specific RefactoringId (Move, Change Signature, Extract Variable/Constant/Method, Inline) or displays a reason why it cannot run.
- [index.ts:safeDelete]: Resolves the declaration at the caret, checks usages via LSP or tree-sitter locals, prompts on conflicts, and deletes the declaration.
- [index.ts:fileUsages]: Searches external references to top-level symbols of files before file deletion.
- [index.ts:askUsagesDetected]: Prompts the user with View Usages / Delete Anyway / Cancel when Safe Delete finds usages.
- [catalog.ts:REFACTORINGS]: Canonical catalog mapping LSP CodeAction kinds/titles to OpenIDEA RefactoringIds.

## Invariants
- [refactorThis.ts:refactorThis]: Must await whenSynced(view) before querying refactor actions, and abort/discard results if the document or selection changes before the popup opens.
- [safeDelete.ts:applyDeletion]: Refuses to apply deletion if view.state.doc changed while awaiting user confirmation or usage search, or if the declaration shares a line with other code.

## Side Effects
- [refactorThis.ts:runRefactoring]: Invokes LSP code-action/prepareRename/references and syntax structure IPC commands, opens confirmation dialogs, and dispatches workspace or editor transactions.

## Verification
- Run: `pnpm test`
