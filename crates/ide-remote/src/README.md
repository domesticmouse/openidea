# src

## Overview
SSH deployment synchronization (`Deployment`), bidirectional local/remote path mapping and glob exclusions (`Mappings`, `Excludes`), OpenSSH `~/.ssh/config` host import, and remote target command execution (`RemoteService`, `RemoteRuns`).

## Interface
- [deploy.rs:DeploymentCoverage], [deploy.rs:coverage], [deploy.rs:TransferReport], [deploy.rs:TransferError], [deploy.rs:FileStamp], [deploy.rs:SyncStatus], [deploy.rs:SyncEntry], [deploy.rs:SyncAction], [deploy.rs:SyncStep], [deploy.rs:status], [deploy.rs:Deployment]: SFTP deployment transfer and directory synchronization engine.
- [import.rs:import_local]: Imports SSH host definitions from `~/.ssh/config`.
- [mapping.rs:Mappings], [mapping.rs:posix], [mapping.rs:Excludes]: Local-to-remote path mapper and glob exclusion filter.
- [service.rs:Progress], [service.rs:DeployedPath], [service.rs:RemoteService], [service.rs:server], [service.rs:run_target], [service.rs:connection]: High-level remote deployment service.
- [target.rs:SyncFn], [target.rs:RemoteCommand], [target.rs:remote_command], [target.rs:RemoteRuns], [target.rs:RemoteStart]: Remote SSH process runner and path-rewriting command builder.

## Invariants
- `Mappings` sorts mapping pairs by descending local-path specificity (component count, then length) so `to_remote` and `to_local` always match the most specific mapping first (`[mapping.rs:Mappings::new]`).
- `Deployment::upload_file` and `download_file` refuse to transfer paths that are outside the configured mappings or matched by `Excludes` (`[deploy.rs:Deployment::upload_file]`, `[deploy.rs:Deployment::download_file]`).
- `remote_command` rewrites any argument equal to or prefixed by `project_root` into its mapped POSIX remote path and POSIX-quotes all command tokens (`[target.rs:remote_command]`).
- `import_local` skips wildcard patterns (`*`, `?`, `!`) and deduplicates hosts both within `~/.ssh/config` and against already-configured `SshServerConfig` entries (`[import.rs:import_from]`).

## Side Effects
- Opens SSH/SFTP connections (`ide_ssh::SshClient`), walks local and remote directories, uploads/downloads/deletes files, and streams remote process stdout/stderr into `IdeEvent::RunOutput` and `IdeEvent::RunExited` (`[service.rs:RemoteService]`, `[deploy.rs:Deployment]`, `[target.rs:RemoteRuns::start]`).
- Reads `~/.ssh/config` (`Include` directives up to depth 8) and probes `~/.ssh/id_*` private keys on the local filesystem (`[import.rs:import_local]`).

## Verification
- Run: `cargo test -p ide-remote`
