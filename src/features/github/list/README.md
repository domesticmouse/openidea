# list

## Overview
Pull request list tab (`ListPanel`) featuring search/filter chips, `:qualifier` query completion, virtualized PR rows with unread/check/thread indicators, and hover balloons.

## Interface
- [ListPanel.tsx:ListPanel] — Pull request list tab with search panel, virtualized infinite-scrolling list, keyboard navigation, and context menu.
- [SearchPanel.tsx:SearchPanel] — Search bar with `:qualifier` / `@user` autocomplete popup and filter dropdown chips (State, Author, Label, Assignee, Review, Sort).
- [PullRequestRow.tsx:PullRequestRow] — Renders a pull request row with unread dot, title, draft/state badge, label icon balloon, CI check icon, unresolved thread count, `#number · author · date` subtitle, and assignee/reviewer avatar stacks.
- [HoverBalloon.tsx:BalloonItem], [HoverBalloon.tsx:HoverBalloon] — Delayed hover tooltip popup listing labels or reviewers to the right of a row trigger.
- [model.ts:QUALIFIERS], [model.ts:STATE_FILTER_OPTIONS], [model.ts:SORT_FILTER_OPTIONS], [model.ts:REVIEW_FILTER_OPTIONS], [model.ts:STATE_VALUES], [model.ts:SORT_VALUES], [model.ts:CompletionContext], [model.ts:CompletionItem], [model.ts:completionContext], [model.ts:completionItems], [model.ts:applyCompletion], [model.ts:authorFilterItems], [model.ts:assigneeFilterItems], [model.ts:reviewFilterLabel], [model.ts:reviewStateLabel], [model.ts:outdatedBannerText] — Pure search completion and filter chip presentation logic.

## Invariants
- [HoverBalloon.tsx:HoverBalloon] — Opens after `TOOLTIP_DELAY` and remains open across a `HIDE_GRACE_MS` (120ms) pointer transition between trigger and popup.
- [ListPanel.tsx:ListPanel] — Triggers `loadMore()` only when `hasMore && !loading && !error` as scroll approaches within `LOAD_MORE_THRESHOLD_PX` (120px) of the bottom.

## Side Effects
- [ListPanel.tsx:ListPanel], [SearchPanel.tsx:SearchPanel] — Updates `useGhPrList` search state, saves search history via `rememberSearch`, updates list selection in `useGhTabs`, and opens PR tabs, diffs, or browser links from context menu actions.

## Verification
- Run: `pnpm test`
