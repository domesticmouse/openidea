# everywhere

## Overview
Unified Search Everywhere popup (Double Shift, ⌘O, ⇧⌘O, ⌥⌘O, ⇧⌘A) searching across All, Classes, Files, Symbols, Actions (including settings), and Text with streaming sections, / scope commands, per-tab funnel filters, and code preview.

## Interface
- [SearchEverywhere.tsx:SearchEverywhereOverlay] Modal Search Everywhere dialog with tab bar, scope selector, funnel filter popup, result list, and optional CodePreview.
- [store.ts:useSearchEverywhere] [store.ts:setSearchEverywherePreview] [store.ts:setSearchEverywhereFilter] [store.ts:showSearchEverywhere] [store.ts:setIncludeNonProject] [store.ts:selectSearchEverywhereTab] [store.ts:widenSearchEverywhere] [store.ts:onSearchEverywhereQuery] [store.ts:hideSearchEverywhere] [store.ts:recentActions] [store.ts:pushRecentAction] [store.ts:searchHistory] [store.ts:pushSearchHistory] Zustand state store, auto-widening scope logic, and persisted search/action history.
- [controller.ts:SeDeps] [controller.ts:SeRequest] [controller.ts:SeSnapshot] [controller.ts:TEXT_MIN_QUERY] [controller.ts:sectionLimit] [controller.ts:SeController] Streaming multi-source search controller with per-section signature caching and request cancellation.
- [model.ts:SeTab] [model.ts:SectionId] [model.ts:TABS] [model.ts:SECTION_ORDER] [model.ts:SECTION_TITLES] [model.ts:sectionsFor] [model.ts:SeItem] [model.ts:SeRow] [model.ts:SectionState] [model.ts:fileItem] [model.ts:symbolItem] [model.ts:mergeSymbols] [model.ts:matchActions] [model.ts:matchSettings] [model.ts:mergeActionResults] [model.ts:actionItem] [model.ts:textItems] [model.ts:itemWeight] [model.ts:mixItems] [model.ts:buildRows] [model.ts:isSectionRow] [model.ts:groupJump] [model.ts:parseCommand] [model.ts:topResultSettled] [model.ts:stableIndex] Result ranking, LSP/index symbol deduplication, All-tab relevance mixing, and /command parser.
- [filters.ts:SeFilters] [filters.ts:FilterTab] [filters.ts:FILTER_TABS] [filters.ts:isFilterTab] [filters.ts:DEFAULT_FILTERS] [filters.ts:sanitizeFilters] [filters.ts:filterActive] [filters.ts:kindsFor] [filters.ts:extensionIndex] [filters.ts:languageOf] [filters.ts:resolveFilter] [filters.ts:passesFilter] [FilterPopup.tsx:FilterPopup] Per-tab language, symbol kind, and file mask filters.
- [deps.ts:ipcDeps] [deps.ts:loadLanguages] Backend IPC bindings for SeController.

## Invariants
- [controller.ts:SeController] Sections whose request signature (query, tab, includeNonProject, fileSet, filter, includeDisabled, limit) has not changed are not re-fetched; text search requires at least TEXT_MIN_QUERY (2) characters.
- [model.ts:topResultSettled] Pressing Enter waits until all higher-priority sections (or non-text sections outranking TEXT_WEIGHT) finish loading so a stale row from a previous query is never activated.
- [store.ts:widenSearchEverywhere] [store.ts:onSearchEverywhereQuery] Automatically widens includeNonProject when a project search returns no hits while autoEverywhere is true, and narrows back when the query no longer contains notFound.

## Side Effects
- [deps.ts:ipcDeps] [deps.ts:loadLanguages] Invokes commands.findFiles, commands.findSymbols, commands.lspWorkspaceSymbols, commands.recentFiles, commands.languages, and streamSearch.
- [store.ts:setSearchEverywherePreview] [store.ts:setSearchEverywhereFilter] [store.ts:pushRecentAction] [store.ts:pushSearchHistory] Persists preview toggle, filters, recent actions (MAX_RECENT_ACTIONS = 12), and per-tab search history (MAX_HISTORY = 50) in localStorage.

## Verification
- Run: `pnpm test`
