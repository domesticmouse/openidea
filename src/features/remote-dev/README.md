# remote-dev

## Overview
SSH Remote Development connection dialog (remote.connect), remote folder browser, disconnect action (remote.disconnect), and login-shell/devcontainer environment reload (remote.reloadEnvironment).

## Interface
- [index.ts:activate] Registers remote.connect overlay (ConnectHost) and actions remote.connect, remote.disconnect, and remote.reloadEnvironment.
- [ConnectDialog.tsx:ConnectHost] Modal dialog for selecting an SSH configuration, browsing remote directories via commands.remoteBootstrapList, and opening a remote project window via openRemoteProject.
- [environment.ts:reloadSummary] [environment.ts:reloadEnvironment] Calls commands.envReload(), summarizes changed environment variables, and offers an action to restart running LSP servers (commands.lspServers, commands.lspRestart).
- [model.ts:useConnectDialog] [model.ts:openConnectDialog] [model.ts:closeConnectDialog] [model.ts:remoteParent] [model.ts:browsableFolders] [model.ts:listedDirectory] [model.ts:stageLabel] Connect dialog state store, POSIX path parent/folder sorting helpers, and connection stage labels.

## Invariants
- [index.ts:activate] remote.disconnect requires confirmLeaveProject() approval before calling disconnectRemote(), and calls maybeEditorService()?.cancelClose?.() if disconnecting throws.
- [model.ts:browsableFolders] Filters entries to directories (e.isDir) and sorts non-hidden folders before dotfiles (e.name.startsWith(".")).

## Side Effects
- [ConnectDialog.tsx:ConnectHost] [environment.ts:reloadEnvironment] [index.ts:activate] Calls commands.remoteBootstrapList, openRemoteProject, disconnectRemote, commands.envReload, commands.lspServers, and commands.lspRestart, and displays notifications.

## Verification
- Run: `pnpm test`
