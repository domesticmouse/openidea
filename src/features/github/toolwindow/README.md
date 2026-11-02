# toolwindow

## Overview
Pull Requests tool window shell (`pullRequests`), managing tab state (PR list, `#N` details, New Pull Request), timeline editor inputs, and cross-component action handlers.

## Interface
- [PullRequestsToolWindow.tsx:PullRequestsToolWindow], [PullRequestsToolWindow.tsx:PullRequestsTabs], [PullRequestsToolWindow.tsx:PullRequestsHeaderActions], [PullRequestsToolWindow.tsx:usePullRequestsHeaderMenu], [PullRequestsToolWindow.tsx:changeRepository] — Tool window component, header tab bar, header action buttons, and repository switcher.
- [open.ts:WINDOW], [open.ts:showPullRequests], [open.ts:openPullRequest], [open.ts:openCreatePullRequest], [open.ts:selectOpenedPullRequest] — Helpers to reveal the Pull Requests tool window and open PR details, creation, or timeline tabs.
- [store.ts:GhTab], [store.ts:GhTabId], [store.ts:GhTabsState], [store.ts:useGhTabs], [store.ts:tabId], [store.ts:openPullRequestTab], [store.ts:openCreateTab], [store.ts:selectTab], [store.ts:closeTab], [store.ts:setListSelection], [store.ts:contextPullRequest], [store.ts:resetTabs] — Zustand store managing open tool window tabs and active PR context.
- [timelineInput.ts:TIMELINE_KIND], [timelineInput.ts:TimelineInputData], [timelineInput.ts:timelineInput], [timelineInput.ts:timelineData], [timelineInput.ts:timelineTitle], [timelineInput.ts:timelineIcon], [timelineInput.ts:openTimeline], [timelineInput.ts:activeTimelinePullRequest] — Custom editor input factory and active-tab inspector for `"github.timeline"`.
- [handlers.ts:GithubHandlers], [handlers.ts:GithubHandlerName], [handlers.ts:handlerContextKey], [handlers.ts:provideGithubHandler], [handlers.ts:githubHandler] — Late-bound handler registry synchronizing `github.<name>` context keys.

## Invariants
- [store.ts:closeTab], [store.ts:openPullRequestTab], [store.ts:openCreateTab] — The `"list"` tab is permanent and cannot be closed; opening an already-open `#N` or `"create"` tab focuses the existing tab rather than duplicating it.
- [timelineInput.ts:activeTimelinePullRequest] — Returns a PR number only when the active timeline editor tab matches the currently connected repository.

## Side Effects
- [store.ts:useGhTabs], [handlers.ts:provideGithubHandler], [open.ts:showPullRequests], [timelineInput.ts:openTimeline] — Updates workbench context keys (`githubPrTab`, `github.<handlerName>`), activates the `pullRequests` tool window via `toolWindowService()`, and opens timeline custom editor tabs via `editorService().openInput()`.

## Verification
- Run: `pnpm test`
