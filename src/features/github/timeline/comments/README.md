# comments

## Overview
Shared code review comment, thread, diff hunk, reaction, and suggested-change components used by the pull request timeline and review diff/editor inlays.

## Interface
- [index.ts:ChatItem] — Re-exports chat row, comment editor, comment item, diff hunk, reaction, suggested change, and thread view components.
- [ChatItem.tsx:ChatItemVariant], [ChatItem.tsx:ChatItemProps], [ChatItem.tsx:avatarSize], [ChatItem.tsx:AuthorLink], [ChatItem.tsx:Tag], [ChatItem.tsx:ChatItem], [ChatItem.tsx:rich], [ChatItem.tsx:joined] — Base chat row layout with avatar, author profile link, relative timestamp, status tags, and template interpolation helpers.
- [CommentEditor.tsx:CommentEditorAction], [CommentEditor.tsx:CommentEditorProps], [CommentEditor.tsx:submitShortcut], [CommentEditor.tsx:CommentEditor] — Auto-growing Markdown comment textarea with `@mention` completion popup, keyboard shortcuts (`Cmd/Ctrl+Enter` to submit, `Escape` to cancel), and extra action buttons.
- [CommentItem.tsx:CommentItemProps], [CommentItem.tsx:confirmDeleteComment], [CommentItem.tsx:CommentItem] — Chat item wrapper for comments supporting inline editing, deletion confirmation, custom body rendering, and emoji reactions.
- [DiffHunk.tsx:HunkView], [DiffHunk.tsx:DiffBox] — Read-only unified diff hunk table with dual line-number columns and anchor highlighting inside a collapsible file header box.
- [Reactions.tsx:ToggleReaction], [Reactions.tsx:ReactionsState], [Reactions.tsx:ReactionsProps], [Reactions.tsx:useReactionsState], [Reactions.tsx:Reactions], [Reactions.tsx:AddReactionButton] — Optimistic reaction state hook, reaction pill bar, and emoji reaction picker popup.
- [SuggestedChange.tsx:ReviewCommentBody] — Renders comment bodies with fenced `suggestion` blocks as interactive diff boxes offering "Apply Locally" and "Commit…" actions.
- [suggestion.ts:applySuggestion], [suggestion.ts:isReviewBranch], [suggestion.ts:useOnReviewBranch] — Applies suggested change patches to local working tree files via `vcsApplyPatch`, optionally performs partial hunk commits via `vcsCommit`, and resolves the review thread.
- [ThreadView.tsx:ThreadViewProps], [ThreadView.tsx:ThreadView] — Renders a pull request review thread in `full` (timeline with diff box and collapsible replies) or `compact` (editor/diff inlay) variant.

## Invariants
- [CommentEditor.tsx:CommentEditor] — Disables submission while `busy` or when trimmed text is empty unless `allowEmpty` is set.
- [Reactions.tsx:useReactionsState] — Applies reaction toggles optimistically against `base === reactions` and reverts local state to `null` if `onToggle` rejects.
- [SuggestedChange.tsx:ReviewCommentBody], [suggestion.ts:applySuggestion] — Suggested changes are disabled when the PR branch is not checked out (`!onReviewBranch`), when the thread is resolved or outdated, or when patch application produces conflicts.
- [ThreadView.tsx:ThreadView] — In `full` mode, only resolved threads can collapse (`collapsed = thread.isResolved && collapsedState`); in `compact` mode, threads with more than 3 comments fold middle replies behind `… N more replies`.

## Side Effects
- [ChatItem.tsx:AuthorLink] — Opens author profile URLs in the external browser via `openInBrowser`.
- [CommentItem.tsx:confirmDeleteComment] — Prompts the user with a modal confirmation dialog before deleting a comment.
- [ThreadView.tsx:ThreadView] — Invokes PR comment/thread mutations (`replyToComment`, `updateReviewComment`, `deleteReviewComment`, `resolveThread`, `unresolveThread`, `toggleReaction`) and opens file diffs via `openPullRequestFileDiff`.
- [suggestion.ts:applySuggestion], [suggestion.ts:useOnReviewBranch] — Reads local files and Git state (`vcsRoots`, `fsReadText`, `vcsFileAt`, `vcsDiffHunks`, `vcsBranchInfo`), applies patches via `vcsApplyPatch`, commits partial hunks via `vcsCommit`, and resolves threads via `resolveThread`.

## Verification
- Run: `pnpm test`
