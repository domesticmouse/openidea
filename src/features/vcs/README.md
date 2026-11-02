# vcs

## Overview
Git version control, changelists, shelf, stash, interactive rebase, branch management, side-by-side/unified diff viewer, 3-way merge conflict resolver, gutter change markers, blame annotations, and Local History.

## Interface
- [index.ts:activateSettingsPages] [index.ts:activate] Registers VCS stores, Commit and Git tool windows, title bar BranchWidget, DiffEditor, MergeEditor, LocalHistoryEditor, editor gutter/blame extensions, and VCS actions.
- [store.ts:RepoHead] [store.ts:VcsState] [store.ts:useVcs] [store.ts:currentRepo] [store.ts:currentRepoRoot] [store.ts:repoOf] [store.ts:repoName] [store.ts:attentionRepo] [store.ts:selectRepo] [store.ts:focusRepo] [store.ts:conflictedFiles] [store.ts:onConflictsAppeared] [store.ts:onConflictsCleared] [store.ts:scheduleRefresh] [store.ts:refreshNow] [store.ts:applyStatus] [store.ts:loadBranches] [store.ts:loadStashes] [store.ts:statusOf] [store.ts:installVcsStore] Multi-root repository state store, status polling/event sync, and conflict transition callbacks.
- [commitStore.ts:CommitTab] [store.ts:useVcs] [CommitView.tsx:CommitToolWindow] [CommitDialog.tsx:showCommitDialog] [commitChecks.tsx:runCommitChecks] [changes.ts:buildChangeTree] [changes.ts:planCommit] [changelists.ts:useChangelists] [changelists.ts:installChangelists] [shelf.tsx:useShelf] [shelf.tsx:shelveChanges] [shelf.tsx:unshelve] Non-modal Commit tool window and modal Commit dialog, pre-commit checks (diagnostics, TODOs), changelists, partial hunk inclusion, and Shelf.
- [logStore.ts:useGitLog] [logStore.ts:installGitLog] [GitLog.tsx:GitToolWindow] [graph.ts:computeGraph] [BranchWidget.tsx:BranchWidget] [branchOps.ts:checkoutBranch] [branchOps.ts:mergeIntoCurrent] [branchOps.ts:rebaseOnto] [remoteUi.tsx:showPushDialog] [remoteUi.tsx:showUpdateDialog] [rebase.tsx:interactivelyRebaseFrom] [patches.tsx:showApplyPatch] Git Log graph/table, branch widget and multi-repo branch operations, Push/Pull/Update dialogs, interactive rebase, and patches.
- [DiffEditor.tsx:DiffEditor] [diffInput.ts:DIFF_KIND] [diffInput.ts:openDiff] [MergeEditor.tsx:MergeEditor] [mergeKind.ts:MERGE_KIND] [conflicts.ts:parseConflicts] [conflictsUi.tsx:showConflictsDialog] [LocalHistory.tsx:LocalHistoryEditor] [localHistoryKind.ts:LOCAL_HISTORY_KIND] [DirectoryCompare.tsx:showDirectoryComparison] [gutter.ts:changeMarkers] [blame.ts:toggleAnnotations] Diff, 3-way Merge, Directory Compare, and Local History editors, plus editor gutter hunk markers and inline blame.
- [comparison](./comparison/README.md) - Two-level line, word, and character diff engine (ported from IntelliJ ComparisonManagerImpl) supporting DEFAULT, TRIM_WHITESPACES, and IGNORE_WHITESPACES policies, block splitting, and chunk optimization.

## Invariants
- [index.ts:trackCommitWindow] Registers the left-docked Commit tool window only when workflowSettings().vcs.commit.nonModal is true; when switched to modal mode, Commit… opens a modal dialog and Local Changes/Shelf/Stash move to the Git tool window.
- [changes.ts:planCommit] [hunkInclusion.ts:toggleBlock] Separates whole-file staged/unstaged selections from partial-hunk commits (PartialCommitFile) and groups selections per repository root.
- [conflicts.ts:parseConflicts] Parses 7-char <<<<<<< / ||||||| / ======= / >>>>>>> conflict markers and computes non-overlapping two-side changes for one-click Resolve Non-Conflicting Changes.

## Side Effects
- [store.ts:refreshNow] [ops.ts:runOp] [ops.ts:runRemote] [ops.ts:runGitOperation] Invokes backend VCS/Git and Local History IPC commands (vcsStatus, vcsCommit, vcsPush, vcsPull, vcsFetch, vcsLog, vcsBranches, vcsMerge, vcsRebase, vcsStash, vcsShelf*, localHistory*, etc.).
- [changelists.ts:updateChangelists] [diffOptions.ts:saveDiffOptions] [logOptions.ts:setLogOptions] Persists changelists, diff viewer options, and Git Log options to localStorage and project settings.

## Verification
- Run: `pnpm test`
