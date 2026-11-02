# diff

## Overview
Pull request diff review integration attaching comment gutter markers, review thread and draft comment inlays, toolbar actions, and viewed-state tracking to VCS diff editors.

## Interface
- [index.ts:registerReviewDiff] — Subscribes to active VCS diff sessions, attaches `ReviewDiff` instances to `pullRequest` diffs, and registers the GitHub settings page and review diff actions (`github.diff.addComment`, `github.diff.addMultilineComment`, `github.diff.prevComment`, `github.diff.nextComment`, `github.diff.refresh`).
- [reviewDiff.tsx:ReviewDiff], [reviewDiff.tsx:activeReviewDiff] — Manages CodeMirror compartments, gutter commentable ranges, thread/new-comment line inlays, comment navigation, and automatic mark-as-viewed on diff open.
- [Inlays.tsx:ThreadInlay], [Inlays.tsx:discardNewComment], [Inlays.tsx:NewCommentInlay] — React inlay components for existing review threads and draft review comments.
- [ReviewToolbar.tsx:ReviewToolbar] — Diff toolbar action buttons (Refresh Review Data, Submit Review, Previous/Next Comment).
- [GithubSettingsPage.tsx:GithubSettingsPage] — Settings › Version Control › GitHub page configuring automatic "Mark as viewed" and preferred review comment mode.
- [model.ts:PatchHunk], [model.ts:PlacedThread], [model.ts:ThreadStop], [model.ts:CommentPosition], [model.ts:SubmitActionKind], [model.ts:SUBMIT_ACTION_TEXT], [model.ts:parsePatch], [model.ts:commentableRanges], [model.ts:placeThread], [model.ts:placeThreads], [model.ts:newCommentLocation], [model.ts:lineToUnified], [model.ts:threadOrder], [model.ts:adjacentThread], [model.ts:submitActions], [model.ts:shouldMarkViewedOnOpen] — Pure diff patch parsing, thread placement, unified hunk ordering, and submit action rules.
- [newComments.ts:NewComment], [newComments.ts:useNewComments], [newComments.ts:newCommentsOf], [newComments.ts:requestNewComment], [newComments.ts:updateNewComment], [newComments.ts:removeNewComment], [newComments.ts:clearNewComments], [newComments.ts:submitNewComment] — Store and submission workflow for draft review comments.
- [settings.ts:MarkViewedPreference], [settings.ts:ReviewDiffSettings], [settings.ts:useReviewDiffSettings], [settings.ts:setAutomaticallyMarkAsViewed], [settings.ts:setReviewCommentsPreferred] — Persistent review diff settings store.

## Invariants
- [newComments.ts:requestNewComment] — Deduplicates draft comments by `(number, path, commitSha, side, line)`, incrementing `focus` to re-focus an existing draft inlay instead of creating a duplicate.
- [model.ts:shouldMarkViewedOnOpen], [reviewDiff.tsx:ReviewDiff] — Automatically marks a file as viewed on open only for full-PR diffs (`!isCommitDiff`) when `automaticallyMarkAsViewed` matches and the file is currently `UNVIEWED`.
- [Inlays.tsx:discardNewComment] — Prompts with a confirmation dialog before discarding a draft comment only when `current.text.trim()` is non-empty.

## Side Effects
- [reviewDiff.tsx:ReviewDiff] — Reconfigures CodeMirror diff pane compartments, updates `githubReviewDiff` context key, loads PR threads/pendingReview/viewedState slices, marks files viewed via `setViewed`, and opens adjacent files via `openPullRequestFileDiff`.
- [settings.ts:useReviewDiffSettings] — Persists diff review preferences in `localStorage` (`openidea.github.settings.v1`).

## Verification
- Run: `pnpm test`
