# code-insight

## Overview
Code insight views and language-server navigation features providing the Problems tool window, Find Usages tool window and Show Usages popup (with syntax/text fallback in dumb mode), Type/Call Hierarchy tool window, language server status widget, Go build constraints status widget, and project tree problem decorations.

## Interface
- [index.ts:activate]
- [problemsStore.ts:useProblems]
- [problemsStore.ts:ProblemEntry]
- [problemsStore.ts:FileProblemSummary]
- [problemsStore.ts:fileProblems]
- [problemsStore.ts:allFileProblems]
- [problemsStore.ts:allProblems]
- [problemsStore.ts:totalProblemCounts]
- [problemsStore.ts:fileProblemSummaries]
- [problemsStore.ts:navigateProblem]
- [ProblemsView.tsx:ProblemsView]
- [ProblemsView.tsx:ProblemsTabBadge]
- [ProblemsView.tsx:ProblemsHeaderActions]
- [decorations.ts:installProblemDecorations]
- [decorations.ts:computeProblemDecorations]
- [usages.ts:Usage]
- [usages.ts:UsageCategory]
- [usages.ts:UsagesQuery]
- [usages.ts:useUsages]
- [usages.ts:findUsages]
- [usages.ts:showUsages]
- [usages.ts:rerunUsages]
- [usages.ts:cancelUsages]
- [usages.ts:navigateUsage]
- [usages.ts:removeUsage]
- [usages.ts:classifyUsages]
- [UsagesView.tsx:UsagesView]
- [UsagesView.tsx:UsagesHeaderActions]
- [UsagesPopup.tsx:UsagesPopup]
- [UsageAccessFilter.tsx:UsageAccessFilter]
- [UsageAccessFilter.tsx:useUsageAccessOptions]
- [UsageAccessFilter.tsx:toggleUsageAccess]
- [UsageAccessFilter.tsx:filterUsageAccess]
- [UsageAccessFilter.tsx:supportsUsageAccess]
- [UsageAccessFilter.tsx:loadAccessOptions]
- [syntaxUsages.ts:syntaxUsages]
- [syntaxUsages.ts:SyntaxUsages]
- [syntaxUsages.ts:LOCAL_NOTE]
- [syntaxUsages.ts:TEXT_NOTE]
- [candidates.ts:filesMentioning]
- [candidates.ts:CandidateFiles]
- [hierarchy.ts:HierarchyKind]
- [hierarchy.ts:TypeViewMode]
- [hierarchy.ts:CallViewMode]
- [hierarchy.ts:HierarchyRoot]
- [hierarchy.ts:useHierarchy]
- [hierarchy.ts:openTypeHierarchy]
- [hierarchy.ts:openCallHierarchy]
- [hierarchy.ts:setTypeMode]
- [hierarchy.ts:setCallMode]
- [hierarchy.ts:refreshHierarchy]
- [hierarchy.ts:focusSelectedItem]
- [hierarchy.ts:loadTypeChildren]
- [hierarchy.ts:loadCallChildren]
- [HierarchyView.tsx:HierarchyView]
- [HierarchyView.tsx:HierarchyHeaderActions]
- [servers.ts:useServers]
- [servers.ts:ServerEntry]
- [servers.ts:restartServers]
- [servers.ts:reloadProject]
- [servers.ts:openLogs]
- [ServerStatus.tsx:ServerStatus]
- [goConstraints.ts:GoBuildConstraintsStatus]
- [goConstraints.ts:useGoTarget]
- [goConstraints.ts:parseBuildTags]
- [ipc.ts:lspRequest]
- [ipc.ts:CaretContext]
- [ipc.ts:caretContext]
- [ipc.ts:caretPoint]
- [ipc.ts:readFileLines]
- [ipc.ts:previewText]
- [ipc.ts:readManyFileLines]
- [ipc.ts:withDocument]
- [ipc.ts:openLocation]
- [ipc.ts:hint]
- [ipc.ts:reportError]
- [ipc.ts:isUnavailable]

## Invariants
- [problemsStore.ts:effective] — Filters and re-projects raw LSP diagnostics through the active useInspectionProfile (except 'sql-inspections' producer diagnostics, which bypass profile filtering).
- [usages.ts:findUsagesAt] — Automatically falls back to [syntaxUsages.ts:syntaxUsages] (Lezer local-scope references or project-wide identifier search) when the language server is unavailable.
- [UsageAccessFilter.tsx:toggleUsageAccess], [UsageAccessFilter.tsx:filterUsageAccess] — Prevents both read and write filters from being disabled simultaneously, and never hides usages with 'unknown' or 'readWrite' access.
- [ipc.ts:withDocument] — Opens non-editor files with commands.docOpen only for the duration of the callback and always releases the reference with commands.docClose in finally.

## Side Effects
- [index.ts:activate], [servers.ts:useServers] — Subscribes to IdeEvent (diagnostics, serverStatus, serverLog, fileSaved, fsChanged), syncs open editor buffers on disk changes, and manages language servers via commands.lspRunningServers, commands.lspRestart, and commands.lspReloadProject.
- [decorations.ts:installProblemDecorations] — Debounces diagnostic changes by 60 ms and publishes project tree underline decorations (error / warning) up ancestor directories via setFileDecorations('problems', ...).
- [usages.ts:queryUsages], [hierarchy.ts:openHierarchy] — Queries LSP references/definitions/implementations and call/type hierarchies via commands.lspReferences, commands.lspGoto, commands.lspPrepareTypeHierarchy, commands.lspSupertypes, commands.lspSubtypes, commands.lspPrepareCallHierarchy, commands.lspIncomingCalls, and commands.lspOutgoingCalls.
- [goConstraints.ts:applyBuildConstraints] — Updates 'languages.go.goos', 'languages.go.goarch', and 'languages.go.buildTags' project settings via commands.settingsSet and reloads Go workspace state.

## Verification
- Run: `pnpm test`
