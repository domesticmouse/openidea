# vcs

## Overview
Implements multi-root Git, diff, patch, shelf, changelist, worktree, and repository-inspection RPC/IPC commands.

## Interface
- Types: `VcsRoot`, `RemoteOp`, `GitExecutableInfo`, `GitOperation`, `PatchSource` (`[commands.rs:VcsRoot]`, `[commands.rs:RemoteOp]`, `[commands.rs:GitExecutableInfo]`, `[commands.rs:GitOperation]`, `[commands.rs:PatchSource]`)
- Repository & status commands: `vcs_roots`, `vcs_branch_info`, `vcs_line_hunks`, `vcs_file_at`, `vcs_blame`, `vcs_log`, `vcs_commit_files`, `vcs_commits_changes`, `vcs_branches`, `vcs_stashes`, `vcs_tags`, `vcs_remotes`, `vcs_worktrees`, `vcs_git_version`, `vcs_detected_git`, `vcs_config_get`, `vcs_operation` (`[commands.rs:vcs_roots]`, `[commands.rs:vcs_log]`, `[commands.rs:vcs_operation]`)
- Working-tree & history mutation commands: `vcs_stage`, `vcs_add_to_ignore`, `vcs_unstage`, `vcs_stage_patch`, `vcs_stage_hunks`, `vcs_discard`, `vcs_commit`, `vcs_checkout`, `vcs_create_branch`, `vcs_undo_commit`, `vcs_delete_branch`, `vcs_restore_branch`, `vcs_rename_branch`, `vcs_merge`, `vcs_merge_with`, `vcs_rebase`, `vcs_rebase_with`, `vcs_stash_push`, `vcs_stash_apply`, `vcs_stash_drop`, `vcs_remote`, `vcs_create_tag`, `vcs_edit_remote`, `vcs_add_worktree`, `vcs_remove_worktree`, `vcs_clone`, `vcs_init` (`[commands.rs:vcs_stage]`, `[commands.rs:vcs_commit]`, `[commands.rs:vcs_remote]`)
- Diff, patch, shelf & changelist commands: `vcs_conflict_versions`, `vcs_diff_text`, `vcs_diff_hunks`, `vcs_compare_scope`, `vcs_compare_paths`, `vcs_diff_with_worktree`, `vcs_diff_revisions`, `vcs_merge_base`, `vcs_create_patch`, `vcs_patch_preview`, `vcs_apply_patch`, `vcs_write_patch`, `vcs_shelve`, `vcs_shelves`, `vcs_unshelve`, `vcs_shelf_delete`, `vcs_shelf_rename`, `vcs_shelf_patch`, `vcs_shelf_diff`, `vcs_shelf_import`, `vcs_changelists_load`, `vcs_changelists_save`, and `register` (`[commands.rs:vcs_diff_hunks]`, `[commands.rs:vcs_shelve]`, `[commands.rs:vcs_changelists_save]`, `[commands.rs:register]`)

## Invariants
- Commands that execute Git hooks, network remotes, or working-tree mutations require project trust via `host.require_trust(window)` (`[commands.rs:vcs_commit]`, `[commands.rs:vcs_remote]`, `[commands.rs:vcs_apply_patch]`).
- Shelf operations (`in_primary_repo`) require a primary project Git repository (`[commands.rs:in_primary_repo]`), and `vcs_add_to_ignore` rejects paths outside `project.root` (`[commands.rs:vcs_add_to_ignore]`).

## Side Effects
- Executes Git repository operations across discovered `ide_vcs::Repo` roots and records `LocalHistory` labels and file snapshots before and after working-tree-mutating operations (`[commands.rs:per_repo]`, `[commands.rs:snapshot_repo_changes]`).
- Writes `.openidea/shelf`, `.openidea/changelists.json`, and `.openidea/.gitignore` (`[commands.rs:keep_private]`, `[commands.rs:vcs_shelve]`, `[commands.rs:vcs_changelists_save]`).

## Verification
- Run: `cargo test -p ide-host`
