# github

## Overview
GitHub Pull Requests feature contribution registering the `pullRequests` tool window, `github.timeline` editor kind, VCS diff and in-editor review mode extensions, and GitHub workbench actions and menus.

## Interface
- [index.ts:activate] — Activates the GitHub feature: registers the `pullRequests` tool window, `github.timeline` editor kind, review diff and review mode extensions, Git menu items, and workbench actions (`github.pullRequests.show`, `github.pullRequests.viewList`, `github.pullRequests.openCreate`, `github.pullRequests.refreshList`, `github.pullRequests.changeRepository`, `github.pullRequest.*`, `github.SubmitReview`, `github.ToggleViewed`).
- [api](./api/README.md) - Typed GitHub GraphQL and REST API client executing authenticated requests through the backend `gh api` IPC command with automatic transitive GraphQL fragment assembly.
- [create](./create/README.md) - New Pull Request tool window tab (`CreatePanel`) supporting base/head branch direction selection, commit and file diff preview, metadata choosers, remote branch push, and PR creation.
- [data](./data/README.md) - Data, caching, and mutation layer for GitHub repository connections, paginated pull request lists, per-PR slice stores, local Git/worktree operations, unread state, and search history.
- [details](./details/README.md) - Pull request `#N` details tab (`DetailsPanel`), including branch and commit pickers, file changes tree with viewed state, CI/conflict/reviewer status checks, review flow actions, and the Submit Review popup.
- [diff](./diff/README.md) - Pull request diff review integration attaching comment gutter markers, review thread and draft comment inlays, toolbar actions, and viewed-state tracking to VCS diff editors.
- [list](./list/README.md) - Pull request list tab (`ListPanel`) featuring search/filter chips, `:qualifier` query completion, virtualized PR rows with unread/check/thread indicators, and hover balloons.
- [reviewMode](./reviewMode/README.md) - In-editor Pull Request Review Mode displaying review change markers, commentable gutter ranges, thread/draft inlays, and top-right Review Mode controls directly inside regular file editors when the checked-out branch tracks an open pull request.
- [timeline](./timeline/README.md) - Pull request timeline custom editor tab displaying PR title/description, merged timeline events, commits, reviews, review threads, and the new-comment input.
- [toolwindow](./toolwindow/README.md) - Pull Requests tool window shell (`pullRequests`), managing tab state (PR list, `#N` details, New Pull Request), timeline editor inputs, and cross-component action handlers.
- [ui](./ui/README.md) - Shared GitHub UI primitives: HiDPI user/team avatars with review-state rings, single/multi-select fuzzy chooser popups, state/label/branch chips, sanitized Markdown renderer, relative date formatters, and merge/push confirmation dialogs.

## Invariants
- [index.ts:activate] — Shows the `pullRequests` tool window stripe button only when the project has at least one GitHub remote (`githubAvailable` context key); clears all cached PR, list, diff, and tab state whenever the connected repository changes or disconnects.

## Side Effects
- [index.ts:activate] — Subscribes to `useVcs`, `useProject`, `useGhRepository`, and `useGhTabs`; invokes `refreshConnection()`; updates context keys (`githubConnected`, `githubCanWrite`, `githubPrSelected`); and registers tool windows, editor kinds, menu items, and actions.

## Verification
- Run: `pnpm test`
