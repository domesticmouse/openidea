# ide-remote

## Overview
Crate root for `ide-remote`, coordinating SSH/SFTP workspace deployment, path mapping, OpenSSH config discovery, and remote run/debug target execution.

## Subdirectories & Boundaries
- [src](./src/README.md) - SSH deployment synchronization (`Deployment`), bidirectional local/remote path mapping and glob exclusions (`Mappings`, `Excludes`), OpenSSH `~/.ssh/config` host import, and remote target command execution (`RemoteService`, `RemoteRuns`).
