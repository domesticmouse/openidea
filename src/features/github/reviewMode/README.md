# reviewMode

## Overview
In-editor Pull Request Review Mode displaying review change markers, commentable gutter ranges, thread/draft inlays, and top-right Review Mode controls directly inside regular file editors when the checked-out branch tracks an open pull request.

## Interface
- [index.ts:registerReviewMode] — Registers the `github.reviewMode` editor contribution, tracks the current branch's PR and active review editor, and registers review mode actions (`github.reviewEditor.addComment`, `github.reviewEditor.addMultilineComment`, `github.reviewEditor.prevComment`, `github.reviewEditor.nextComment`, `github.reviewEditor.refresh`, `github.reviewEditor.showDiff`, `github.reviewMode.toggle`, `github.reviewMode.updateBranch`).
- [editor.tsx:ReviewEditor], [editor.tsx:activeReviewEditor], [editor.tsx:activeReviewFile], [editor.tsx:trackActiveReviewEditor], [editor.tsx:reviewModeEditorExtension] — Per-editor ViewPlugin syncing PR head revisions against local document edits via `vcsDiffHunks` and projecting review threads, new-comment inlays, and gutter change markers onto document lines.
- [Toolbar.tsx:ReviewModeToolbar] — Top-right editor corner widget providing Previous/Next Comment buttons and the Review Mode configuration menu.
- [state.ts:ReviewBranchState], [state.ts:useReviewBranch], [state.ts:refreshReviewBranch], [state.ts:trackReviewBranch], [state.ts:updateReviewBranch] — Tracks the open pull request associated with the checked-out Git branch and whether local history is in sync with `headRefOid`.
- [model.ts:TrackedBranch], [model.ts:Range], [model.ts:ChangeKind], [model.ts:DocumentChange], [model.ts:EditorThreadsOption], [model.ts:trackedBranch], [model.ts:openPullRequestQuery], [model.ts:isUpdateRequired], [model.ts:patchChanges], [model.ts:patchRanges], [model.ts:hunksToRanges], [model.ts:lineToAfter], [model.ts:lineFromAfter], [model.ts:shiftRanges], [model.ts:documentChanges], [model.ts:editorThreadRange] — Pure branch tracking, range shifting (`ExcludingApproximateChangedRangesShifter`), and thread line-mapping algorithms.
- [settings.ts:ReviewModeSettings], [settings.ts:useReviewModeSettings], [settings.ts:setReviewModeEnabled], [settings.ts:setEditorThreadsOption], [settings.ts:toggleReviewMode] — Persistent Review Mode settings store.

## Invariants
- [editor.tsx:ReviewEditor] — Shows in-editor review inlays and gutter markers only when `settings.enabled && !branch.updateRequired` and the PR head text and local diff ranges have been computed.
- [model.ts:editorThreadRange] — Places only current (`!isOutdated`), line-anchored (`subjectType !== "FILE"`), right-side (`diffSide === "RIGHT"`) threads in the regular editor.
- [model.ts:shiftRanges] — Excludes review change ranges that have been subsequently modified by local post-review edits.

## Side Effects
- [editor.tsx:ReviewEditor], [state.ts:refreshReviewBranch], [state.ts:updateReviewBranch] — Queries Git config, remotes, merge-base, file contents at `headRefOid`, and diff hunks via IPC (`vcsConfigGet`, `vcsRemotes`, `vcsMergeBase`, `vcsFileAt`, `vcsDiffHunks`), pulls branch updates via `pullCurrent()`, mounts `ReviewModeToolbar` into `editorCorner`, and updates `githubReviewEditor` and `githubReviewBranch` context keys.
- [settings.ts:useReviewModeSettings] — Persists review mode settings in `localStorage` (`openidea.github.reviewMode.v1`).

## Verification
- Run: `pnpm test`
