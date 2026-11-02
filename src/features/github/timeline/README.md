# timeline

## Overview
Pull request timeline custom editor tab displaying PR title/description, merged timeline events, commits, reviews, review threads, and the new-comment input.

## Interface
- [TimelineEditor.tsx:TimelineEditor] — Custom editor component (`github.timeline`) rendering the PR header, editable title and description, paginated timeline entries, review action buttons, and comment composer.
- [items.tsx:WebLink], [items.tsx:StatusLine], [items.tsx:TimelineItem] — Renders individual timeline entries (comments, reviews with threads, commit groups, merged simple/state events, and complex branch/reference events).
- [model.ts:MERGE_THRESHOLD_MS], [model.ts:DIFF_CONTEXT_SIZE], [model.ts:SUGGESTION_BLOCK_START], [model.ts:REACTIONS], [model.ts:TimelineCommit], [model.ts:StateEvent], [model.ts:ComplexEvent], [model.ts:SimpleGroup], [model.ts:StateGroup], [model.ts:TimelineEntry], [model.ts:StatusType], [model.ts:HunkLineType], [model.ts:HunkLine], [model.ts:PatchHunk], [model.ts:BodyBlock], [model.ts:SuggestionApplicability], [model.ts:SuggestionTarget], [model.ts:ReactionInfo] — Timeline domain types and constants.
- [model.ts:stateOf], [model.ts:simpleHasChanges], [model.ts:effectiveRename], [model.ts:mergeTimeline], [model.ts:commitsActor], [model.ts:reviewStatus], [model.ts:threadReviewId], [model.ts:canEditReview], [model.ts:presentableName], [model.ts:parsePatchHunk], [model.ts:truncateHunkBefore], [model.ts:threadDiffWithAnchor], [model.ts:threadDiffHunk], [model.ts:hunkLineNumbers], [model.ts:containsSuggestedChange], [model.ts:splitSuggestions], [model.ts:suggestionTarget], [model.ts:suggestionDisabledReason], [model.ts:buildSuggestionPatch], [model.ts:groupReactions], [model.ts:toggleReactionLocally], [model.ts:mentionQuery], [model.ts:filterMentions], [model.ts:insertMention] — Pure timeline merging, diff hunk truncation, suggested-change patch construction, reaction grouping, and `@mention` filtering rules.
- [comments](./comments/README.md) - Shared code review comment, thread, diff hunk, reaction, and suggested-change components used by the pull request timeline and review diff/editor inlays.

## Invariants
- [model.ts:mergeTimeline] — Groups consecutive `PullRequestCommit` items and merges consecutive simple or state events from the same actor within `MERGE_THRESHOLD_MS` (120,000ms), dropping collapsed groups that produce no net change (`!simpleHasChanges` or `newState === inferredOriginalState`).
- [model.ts:mergeTimeline] — Preserves `MERGED` state in a `StateGroup` even if subsequent state events are merged into the same group.
- [model.ts:canEditReview] — Disallows editing a `PullRequestReview` whose initial `body` is empty because GitHub rejects edits to empty-body reviews.
- [model.ts:threadDiffWithAnchor] — Truncates thread diff hunks to `DIFF_CONTEXT_SIZE` (3) lines before the anchor range.

## Side Effects
- [TimelineEditor.tsx:TimelineEditor] — Loads PR details, threads, and timeline pages (`loadPullRequestData`, `loadTimelinePage`), marks the PR as seen (`markSeen`), registers a `ScrollBarStripe` layer for unread/mention/my-comment markers, and invokes PR mutations (`updateDetails`, `addIssueComment`, `toggleReaction`, `closePullRequest`, `reopenPullRequest`).
- [items.tsx:WebLink], [items.tsx:TimelineItem] — Opens external URLs via `openInBrowser`, opens same-repository PRs in the IDE via `openPullRequest`, and mutates comments/reviews via `updateIssueComment`, `deleteIssueComment`, `updateReviewBody`, and `toggleReaction`.

## Verification
- Run: `pnpm test`
