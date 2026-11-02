# src

## Overview
Cross-platform PTY session manager (`portable-pty`) with shell auto-detection, automatic shell integration injection (`zsh`/`bash`/`fish`), incremental UTF-8 output decoding, and foreground busy detection.

## Interface
- [lib.rs:TerminalOptions], [lib.rs:TerminalInfo], [lib.rs:TerminalManager] — PTY terminal lifecycle manager (`new`, `spawn`, `write`, `resize`, `kill`, `is_busy`, `contains`, `list`, `size`, `kill_all`).
- [lib.rs:default_shell_path], [lib.rs:available_shells], [lib.rs:resolve_shell] — Resolves the user's default login shell (`$SHELL` on Unix, PowerShell/cmd on Windows) and discovers installed shells (`zsh`, `bash`, `fish`, `nu`, `pwsh`, `powershell.exe`, `cmd.exe`, WSL, Git Bash).
- [utf8.rs:Utf8Decoder] — Streaming UTF-8 decoder (`new`, `decode_into`, `decode`, `finish`, `has_pending`) that buffers trailing incomplete 2/3/4-byte sequences across PTY read chunks so split multi-byte characters never produce U+FFFD.

## Invariants
- [integration.rs:prepare] Injects shell integration scripts (`ZDOTDIR` for `zsh`, `--rcfile` for `bash`, `-C` for `fish`) only when the user did not supply custom `args`.
- [utf8.rs:Utf8Decoder::decode_into] Buffers at most 3 trailing incomplete UTF-8 continuation bytes (`pending: [u8; 4]`) between `read` calls and replaces genuinely invalid sequences with U+FFFD.

## Side Effects
- [lib.rs:TerminalManager::spawn] Allocates a native OS pseudo-terminal (`portable-pty`), spawns a child shell process, and starts a background reader thread (`terminal-pty-reader`) that invokes `on_output` and `on_exit`.
- [integration.rs:dir] Writes shell integration startup scripts (`.zshenv`, `.zshrc`, `bash.sh`, `fish.fish`) into `<temp_dir>/openidea-shell-integration/`.
- [lib.rs:TerminalManager::is_busy] Queries OS process tables (`tcgetpgrp` or `pgrep`/`ps` on Unix; `CreateToolhelp32Snapshot` on Windows) to detect whether the shell has a foreground child process.

## Verification
- Run: `cargo test -p ide-terminal`
