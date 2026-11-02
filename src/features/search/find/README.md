# find

## Overview
Find and Replace in Files (⇧⌘F / ⇧⌘R) modeless popup, Scopes editor dialog, and multi-tab Find tool window with streaming matches, usage exclusion (⌫), inline/diff replacement preview, and batch backend replacement.

## Interface
- [FindInFiles.tsx:FindInFilesOverlay] Modeless Find/Replace in Files popup with file mask, search context filter, Project/Module/Directory/Scope tabs, match list, and code/diff preview.
- [FindToolWindow.tsx:buildFindTree] [FindToolWindow.tsx:nodeGroups] [FindToolWindow.tsx:FindTabs] [FindToolWindow.tsx:FindToolWindow] [FindToolWindow.tsx:FindToolWindowActions] Find tool window tree view, tab bar, occurrence navigator, and Exclude/Replace toolbar.
- [ScopesDialog.tsx:useScopesDialog] [ScopesDialog.tsx:showScopesDialog] [ScopesDialog.tsx:ScopesDialogOverlay] [ScopesDialog.tsx:scopesProblem] Custom file-pattern Scopes editor dialog.
- [store.ts:ScopeTab] [store.ts:BuiltinScope] [store.ts:NamedScope] [store.ts:FindOptions] [store.ts:FindPopupCommand] [store.ts:useFindPopup] [store.ts:setFindPopupPinned] [store.ts:setFindPopupCommandHandler] [store.ts:runFindPopupCommand] [store.ts:parseFindArg] [store.ts:showFindPopup] [store.ts:hideFindPopup] [store.ts:recentMasks] [store.ts:rememberMask] [store.ts:buildQuery] [store.ts:resolveDirectory] [store.ts:resolveFindScope] [store.ts:describeQuery] Popup state store, scope resolution, and file mask persistence.
- [windowStore.ts:FindTab] [windowStore.ts:useFindWindow] [windowStore.ts:ipcFindDeps] [windowStore.ts:openInFindWindow] [windowStore.ts:openEditorResults] [windowStore.ts:pinFindTab] [windowStore.ts:rerunActiveFindTab] [windowStore.ts:rerunFindTab] [windowStore.ts:stopFindTab] [windowStore.ts:closeFindTab] [windowStore.ts:disposeFindWindow] [windowStore.ts:useFindViewOptions] [windowStore.ts:useFindHeaderMenu] Tool window tab store (capped at MAX_TABS = 8), session lifecycle, and persisted view options.
- [session.ts:FindStatus] [session.ts:FindState] [session.ts:FindDeps] [session.ts:IDLE] [session.ts:FindSession] [session.ts:MatchRow] [session.ts:countFileMatches] [session.ts:matchRows] [session.ts:previewReplacement] [session.ts:rangesToIndices] Streaming search state machine (idle -> validating -> searching -> done/invalid/error).
- [replaceFlow.ts:Usage] [replaceFlow.ts:UsageGroup] [replaceFlow.ts:ReplaceMarks] [replaceFlow.ts:NO_MARKS] [replaceFlow.ts:pendingUsages] [replaceFlow.ts:replaceTargets] [replaceFlow.ts:countUsages] [replaceFlow.ts:confirmText] [replaceFlow.ts:toggleExcluded] [replaceFlow.ts:markDone] [replaceFlow.ts:groupState] [replaceFlow.ts:nextPending] [replaceFlow.ts:startReplace] [replaceFlow.ts:loadReplacePreview] [replaceFlow.ts:summaryText] [replaceFlow.ts:replacePreviewLines] Usage exclusion/completion tracking, editor sync barrier, and cancellable backend replacement.
- [parts.tsx:MatchText] [parts.tsx:PreviewHighlight] [parts.tsx:previewDecorations] [parts.tsx:CodePreview] [parts.tsx:ReplaceDiffPreview] [preview.ts:loadPreviewText] [preview.ts:clearPreviewCache] [rowSelection.ts:selectedRowIndices] [rowSelection.ts:selectRow] Read-only CodeMirror preview pane, inline match/replacement renderer, unified diff preview, and multi-row range selection.

## Invariants
- [session.ts:FindSession] Starting or invalidating a search increments seq, cancels any in-flight backend request, and drops late events from superseded searches.
- [windowStore.ts:openInFindWindow] Reuses the active tab unless it is pinned, and evicts the oldest tab when tabs.length > MAX_TABS (8).
- [replaceFlow.ts:startReplace] Waits for all open editors of target files to flush local edits (whenSynced) before invoking commands.replaceInFiles.

## Side Effects
- [windowStore.ts:ipcFindDeps] [replaceFlow.ts:ipcReplaceDeps] [replaceFlow.ts:loadReplacePreview] Invokes commands.searchValidate, commands.searchStart, commands.replaceInFiles, commands.replacePreview, and commands.cancelRequest.
- [store.ts:setFindPopupPinned] [store.ts:rememberMask] [windowStore.ts:useFindViewOptions] [ScopesDialog.tsx:ScopesDialog] Persists popup pin state, recent file masks, tool window view options, and custom scopes in localStorage.

## Verification
- Run: `pnpm test`
