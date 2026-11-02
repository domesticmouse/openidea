# create

## Overview
New Pull Request tool window tab (`CreatePanel`) supporting base/head branch direction selection, commit and file diff preview, metadata choosers, remote branch push, and PR creation.

## Interface
- [CreatePanel.tsx:CreatePanel] — Split-pane form for creating a pull request or draft pull request.
- [ChangesPanel.tsx:ChangesPanel] — Commit selector and changed-files tree with diff launch.
- [DirectionSelector.tsx:DirectionSelector] — Base and head repository/branch selector popups.
- [MetadataGrid.tsx:MetadataGrid] — Reviewer, assignee, and label multi-select chooser rows.
- [RemoteBranchDialog.tsx:remoteBranchNameDialog] — Modal prompt asking for the remote branch name before pushing an untracked local branch.
- [store.ts:CreatePrState], [store.ts:useCreatePr], [store.ts:initCreate], [store.ts:resetCreate], [store.ts:setBaseBranch], [store.ts:setHead], [store.ts:refreshBranchesState], [store.ts:onRepositoryChanged], [store.ts:selectCommit], [store.ts:setTitle], [store.ts:setDescription], [store.ts:setReviewers], [store.ts:setAssignees], [store.ts:setLabels], [store.ts:canCreate], [store.ts:createFromForm], [store.ts:selectableReviewers] — Zustand store and workflow actions for the PR creation tab.
- [model.ts:RemoteBranchRef], [model.ts:HeadBranch], [model.ts:Direction], [model.ts:BranchesCheckResult], [model.ts:PushTarget], [model.ts:defaultDirection], [model.ts:headCompareRef], [model.ts:findExistingPullRequestKey], [model.ts:checkBranches], [model.ts:cleanUpBranchName], [model.ts:generateTitle], [model.ts:needsPush], [model.ts:pushTarget] — Pure branch validation, comparison ref, push target, and default title rules.

## Invariants
- [store.ts:canCreate] — Pull request creation is enabled only when `check.state === "ok" && check.value.kind === "ok"` and `!creating`.
- [store.ts:setTitle], [store.ts:setDescription] — Automatic title generation and `.github` PR template population only overwrite the form fields while `titleEdited` / `descriptionEdited` are `false`.
- [store.ts:selectableReviewers] — Filters out the authenticated user (`me.id`) from user reviewer candidates while keeping team reviewers.

## Side Effects
- [store.ts:initCreate], [store.ts:createFromForm] — Queries local Git branches, commits, merge-base, and revision diffs via IPC (`vcsBranches`, `vcsBranchInfo`, `vcsLog`, `vcsCommitFiles`, `vcsMergeBase`, `vcsDiffRevisions`), pushes local branches via `runRemote({ op: "pushBranch", ... })`, creates the PR via `createPullRequest`, opens the resulting `#N` PR tab, and closes the `"create"` tab.

## Verification
- Run: `pnpm test`
