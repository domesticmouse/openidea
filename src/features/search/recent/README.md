# recent

## Overview
Recent Files (⌘E), Recently Edited Files (⌘E again), Switcher (⌃⇥ / ⌃⇧⇥), and Recent Locations (⇧⌘E) popups with speed search, tool window column navigation, and syntax-highlighted code snippets.

## Interface
- [RecentPopups.tsx:RecentPopupsOverlay] Renders RecentFilesDialog (handling both Recent Files and Switcher) or RecentLocationsDialog.
- [store.ts:RecentPopupKind] [store.ts:useRecentPopup] [store.ts:showRecentPopup] [store.ts:cycleSwitcher] [store.ts:hideRecentPopup] Zustand store managing popup visibility, editedOnly/changedOnly toggles on re-invocation, and Switcher step cycling.
- [model.ts:RecentFileItem] [model.ts:CodeSegment] [model.ts:filterRecentFiles] [model.ts:initialFileIndex] [model.ts:switcherPaths] [model.ts:switcherItems] [model.ts:switcherSelection] [model.ts:switcherToolWindows] [model.ts:filterLocations] [model.ts:locationTime] [model.ts:locationBreadcrumb] [model.ts:lineSegments] Pure filtering, Switcher wrap-around across files and tool windows, relative timestamp formatting, and token-to-line segmentation.

## Invariants
- [model.ts:initialFileIndex] Preselects index 1 (the previously active file) when query is empty and items[0].path === activePath, enabling ⌘E ↵ to toggle between the two most recent files.
- [model.ts:switcherSelection] Wraps (base + step) mod (files + tools) across both columns so stepping past the end of open files enters the tool windows column and vice versa.

## Side Effects
- [RecentPopups.tsx:RecentFilesDialog] [RecentPopups.tsx:useOpenOutlines] Calls commands.recentFiles(), commands.removeRecent(path), and commands.docOutline(doc), closes tabs/hides tool windows on Delete, and opens selected files/tool windows on activation or Control key release (in Switcher mode).

## Verification
- Run: `pnpm test`
