# todo

## Overview
Project-wide and scoped TODO index (search_start), in-editor TODO comment highlighter (todoEditorExtension), TODO tool window (Project, Current File, Scope Based, Changes tabs with source preview), and Settings › Editor › TODO page.

## Interface
- [index.ts:activateSettingsPages] [index.ts:activate] Registers the editor.todo settings page, todo tool window, editor highlight extension, and background TODO index.
- [model.ts:TodoPattern] [model.ts:TodoSettings] [model.ts:DEFAULT_PATTERNS] [model.ts:DEFAULT_TODO_SETTINGS] [model.ts:TodoItem] [model.ts:TodoGroup] [model.ts:parseTodoSettings] [model.ts:activePatterns] [model.ts:todoRegexSource] [model.ts:buildTodoQuery] [model.ts:looksLikeComment] [model.ts:itemsFromMatches] [model.ts:scanText] [model.ts:TodoFilter] [model.ts:filterItems] [model.ts:groupTodos] [model.ts:countItems] [model.ts:mergeLive] [model.ts:TodoScope] [model.ts:BUILTIN_TODO_SCOPES] [model.ts:todoSearchScope] [model.ts:changedPaths] TODO pattern matching, comment filtering, grouping, and scope resolution.
- [store.ts:useTodoSettings] [store.ts:updateTodoSettings] [store.ts:syncTodoSettingsFromStorage] [store.ts:useTodos] [store.ts:setLiveTodos] [store.ts:scanProject] [store.ts:rescanFiles] [store.ts:scanScope] [store.ts:queueRefresh] [store.ts:installTodoIndex] [ipc.ts:cancelRequestSafe] [ipc.ts:previewText] Streaming TODO search store and incremental file rescan queue.
- [highlight.ts:todoDecorations] [highlight.ts:todoEditorExtension] [TodoView.tsx:useTodoView] [TodoView.tsx:useTodoViewOptions] [TodoView.tsx:useTodoHeaderMenu] [TodoView.tsx:useTodoGroups] [TodoView.tsx:useTodoBadge] [TodoView.tsx:TodoHeaderActions] [TodoView.tsx:TodoTabs] [TodoView.tsx:TodoView] [TodoSettingsPage.tsx:TodoSettingsPage] CodeMirror 6 comment decoration extension, TODO tool window UI, and settings page.

## Invariants
- [highlight.ts:todoDecorations] [highlight.ts:todoEditorExtension] Editor decorations only apply inside syntax tree nodes matching /comment/i, and live full-text buffer scanning is skipped for virtual documents or documents exceeding LIVE_SCAN_LIMIT (2,000,000 chars).
- [store.ts:queueRefresh] [store.ts:rescanFiles] Incremental updates exceeding MAX_INCREMENTAL (200 paths) fall back to a full scanProject(), and incremental rescans defer while a full project scan is active (projectSlot.id !== null).

## Side Effects
- [store.ts:run] [ipc.ts:previewText] Starts streaming search channels via IPC commands.searchStart, cancels active searches via commands.cancelRequest, and reads preview text via commands.fsReadText.
- [store.ts:updateTodoSettings] [TodoView.tsx:useTodoView] [TodoView.tsx:useTodoViewOptions] Persists settings and view state in localStorage (openidea.todo.v1, openidea.todo.scope.v1, openidea.todo.view.v1).

## Verification
- Run: `pnpm test`
