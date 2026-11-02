# analysis

## Overview
Batch code analysis features including Inspect Code (batch language-server diagnostics), Code Cleanup, Locate Duplicates, and Structural Search & Replace (SSR).

## Interface
- [index.ts:activate]
- [AnalysisDialogs.tsx:showInspectCodeDialog]
- [AnalysisDialogs.tsx:showDuplicatesDialog]
- [AnalysisDialogs.tsx:showCodeCleanupDialog]
- [AnalysisDialogs.tsx:showStructuralDialog]
- [AnalysisDialogs.tsx:showReplacePreview]
- [dialogs.tsx:openDialog]
- [dialogs.tsx:AnalysisDialogHost]
- [dialogs.tsx:ScopeFields]
- [inspect.ts:useInspection]
- [inspect.ts:runInspection]
- [inspect.ts:rerunInspection]
- [inspect.ts:cancelInspection]
- [inspect.ts:resetInspection]
- [inspect.ts:groupInspection]
- [inspect.ts:toggleExcludedProblems]
- [inspect.ts:installInspectionProfileSync]
- [inspect.ts:problemId]
- [inspect.ts:kindOf]
- [inspect.ts:severityKey]
- [InspectionView.tsx:InspectionView]
- [InspectionView.tsx:InspectionHeaderActions]
- [InspectionView.tsx:useInspectionHeaderMenu]
- [InspectionView.tsx:useInspectionOptions]
- [InspectionView.tsx:buildInspectionNodes]
- [duplicates.ts:useDuplicates]
- [duplicates.ts:locateDuplicates]
- [duplicates.ts:rerunDuplicates]
- [duplicates.ts:cancelDuplicates]
- [duplicates.ts:resetDuplicates]
- [duplicates.ts:loadDuplicateOptions]
- [duplicates.ts:fragmentLines]
- [duplicates.ts:DEFAULT_DUPLICATE_OPTIONS]
- [DuplicatesView.tsx:DuplicatesView]
- [DuplicatesView.tsx:DuplicatesHeaderActions]
- [cleanup.ts:CleanupOptions]
- [cleanup.ts:runCodeCleanup]
- [cleanup.ts:cleanupFiles]
- [cleanup.ts:walkFiles]
- [structural.ts:StructuralForm]
- [structural.ts:loadStructuralForm]
- [structural.ts:saveStructuralForm]
- [structural.ts:searchStructurally]
- [structural.ts:showStructuralResults]
- [structural.ts:buildReplaceEdit]
- [structural.ts:applyStructuralReplace]
- [structural.ts:cancelStructural]
- [structural.ts:matchKey]
- [structural.ts:matchLocations]
- [scope.ts:ScopeKind]
- [scope.ts:ScopeChoice]
- [scope.ts:SCOPE_LABELS]
- [scope.ts:defaultScope]
- [scope.ts:resolveScope]
- [scope.ts:describeScope]
- [scope.ts:currentFile]
- [scope.ts:changedFiles]

## Invariants
- [AnalysisDialogs.tsx:DuplicatesDialog] — Requires minTokens to be an integer between 10 and 10000 before starting duplicate detection.
- [cleanup.ts:runCodeCleanup] — Prevents concurrent cleanup runs (if (!isTauri() || running) return), prompts for confirmation when cleaning more than CONFIRM_ABOVE (100) files, and closes any editor tabs that were not open before the cleanup started.
- [cleanup.ts:walkFiles] — Skips ignored, excluded, hidden, and symlink entries (e.ignored || e.excluded || e.hidden || e.kind === 'symlink') and caps traversal at limit (5000) files.
- [inspect.ts:installInspectionProfileSync] — Preserves unfiltered rawFiles diagnostics in useInspection so inspection profile changes re-project severities and enabled states without re-running language servers.

## Side Effects
- [inspect.ts:runInspection] — Streams batch inspection diagnostics via Tauri Channel<InspectEvent> and commands.inspectCode.
- [duplicates.ts:locateDuplicates] — Invokes commands.analysisLocateDuplicates and persists duplicate detection options under localStorage['openidea.analysis.duplicates.v1'].
- [structural.ts:searchStructurally], [structural.ts:applyStructuralReplace] — Streams structural search matches via Channel<StructuralEvent> (commands.structuralSearch), validates templates via commands.structuralValidate, applies replacements via commands.applyWorkspaceEdit, and persists form state under localStorage['openidea.analysis.structural.v1'].
- [cleanup.ts:runCodeCleanup] — Executes 'code.cleanupFile' and editors.saveAll() across scoped files while reporting background task progress via upsertTask.
- [InspectionView.tsx:useInspectionOptions] — Persists inspection view options (groupBySeverity, autoscroll) under localStorage['openidea.inspection.view.v1'].

## Verification
- Run: `pnpm test`
