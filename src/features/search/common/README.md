# common

## Overview
Shared search utilities for opening editor locations, parsing file:line:col queries, fuzzy/word-aligned text ranking, streaming backend text searches, and tracking active editor carets, edit versions, and recent locations.

## Interface
- [open.ts:Position] [open.ts:openLocation] [open.ts:projectRoot] [open.ts:relPath] [open.ts:relDir] [open.ts:isTypeKind] [open.ts:symbolDisplayName] [open.ts:loadJson] [open.ts:saveJson] [open.ts:openInListOrder] Editor file/selection opener, path/symbol formatters, and localStorage JSON helpers.
- [tracking.ts:CaretInfo] [tracking.ts:useTracking] [tracking.ts:dismissLocation] [tracking.ts:resetTracking] [tracking.ts:installTracking] CodeMirror editor contribution (search.tracking) and Zustand store tracking active caret, per-file editVersions, session edited files, and RecentLocation history.
- [locations.ts:RecentLocation] [locations.ts:DismissedLocation] [locations.ts:MAX_LOCATIONS] [locations.ts:MERGE_DISTANCE] [locations.ts:recordLocation] [locations.ts:mergeLocations] [locations.ts:isDismissed] [locations.ts:renameLocations] [locations.ts:fromNavigationHistory] Pure recent-location merging, deduplication within MERGE_DISTANCE (5 lines), and cap (MAX_LOCATIONS = 60).
- [rank.ts:LocalMatch] [rank.ts:MatchOptions] [rank.ts:matchText] [rank.ts:isWordAligned] [rank.ts:byScore] Multi-word fuzzy and word-aligned text matcher and stable descending score sorter.
- [query.ts:ParsedQuery] [query.ts:parseLocationQuery] [query.ts:selectionOf] Parses path:line:col, path(line,col), and file:// URLs into 0-based coordinates.
- [textSearch.ts:streamSearch] [textSearch.ts:cancelRequest] Streams commands.searchStart results over a Tauri Channel<SearchEvent>.
- [services.ts:maybeToolWindowService] Safe accessor for toolWindowService().

## Invariants
- [locations.ts:recordLocation] Caret moves within MERGE_DISTANCE (5 lines) in the same file merge into a single entry, preserving changed: true, and cap the list at MAX_LOCATIONS (60).
- [rank.ts:isWordAligned] When wordAligned is enabled, every non-contiguous matched character run after index 0 must follow a word separator (/[\s_\-./\\:]/) or camelCase boundary.
- [query.ts:parseLocationQuery] Converts 1-based line and column suffixes into 0-based coordinates and strips the project root prefix when present.

## Side Effects
- [tracking.ts:installTracking] Registers editor contribution search.tracking, subscribes to docPatched and fsChanged IDE events, and invokes commands.markRecent(path) on activeEditorChanged.
- [textSearch.ts:streamSearch] [textSearch.ts:cancelRequest] Invokes commands.searchStart and commands.cancelRequest.

## Verification
- Run: `pnpm test`
