# popup

## Overview
Caret-anchored popup chooser (ChooserHost, showChooser) supporting prefix/fuzzy filtering, section headers, and underlined single-key mnemonics for ⌘J Insert Live Template, ⌥⌘T Surround With, and multi-expression Postfix Completion.

## Interface
- [store.ts:ChooserItem] [store.ts:ChooserRequest] [store.ts:useChooser] [store.ts:showChooser] [store.ts:closeChooser] Zustand store for the active caret-anchored chooser request.
- [Chooser.tsx:ChooserHost] [Chooser.tsx:filterChooserItems] Popup chooser component and prefix/fuzzy item filter.

## Invariants
- [store.ts:showChooser] Opening a new chooser invokes prev.onCancel?.() on any active request and increments seq.
- [Chooser.tsx:filterChooserItems] Excludes section header rows (item.section) when a non-empty filter is active, ordering prefix matches before fuzzy matches.

## Side Effects
- [store.ts:showChooser] [store.ts:closeChooser] Mutates useChooser Zustand store state.

## Verification
- Run: `pnpm test`
