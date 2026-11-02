# emmet

## Overview
Emmet abbreviation expansion (Tab) in markup and stylesheet contexts and "Update Tag with Emmet" enclosing-tag editor and prompt dialog.

## Interface
- [model.ts:EmmetSyntax] [model.ts:syntaxAt] [model.ts:EmmetExpansion] [model.ts:toCodeMirrorSnippet] [model.ts:expandAbbreviation] [model.ts:expandAt] [model.ts:TagAttr] [model.ts:TagRange] [model.ts:enclosingTag] [model.ts:TextEdit] [model.ts:updateTagEdits] Emmet syntax detection, abbreviation expansion, enclosing HTML/JSX tag parser, and tag-update edit computation.
- [editor.ts:expandEmmetAtCaret] [editor.ts:tagAtCaret] [editor.ts:updateTag] CodeMirror 6 commands for Emmet Tab expansion and tag updating.
- [UpdateTagDialog.tsx:promptAbbreviation] [UpdateTagDialog.tsx:UpdateTagDialogHost] Modal prompt for Update Tag with Emmet.

## Invariants
- [model.ts:expandAt] Rejects single plain words in markup (so Live Templates or indentation can handle Tab) unless forced, and caps backward context scanning at CONTEXT_LIMIT (200,000 chars).
- [model.ts:updateTagEdits] Rejects multi-element abbreviations ([>+^*{] outside attribute brackets), merges new class tokens without duplicates, and renames both opening and closing tags when a leading tag name is given.

## Side Effects
- [editor.ts:expandEmmetAtCaret] [editor.ts:updateTag] Dispatches CodeMirror snippet/tag edits into EditorView.
- [UpdateTagDialog.tsx:promptAbbreviation] Resolves any previous pending prompt with null and opens UpdateTagDialogHost.

## Verification
- Run: `pnpm test`
