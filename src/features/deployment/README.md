# deployment

## Overview
SFTP and local folder deployment integration providing Upload, Download, Sync with Deployed, Delete from Server, Automatic Upload on save, and the Remote Host SFTP browser tool window.

## Interface
- [index.ts:activate]
- [store.ts:REMOTE_HOST_WINDOW]
- [store.ts:useDeployment]
- [store.ts:SyncDialogState]
- [store.ts:SecretPrompt]
- [store.ts:RemoteTreeState]
- [store.ts:EMPTY_TREE]
- [store.ts:patchTree]
- [store.ts:patchSync]
- [store.ts:promptSecret]
- [model.ts:FocusedPart]
- [model.ts:SyncChoice]
- [model.ts:ReportSummary]
- [model.ts:SshRemedy]
- [model.ts:pathsFromArg]
- [model.ts:targetPaths]
- [model.ts:defaultSyncAction]
- [model.ts:syncChoices]
- [model.ts:initialSyncChoices]
- [model.ts:syncSteps]
- [model.ts:resolveServer]
- [model.ts:serverLabel]
- [model.ts:summarizeReport]
- [model.ts:joinRemote]
- [model.ts:remoteParent]
- [model.ts:remoteName]
- [model.ts:localPathForRemote]
- [model.ts:sshRemedy]
- [model.ts:formatSize]
- [model.ts:formatTime]
- [actions.tsx:actionTargets]
- [actions.tsx:showReport]
- [actions.tsx:downloadPaths]
- [actions.tsx:openSync]
- [actions.tsx:closeSync]
- [actions.tsx:runSync]
- [actions.tsx:autoUploadOn]
- [actions.tsx:registerDeploymentActions]
- [remoteHost.ts:loadFolder]
- [remoteHost.ts:ensureRoot]
- [remoteHost.ts:refreshAll]
- [remoteHost.ts:openRemote]
- [remoteHost.ts:newFolder]
- [remoteHost.ts:renameEntry]
- [remoteHost.ts:deleteEntries]
- [remoteHost.ts:downloadToProject]
- [remoteHost.ts:copyRemotePath]
- [ssh.ts:withSsh]
- [RemoteHostView.tsx:RemoteHostView]
- [Dialogs.tsx:DeploymentDialogs]

## Invariants
- [model.ts:targetPaths] — Filters out paths outside projectRoot and deduplicates any path that is already a descendant of another selected directory.
- [model.ts:localPathForRemote] — Resolves remote paths to local project paths using the most specific (longest deployment prefix) mapping.
- [model.ts:sshRemedy], [ssh.ts:withSsh] — Retries SSH operations at most 3 times on host-key or authentication prompts and never offers to trust a changed SSH host key (/^SSH host key .* has changed/).

## Side Effects
- [actions.tsx:transfer], [actions.tsx:openSync], [actions.tsx:runSync] — Invokes commands.deployCoverage, commands.deployUpload, commands.deployDownload, commands.deployDeleteRemote, commands.deployCompare, commands.deploySync, and commands.deployInfo, and updates 'deployment.autoUpload' project settings via commands.settingsSet.
- [remoteHost.ts:loadFolder], [remoteHost.ts:openRemote], [remoteHost.ts:deleteEntries] — Executes SFTP browser operations via commands.deployServerRoot, commands.remoteList, commands.remoteOpen, commands.remoteMkdir, commands.remoteRename, and commands.remoteDelete.
- [ssh.ts:withSsh] — Trusts unknown SSH host keys via commands.sshTrustHost and stores SSH passwords/passphrases via commands.sshSetSecret.

## Verification
- Run: `pnpm test`
