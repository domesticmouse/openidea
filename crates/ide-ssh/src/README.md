# src

## Overview
SSH connection pool (`russh`), `ProxyJump` and `~/.ssh/config` resolution, SFTP/local remote filesystem abstraction, TCP local port forwarding tunnels, `SSH_ASKPASS`/`GIT_ASKPASS` helper IPC server, and app-local secret storage.

## Interface
- [lib.rs:SshManager], [manager.rs:SshManager] — App-wide pool of shared `Session`s keyed by `SshConfiguration::id` (`new`, `with_files`, `files`, `set_configs`, `configs`, `config`, `set_secret`, `secret`, `session`, `connect`, `test`, `trust_host`, `disconnect`, `connected`, `needs_secret`).
- [session.rs:Session], [session.rs:RemoteProcess], [session.rs:Output], [session.rs:ExecOutput], [session.rs:UnknownHostKey], [session.rs:ConnectError], [session.rs:Client] — Authenticated `russh` SSH session supporting `ProxyJump` chains, remote command execution (`exec`, `exec_stream`, `spawn`), gzipped bulk file upload (`put`, `put_with_progress`), SFTP subsystem channels (`sftp`), and `direct-tcpip` streams (`direct_tcpip`).
- [sftp.rs:RemoteEntry], [sftp.rs:SftpFs], [sftp.rs:join], [sftp.rs:parent], [sftp.rs:mtime_of] — High-level SFTP client (`home`, `resolve`, `stat`, `list`, `read`, `write`, `upload`, `upload_new`, `symlink`, `create_dir`, `download`, `mkdir_all`, `remove`, `rename`, `close`).
- [filesystem.rs:FsConnection], [filesystem.rs:RemoteFs] — Unified async filesystem API over `SftpFs` or `Local` disk used by deployment and remote development bootstrap.
- [tunnel.rs:Tunnel], [tunnel.rs:unreachable_target] — Local `127.0.0.1` TCP listener (`open`, `listen`, `port`, `is_closed`) that forwards accepted sockets over `Session::direct_tcpip`.
- [target.rs:SshTarget], [target.rs:SshFiles], [target.rs:expand], [target.rs:openssh_args] — Resolves `~/.ssh/config` host aliases and `ProxyJump` hops and formats CLI arguments for the system `ssh` binary.
- [secrets.rs:SecretFile] — Thread-safe, `0o600`-permissioned JSON key-value secret file (`new`, `get`, `set`, `delete`, `keys`).
- [askpass.rs:AskpassServer], [askpass.rs:AskpassPrompt], [askpass.rs:AskpassKind], [askpass.rs:PORT_ENV], [askpass.rs:COMMAND_ENV], [askpass.rs:TOKEN_ENV], [askpass.rs:classify], [askpass.rs:remembers], [askpass.rs:run_helper] — Localhost TCP askpass server and helper entrypoint that intercepts `SSH_ASKPASS` and `GIT_ASKPASS` prompts from child processes.
- [git_creds.rs:GitCredential], [git_creds.rs:GitAnswer], [git_creds.rs:GitCredentials], [git_creds.rs:init], [git_creds.rs:get], [git_creds.rs:prompt_key] — Stores and auto-answers Git HTTPS credentials (`scheme://host[:port]`), persisting new credentials only after the Git command succeeds and purging rejected credentials on auth failure.
- [shell.rs:quote], [shell.rs:tilde_quote], [shell.rs:command_line] — POSIX shell quoting and `cd <cwd> && exec env ...` command-line builder.
- [testing.rs:ExecFn], [testing.rs:sshd] — In-process `russh` + `russh-sftp` test server (enabled under feature `testing`).

## Invariants
- [session.rs:Client::check_server_key] Always rejects changed host keys (`KeyChanged`) with `ErrorKind::PermissionDenied` and surfaces unknown host keys as `ConnectError::UnknownHostKey` until explicitly trusted via `UnknownHostKey::trust`.
- [manager.rs:SshManager::session] Serializes concurrent connection attempts per configuration ID via an internal per-ID `tokio::sync::Mutex` so callers share a single session.
- [sftp.rs:SftpFs::list] Ignores directory entries named `.`, `..`, or containing `/` to prevent path traversal from hostile SFTP servers.
- [git_creds.rs:GitCredentials::command_finished] Pending Git HTTPS credentials are written to `SecretFile` only when the Git command exits with `success == true`, and stored credentials consumed by a command that fails with an HTTP 401/403 or auth error are deleted.

## Side Effects
- [session.rs:Session::connect] Opens outbound TCP connections, negotiates SSH handshakes, queries the OS SSH agent (`SSH_AUTH_SOCK` or `\\.\pipe\openssh-ssh-agent`), and reads `~/.ssh/config`, `~/.ssh/known_hosts`, and private key files.
- [session.rs:UnknownHostKey::trust] Appends trusted host public keys to `~/.ssh/known_hosts`.
- [secrets.rs:SecretFile::write] Writes secret files atomically via `.json.tmp` rename with `0o600` permissions on Unix.
- [askpass.rs:AskpassServer::start], [tunnel.rs:Tunnel::listen] Binds localhost (`127.0.0.1:0`) TCP listeners and spawns background worker threads/tasks.

## Verification
- Run: `cargo test -p ide-ssh`
