# ide-ssh

## Overview
Crate root for SSH session management, SFTP/local remote filesystems, port forwarding tunnels, and askpass/Git credential stores.

## Subdirectories & Boundaries
- [src](./src/README.md) - SSH connection pool (`russh`), `ProxyJump` and `~/.ssh/config` resolution, SFTP/local remote filesystem abstraction, TCP local port forwarding tunnels, `SSH_ASKPASS`/`GIT_ASKPASS` helper IPC server, and app-local secret storage.
