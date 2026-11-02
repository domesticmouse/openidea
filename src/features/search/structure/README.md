# structure

## Overview
Structure tool window (⌘7) and File Structure popup (⌘F12) displaying the active editor's symbol outline with sorting, visibility/field filters, autoscroll to/from source, and inherited supertype members via LSP type hierarchy.

## Interface
- [StructureView.tsx:StructureView] [StructureView.tsx:useFileStructurePopup] [StructureView.tsx:FileStructureOverlay] Structure tool window component and File Structure popup dialog.
- [store.ts:useOutline] [store.ts:inheritedDeps] [store.ts:useLiveOutline] [store.ts:useStructureOptions] Reference-counted live outline loader (IDLE_MS = 500 after edits) and persisted view options (alphabetical, autoscrollFromSource, autoscrollToSource, narrowDown, showFields, showNonPublic, sortByVisibility, showInherited).
- [outline.ts:OutlineNode] [outline.ts:OutlineViewFilter] [outline.ts:OutlineFilter] [outline.ts:keyOutline] [outline.ts:withInherited] [outline.ts:visibilityRank] [outline.ts:sortOutline] [outline.ts:applyViewFilter] [outline.ts:hasVisibility] [outline.ts:hasFields] [outline.ts:symbolPathAt] [outline.ts:outlinePathAt] [outline.ts:allKeys] [outline.ts:filterOutline] [outline.ts:parentKeys] [outline.ts:visibleCount] Pure outline tree keying, visibility/field filtering, speed-search filtering, and caret containment lookup.
- [inherited.ts:InheritedMember] [inherited.ts:InheritedDeps] [inherited.ts:typeKey] [inherited.ts:typesIn] [inherited.ts:inheritedMembers] Breadth-first supertype traversal (up to depth = 6) collecting non-overridden inherited members.

## Invariants
- [store.ts:useLiveOutline] Starts editor/edit subscriptions when consumers transitions 0 -> 1 and disposes them when consumers returns to 0.
- [inherited.ts:inheritedMembers] Excludes constructors and nested types, deduplicates visited supertypes by path:line, and hides supertype members whose base name is already declared by the subtype or a nearer supertype.

## Side Effects
- [store.ts:load] [store.ts:withDoc] [store.ts:supertypesOf] Invokes commands.docOutline, commands.lspTypeHierarchy, commands.docOpen / commands.docOpenVirtual, and commands.docClose.
- [store.ts:useStructureOptions] Persists structure view options in localStorage under openidea.structure.options.v1.

## Verification
- Run: `pnpm test`
