# pages

## Overview
Registry and page specifications for all built-in Settings pages (Appearance & Behavior, Keymap, Editor, Version Control, Build/Execution/Deployment, Languages & Frameworks, Tools, SSH Configurations).

## Interface
- [registry.tsx:builtinSpec] [registry.tsx:pageLabels] [registry.tsx:optionLabels] [registry.tsx:registerBuiltinPages] Registers built-in settings page contributions and search labels.
- [spec.ts:OptionSpec] [spec.ts:SectionSpec] [spec.ts:PageSpec] [spec.ts:specLabels] [OptionsPage.tsx:OptionsPage] Declarative page specification types and renderer.
- [builtins.tsx:EDITOR_GROUP] [builtins.tsx:BUILTIN_PAGES] [FileTypes.tsx:applyPatternEdit] [FileTypes.tsx:RecognizedFileTypes] Built-in page list and Editor › File Types pattern association editor.
- [behavior.tsx:APPEARANCE_GROUP] [behavior.tsx:BEHAVIOR_PAGES] [httpProxy.tsx:HTTP_PROXY_PAGE] Appearance & Behavior pages (Appearance, System Settings, Menus and Toolbars, Notifications, Quick Lists, Path Variables, HTTP Proxy).
- [deployment.tsx:DEPLOYMENT_PAGES] [deploymentModel.ts:parseExcludes] [deploymentModel.ts:formatExcludes] [deploymentModel.ts:updateMapping] [deploymentModel.ts:addMapping] [deploymentModel.ts:removeMapping] [deploymentModel.ts:nextServerName] [deploymentModel.ts:newServer] [deploymentModel.ts:fixDefaultServer] [deploymentModel.ts:remoteParent] Deployment servers, mappings, and excluded paths settings.
- [execution.tsx:BUILD_GROUP] [execution.tsx:CONSOLE_ENCODINGS] [execution.tsx:EXECUTION_PAGES] [cmake.tsx:CMakeProfiles] [runTargets.tsx:parentPath] [runTargets.tsx:RUN_TARGET_PAGES] Build, Execution, Deployment pages (Compiler, Debugger, Coverage, CMake, Docker, Run Targets).
- [ssh.tsx:SSH_PAGES] [sshConnection.ts:SshTestRemedy] [sshConnection.ts:sshTestRemedy] [sshConnection.ts:parseSshPort] SSH Configurations page and connection test remedy classifier.
- [tools.tsx:TOOLS_GROUP] [tools.tsx:TOOLS_PAGES] [vcs.tsx:VCS_PAGES] [languages.tsx:LANGUAGES_GROUP] [languages.tsx:LANGUAGE_PAGES] Tools, Version Control (Git, Commit, Confirmation, Directory Mappings, Issue Navigation, Shelf, Changelists), and Languages & Frameworks pages.
- [workflowControls.tsx:BoundToggle] [workflowControls.tsx:Indented] [workflowControls.tsx:Grid] [workflowControls.tsx:ToggleNumber] [workflowControls.tsx:RadioChoice] [workflowControls.tsx:RadioRow] [workflowControls.tsx:PageLink] [workflowControls.tsx:TableColumn] [workflowControls.tsx:TableEditor] Shared workflow setting controls and inline table editor.
- [editor](./editor/README.md) - Declarative Editor settings page builder (editorPage) and Editor settings pages (General & sub-pages, Code Editing, Inlay Hints, Font, Color Scheme with .icls import/export, Code Style & per-language pages, File Encodings, Inspections & profile manager, Natural Languages & Spelling).

## Invariants
- [sshConnection.ts:sshTestRemedy] Never offers host-key trust when a host key has changed (/^SSH host key .* has changed/), and never prompts for a secret when auth === "openSsh" unless a key is encrypted.
- [deploymentModel.ts:fixDefaultServer] Preserves defaultServer if still present in servers, otherwise falls back to servers[0]?.id ?? "".
- [workflowControls.tsx:TableEditor] Drops any edited table row whose first column is empty on commit.

## Side Effects
- [registry.tsx:registerBuiltinPages] Registers built-in SettingsPageContributions and asynchronously loads per-language Code Style pages.
- [httpProxy.tsx:CheckConnection] [deployment.tsx:ConnectionTab] [ssh.tsx:TestConnection] [vcs.tsx:GitExecutableRow] Invokes IPC commands (commands.proxyCheckConnection, commands.deployTestConnection, commands.remoteList, commands.sshTest, commands.sshTrustHost, commands.vcsGitVersion, commands.gitCredentials, commands.gitCredentialForget).

## Verification
- Run: `pnpm test`
