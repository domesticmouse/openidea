# scratch

## Overview
Scratch Files (⇧⌘N) stored in <appData>/scratches, registered as a Scratches root in the Project view, with a language chooser popup prioritizing recently used languages.

## Interface
- [index.ts:SCRATCHES_ROOT_LABEL] [index.ts:newScratchFile] [index.ts:activate] Registers the scratches Project view root, scratch.chooser overlay (ScratchChooserHost), scratch.new menu group item, file.newScratchFile action, and forwards scratch deletions to fsChanged.
- [ScratchChooser.tsx:ScratchChooserHost] Filterable language chooser popup overlay.
- [scratches.ts:ScratchBackend] [scratches.ts:tauriScratchBackend] [scratches.ts:setScratchBackend] [scratches.ts:scratchBackend] [scratches.ts:scratchesDir] [scratches.ts:scratchesAvailable] [scratches.ts:createScratch] Backend abstraction and <appData>/scratches file creation (scratch[_n].<ext>).
- [store.ts:useScratch] [store.ts:openChooser] [store.ts:closeChooser] [store.ts:rememberLanguage] Chooser Zustand store and recent language persistence.
- [model.ts:ScratchLanguage] [model.ts:scratchFileName] [model.ts:scratchLanguages] [model.ts:LanguageRow] [model.ts:chooserRows] [model.ts:MAX_RECENT] [model.ts:pushRecent] Pure naming (scratch.<ext>, scratch_1.<ext>…) and fuzzy/prefix language filtering.

## Invariants
- [model.ts:scratchFileName] Picks the first case-insensitively unused name in the sequence scratch.<ext>, scratch_1.<ext>, scratch_2.<ext>, etc.
- [model.ts:pushRecent] Deduplicates recent language IDs and caps the list at MAX_RECENT (5).

## Side Effects
- [scratches.ts:scratchesDir] [scratches.ts:createScratch] [index.ts:activate] Creates <appData>/scratches and scratch files via commands.fsCreateDir / commands.fsCreateFile, opens them via editorService().openFile, and emits local fsChanged events on creation and deletion.
- [store.ts:rememberLanguage] Persists recent languages to localStorage (openidea.scratch.recentLanguages).

## Verification
- Run: `pnpm test`
