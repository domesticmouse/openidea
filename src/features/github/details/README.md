# details

## Overview
Pull request `#N` details tab (`DetailsPanel`), including branch and commit pickers, file changes tree with viewed state, CI/conflict/reviewer status checks, review flow actions, and the Submit Review popup.

## Interface
- [DetailsPanel.tsx:DetailsPanel] — Main `#N` PR details panel providing the `"submitReview"` handler and rendering metadata, commits, changes tree, status section, and review actions.
- [BranchWidget.tsx:BranchWidget] — Head branch chip menu with Checkout, Open in Worktree, Show in Git Log, and Copy Branch Name actions.
- [ChangesTree.tsx:ChangesTree] — Directory-grouped or flat changed-files tree with viewed checkboxes, comment badges, and `"toggleViewed"` handler registration.
- [CommitPicker.tsx:CommitPicker], [CommitPicker.tsx:CommitInfo] — Commit filter dropdown (`All commits (N)` or individual commit) and commit message summary block.
- [ReviewActions.tsx:ReviewActions] — Role-aware footer action bar (Request/Re-request Review, Submit Review, Set Myself as Reviewer, Merge/Squash/Rebase split button, Close/Reopen/Post Pull Request).
- [StatusSection.tsx:StatusSection] — Status checks list showing CI pipeline summary, conflict resolution trigger, required approvals, and reviewer status rows.
- [SubmitReviewPopup.tsx:showSubmitReview] — Popup dialog to Approve, Request Changes, Comment, or discard a pending review.
- [flow.ts:useFlowBusy], [flow.ts:isFlowBusy], [flow.ts:runFlow], [flow.ts:adjustReviewers], [flow.ts:reRequestReview], [flow.ts:removeReviewer], [flow.ts:setMyselfAsReviewer], [flow.ts:merge], [flow.ts:showInGitLog], [flow.ts:copyBranchName], [flow.ts:resolveConflictsLocally], [flow.ts:isInCurrentHistory], [flow.ts:openCommitFileDiff] — Serialized review-flow actions and local Git helpers.
- [model.ts:ReviewRequestState], [model.ts:ReviewRole], [model.ts:OpenedAction], [model.ts:MergeDisabledReason], [model.ts:CiJobItem], [model.ts:CiSummary], [model.ts:ResolveConflictsState], [model.ts:ChangesTreeNode], [model.ts:ChangesProgress], [model.ts:reviewRequestState], [model.ts:reviewRole], [model.ts:overallReviewState], [model.ts:openedActions], [model.ts:mergeButtonMethods], [model.ts:mergeDisabledReason], [model.ts:ciSummary], [model.ts:resolveConflicts], [model.ts:isHeadCheckedOut], [model.ts:fileStatusKind], [model.ts:fileCommentsCount], [model.ts:fileViewed], [model.ts:changesTree], [model.ts:flattenChangesTree], [model.ts:progressOf] — Pure details-panel domain and presentation logic.
- [state.ts:useCommitFilesOf], [state.ts:clearCommitFiles], [state.ts:useChangesGrouping], [state.ts:setGroupByDirectory], [state.ts:useReviewText], [state.ts:setReviewText] — Per-commit file cache, directory grouping preference, and draft review comment state.

## Invariants
- [flow.ts:runFlow] — Prevents concurrent review-flow operations on the same pull request via `isFlowBusy(number)`.
- [SubmitReviewPopup.tsx:showSubmitReview] — Hides Approve and Request Changes buttons when `viewerDidAuthor` is true, and blocks dismissal on outside click while `busy`.
- [ChangesTree.tsx:ChangesTree] — Enables viewed state checkboxes and `"toggleViewed"` only when viewing all commits (`commitSha === null`).

## Side Effects
- [flow.ts:merge], [flow.ts:resolveConflictsLocally], [flow.ts:openCommitFileDiff] — Runs GitHub PR mutations (`operations.ts`), checks out and merges the base branch locally via `mergeIntoCurrent`, opens commit diffs via `vcs.openDiff`, and persists `groupByDirectory` in `localStorage` (`openidea.github.changesGrouping.v1`).

## Verification
- Run: `pnpm test`
