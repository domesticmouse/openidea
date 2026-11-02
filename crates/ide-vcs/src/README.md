# src

## Overview
Implements Git version control operations for single and multi-root workspaces, combining pure-Rust `gix` read queries with subprocess `git` CLI execution for index mutations, network operations, changelists, and shelved patches.

## Interface
- [lib.rs:Repo]: Single-repository handle providing status, stage/unstage, commit, partial hunk commit, log, blame, diff, stash, branch, tag, remote, merge, rebase, cherry-pick, revert, reset, changelist, and shelf operations.
- [lib.rs:ChangeKind], [lib.rs:FileStatus], [lib.rs:BranchInfo], [lib.rs:HunkKind], [lib.rs:LineHunk], [lib.rs:BlameLine], [lib.rs:Commit], [lib.rs:LogOrder], [lib.rs:LogQuery], [lib.rs:Branch]: Core status, diff, blame, and commit history data structures.
- [lib.rs:CommitOptions], [lib.rs:PartialCommitFile], [lib.rs:HunkRange], [lib.rs:ChangedFile], [lib.rs:RevisionChange], [lib.rs:Stash], [lib.rs:PushTags], [lib.rs:PushBranchOptions], [lib.rs:RebaseAction], [lib.rs:RebaseEntry], [lib.rs:ResetMode]: Options and descriptors for commits, partial staging, stashes, pushes, interactive rebases, and resets.
- [lib.rs:ShelvedChangeList], [lib.rs:PatchFile], [lib.rs:ApplyOutcome], [lib.rs:CheckoutMode], [lib.rs:DivergedLocal], [lib.rs:CheckoutOptions], [lib.rs:CheckoutOutcome], [lib.rs:DeletedBranch], [lib.rs:MergeSides], [lib.rs:MergeVersions], [lib.rs:Changelist], [lib.rs:ChangelistState]: Types for shelving, patch application, smart checkout, 3-way conflict resolution, and changelists.
- [lib.rs:clone], [lib.rs:init], [lib.rs:diff_hunks]: Top-level functions to clone a remote repository with progress, initialize a new repository, and compute line hunks between texts.
- [roots.rs:Repos]: Multi-root repository manager discovering nested Git worktrees and routing paths to their owning `[lib.rs:Repo]`.
- [compare.rs:CompareScope], [compare.rs:WorkingTreeChange], [compare.rs:RepositoryComparison], [compare.rs:CompareResult]: Multi-repository branch/revision comparison types.
- [changelists.rs:load], [changelists.rs:save], [changelists.rs:ensure_ignored]: Persistence helpers for `.openidea/changelists.json`.
- [shelf.rs:list], [shelf.rs:patch_of], [shelf.rs:delete], [shelf.rs:rename]: Storage operations for IDE-managed shelved patch sets.
- [cli.rs:set_git_executable], [cli.rs:detected_git_executable], [cli.rs:git_version], [cli.rs:set_askpass_env], [cli.rs:set_git_command_observer]: Process-global configuration and telemetry hooks for the `git` CLI runner.
- [diff.rs:line_hunks], [diff.rs:build_partial_patch]: Myers line-diff computation and unified patch construction for selected hunk ranges.
- [ignore.rs:IgnoreDestination], [ignore.rs:add_to_ignore]: Helpers for appending ignore rules to `.gitignore` or `.git/info/exclude`.
- [manage.rs:MergeOptions], [manage.rs:RebaseOptions], [manage.rs:Worktree], [manage.rs:RemoteEdit], [remotes.rs:Remote]: Options and descriptors for merges, rebases, Git worktrees, and remotes.
- [index_writes.rs:is_own_index_write]: Checks whether a `.git/index` modification originated from an `ide-vcs` operation.

## Invariants
- [lib.rs:ExecutionScope]: Propagates per-repository `execution_allowed` state via thread-local scope (`[cli.rs:enter_execution_scope]`), blocking external `git` subprocess execution when a project is untrusted unless explicitly overridden.
- [changelists.rs:ChangelistState::normalized]: Guarantees at least one changelist exists (creating `DEFAULT_ID = "default"` named `"Changes"` if empty), deduplicates IDs, ensures `active` references an existing list, and prunes file assignments referencing non-existent lists.
- [ignore.rs:add_to_ignore]: Rejects paths outside the repository root, non-UTF-8 paths, paths containing `..` or `.git`, paths traversing symlinked ancestor directories, and paths already tracked in the Git index.
- [compare.rs:Repos::compare_paths], [remotes.rs:check_rev]: Reject empty revisions or revision strings starting with `-` to prevent CLI option injection.
- [roots.rs:Repos]: Discovers nested repositories up to `MAX_DEPTH = 4` (skipping `node_modules`, `target`, `dist`, `build`, `out`, `vendor`, `.gradle`, `.idea`, `.vscode`, `.openidea`, `__pycache__`, `.venv`, `venv`, `.tox`), sorts roots ascending by path, and resolves `for_path` to the deepest enclosing worktree root.

## Side Effects
- [cli.rs:run], [cli.rs:run_streaming]: Spawn external `git` subprocesses with `GIT_TERMINAL_PROMPT=0` and `LC_ALL=C`, inject SSH askpass environment variables, and notify `COMMAND_OBSERVER`.
- [changelists.rs:save], [changelists.rs:ensure_ignored]: Atomically write `.openidea/changelists.json` via `.openidea/changelists.json.tmp` and append `.openidea/changelists.json` to the root `.gitignore`.
- [shelf.rs:shelve], [shelf.rs:delete], [shelf.rs:rename]: Create, update, and remove `<shelf>/<id>/shelved.patch` and `<shelf>/<id>/shelf.json` on disk.
- [ignore.rs:add_to_ignore]: Atomically writes `.gitignore` or `.git/info/exclude` via temporary files in the target parent directory.
- [index_writes.rs:IndexWrites::record]: Records `(mtime_ns, size)` stamps of `.git/index` in a bounded process-global `Mutex` (up to 256 paths) after index mutations.

## Verification
- Run: `cargo test -p ide-vcs`
