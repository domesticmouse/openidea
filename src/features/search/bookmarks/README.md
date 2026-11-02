# bookmarks

## Overview
Line, file, and folder bookmarks organized into named bookmark lists with optional unique mnemonics (0–9, A–Z), CodeMirror gutter markers, Bookmarks tool window, and mnemonic/list popups.

## Interface
- [BookmarksView.tsx:groupBookmarks] [BookmarksView.tsx:buildBookmarkTree] [BookmarksView.tsx:BookmarksView] [BookmarksView.tsx:BookmarksActions] Bookmarks tool window tree view, grouping helpers, and header action buttons.
- [BookmarkPopups.tsx:BookmarkPopupsOverlay] [BookmarkPopups.tsx:bookmarkRows] Overlay rendering the Mnemonic Chooser (⌥F3), Bookmarks Popup (⌘F3), Edit Description dialog, and List Name dialog.
- [gutter.ts:bookmarkGutter] CodeMirror 6 extension rendering line bookmark and mnemonic markers in .cm-openidea-bookmarks, handling ⌘-click toggles, and syncing line shifts after document edits.
- [store.ts:Bookmark] [store.ts:PathBookmark] [store.ts:BookmarkList] [store.ts:BookmarkData] [store.ts:BookmarkRef] [store.ts:MNEMONICS] [store.ts:DEFAULT_LIST_ID] [store.ts:isMnemonic] [store.ts:sortBookmarks] [store.ts:hasBookmark] [store.ts:bookmarkAt] [store.ts:toggleBookmark] [store.ts:assignMnemonic] [store.ts:toggleMnemonic] [store.ts:bookmarkByMnemonic] [store.ts:describeBookmark] [store.ts:bookmarkLabel] [store.ts:removeBookmarks] [store.ts:setFileLines] [store.ts:renameBookmarks] [store.ts:linesOf] [store.ts:marksOf] [store.ts:pathBookmarkAt] [store.ts:togglePathBookmarks] [store.ts:validateListName] [store.ts:createList] [store.ts:renameList] [store.ts:setDefaultList] [store.ts:deleteList] [store.ts:moveBookmarks] [store.ts:renamePathBookmarks] [store.ts:useBookmarks] [store.ts:loadBookmarks] [store.ts:updateBookmarkData] [store.ts:updateBookmarks] [store.ts:MnemonicTarget] [store.ts:useBookmarkPopups] Bookmark state stores and pure list/mnemonic manipulation functions.

## Invariants
- [store.ts:assignMnemonic] Each mnemonic (0–9, A–Z) is unique across a project's bookmarks; assigning an already-used mnemonic strips it from its previous bookmark.
- [store.ts:deleteList] Deleting the last remaining bookmark list recreates a fallback default list (id: "default") and ensures defaultList always references an existing list ID.
- [store.ts:setFileLines] Collapses duplicate line numbers when document edits merge bookmarked lines onto the same line.

## Side Effects
- [store.ts:loadBookmarks] [store.ts:updateBookmarkData] Reads and writes per-project bookmarks and lists in localStorage (openidea.bookmarks.v1:<scope> and openidea.bookmarkLists.v1:<scope>).
- [gutter.ts:bookmarkGutter] Registers capture-phase mousedown listeners on the CodeMirror DOM and writes moved line numbers back to useBookmarks via microtask.

## Verification
- Run: `pnpm test`
