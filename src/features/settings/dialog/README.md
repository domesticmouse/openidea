# dialog

## Overview
Settings modal dialog and shared SettingsLayout/SettingsFooter (page tree, search filter, breadcrumb header, Global/Project scope switch, back/forward history, and per-page error boundary).

## Interface
- [LazySettingsDialog.tsx:loadBuiltinPages] [LazySettingsDialog.tsx:unloadBuiltinPages] [LazySettingsDialog.tsx:LazySettingsDialog] Lazy loader for built-in settings pages and dialog wrapper.
- [SettingsDialog.tsx:SettingsDialog] [SettingsDialog.tsx:useSettingsTitle] [SettingsDialog.tsx:SettingsLayout] [SettingsDialog.tsx:SettingsFooter] Modal dialog shell, split navigation/page layout, and Reset/Cancel/Apply/OK footer.
- [PageErrorBoundary.tsx:PageErrorBoundary] Error boundary isolating crashes in individual settings pages.

## Invariants
- [SettingsDialog.tsx:PageHost] Disables the "project" scope switch when no project is open or when editing "Settings for New Projects" (newProjects === true).
- [SettingsDialog.tsx:SettingsLayout] Pressing Escape in the search field clears a non-empty query before allowing the dialog to close, and ArrowUp/ArrowDown navigate the page tree while keeping focus in the search input.
- [PageErrorBoundary.tsx:PageErrorBoundary] Resets the caught error state whenever props.pageId changes so a failing page does not break navigation to other pages.

## Side Effects
- [LazySettingsDialog.tsx:loadBuiltinPages] Lazily imports ../pages/registry and registers built-in pages once.
- [SettingsDialog.tsx:useSettingsButtons] Invokes applySettings() and closeSettings() on OK/Apply/Cancel.

## Verification
- Run: `pnpm test`
