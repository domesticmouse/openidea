# search

## Overview
Top-level search, navigation, bookmarks, recent files/locations, and structure feature entry point wiring tool windows, overlays, editor gutter contributions, and keyboard actions across its 6 subpackages (./bookmarks/README.md, ./common/README.md, ./everywhere/README.md, ./find/README.md, ./recent/README.md, ./structure/README.md).

## Interface
- [index.ts:activate] Installs editor tracking, registers find, bookmarks, and structure tool windows, search.bookmarks editor gutter, provideFindResultsWindow, overlays (search.everywhere, search.findInFiles, search.scopes, search.bookmarks, search.fileStructure, search.recent), project/FS listeners, and all search.*, navigate.*, find.*, and edit.findUsages actions.
- [bookmarks](./bookmarks/README.md) - Line, file, and folder bookmarks organized into named bookmark lists with optional unique mnemonics (0–9, A–Z), CodeMirror gutter markers, Bookmarks tool window, and mnemonic/list popups.
- [common](./common/README.md) - Shared search utilities for opening editor locations, parsing file:line:col queries, fuzzy/word-aligned text ranking, streaming backend text searches, and tracking active editor carets, edit versions, and recent locations.
- [everywhere](./everywhere/README.md) - Unified Search Everywhere popup (Double Shift, ⌘O, ⇧⌘O, ⌥⌘O, ⇧⌘A) searching across All, Classes, Files, Symbols, Actions (including settings), and Text with streaming sections, / scope commands, per-tab funnel filters, and code preview.
- [find](./find/README.md) - Find and Replace in Files (⇧⌘F / ⇧⌘R) modeless popup, Scopes editor dialog, and multi-tab Find tool window with streaming matches, usage exclusion (⌫), inline/diff replacement preview, and batch backend replacement.
- [recent](./recent/README.md) - Recent Files (⌘E), Recently Edited Files (⌘E again), Switcher (⌃⇥ / ⌃⇧⇥), and Recent Locations (⇧⌘E) popups with speed search, tool window column navigation, and syntax-highlighted code snippets.
- [structure](./structure/README.md) - Structure tool window (⌘7) and File Structure popup (⌘F12) displaying the active editor's symbol outline with sorting, visibility/field filters, autoscroll to/from source, and inherited supertype members via LSP type hierarchy.

## Invariants
- [index.ts:activate] Mnemonic bookmark actions are registered for every character in MNEMONICS (0–9, A–Z), and digit mnemonics (0–9) additionally bind Ctrl+Shift+Digit<m> (toggle) and Ctrl+Digit<m> (jump).
- [index.ts:activate] On fsChanged, both line bookmarks (renameBookmarks) and path bookmarks (renamePathBookmarks) are updated for renamed/removed paths.

## Side Effects
- [index.ts:activate] Registers workbench tool windows, overlays, editor contributions, and actions; subscribes to useProject and onIdeEvent("fsChanged"); invokes commands.lspReferences for edit.findUsages.

## Verification
- Run: `pnpm test`
