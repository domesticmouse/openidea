# navigation

## Overview
Editor structural and hierarchy navigation actions: Go to Test / Test Subject, Super Method, Next/Previous Method, and implementation/override gutter markers.

## Interface
- [index.ts:registerNavigationActions]: Registers navigate.gotoTest, navigate.superMethod, navigate.nextMethod, and navigate.previousMethod actions.
- [implementationGutter.ts:implementationGutter]: CodeMirror extension rendering gutter icons for overridden/implemented methods and classes.
- [gotoTest.ts:gotoTestOrSubject]: Resolves matching test files or test subjects (or offers creating a test / Rust #[cfg(test)] module).
- [structureNav.ts:superMethods]: Traverses incoming type hierarchy and document outlines to locate overridden super methods.
- [structureNav.ts:methodStop]: Computes the target Pos for Next/Previous Method navigation from an OutlineItem tree.

## Invariants
- [index.ts:caretOutline]: Must await whenSynced(view) before querying commands.docOutline(doc) so caret offsets match the backend buffer.
- [index.ts:withDoc]: Temporary backend documents opened to inspect unopened files must always be closed in a finally block via commands.docClose.

## Side Effects
- [index.ts:registerNavigationActions]: Invokes commands.docOutline, commands.lspTypeHierarchy, commands.lspGoto, and commands.findFiles over IPC, and dispatches editor selections or templates.newFromTemplate.

## Verification
- Run: `pnpm test`
