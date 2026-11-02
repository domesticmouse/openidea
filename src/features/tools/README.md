# tools

## Overview
Language server, debug adapter, and formatter installer/configurator (Settings › Languages & Frameworks › Language Servers), missing server install prompts, and one-click Eclipse Temurin JDK downloader.

## Interface
- [index.ts:activateSettingsPages] [index.ts:activate] Registers the tools settings page, JDK actions, and missing-server / missing-JDK notification listeners.
- [store.ts:SETTINGS_PAGE_ID] [store.ts:DOWNLOAD_QUEUE] [store.ts:InstallJob] [store.ts:useTools] [store.ts:refreshTools] [store.ts:toolByName] [store.ts:installTool] [store.ts:cancelInstall] [store.ts:uninstallTool] [store.ts:STATUS_LABEL] Managed tool state store and queued installer.
- [backend.ts:LanguageChoice] [backend.ts:ToolsBackend] [backend.ts:tauriToolsBackend] [backend.ts:sampleTools] [backend.ts:MemoryToolsBackend] [backend.ts:toolsBackend] [backend.ts:setToolsBackend] Pluggable Tauri and in-memory tools backend.
- [jdk.ts:chooseForLanguageServer] [jdk.ts:downloadJdkFor] [jdk.ts:installJdkPrompts] [jdk.ts:registerJdkActions] [missing.ts:missingKey] [missing.ts:promptInstall] [missing.ts:installMissingServerPrompts] Missing language server and JDK notification handlers and JDK download workflow.
- [serverConfig.ts:ServerEntry] [serverConfig.ts:ServerField] [serverConfig.ts:serverKey] [serverConfig.ts:toolPathKey] [serverConfig.ts:nameFromCommand] [serverConfig.ts:SERVER_NAME] [serverConfig.ts:parseArgs] [serverConfig.ts:formatArgs] [serverConfig.ts:parseEnv] [serverConfig.ts:formatEnv] [serverConfig.ts:parseJsonObject] [serverConfig.ts:formatJson] [serverConfig.ts:parseList] [serverConfig.ts:formatList] [ServerSettings.tsx:useLanguageChoices] [ServerSettings.tsx:resetLanguageChoices] [ServerSettings.tsx:languageNames] [ServerSettings.tsx:LanguagePicker] [ServerSettings.tsx:ToolSettings] [ServerSettings.tsx:ServerSettings] [ServerSettings.tsx:NewServerDialog] [LanguageServersPage.tsx:filterTools] [LanguageServersPage.tsx:LanguageServersPage] Custom/managed server configuration parsers and Language Servers settings page.

## Invariants
- [store.ts:installTool] [jdk.ts:downloadJdkFor] All tool installations and JDK downloads serialize on the shared DOWNLOAD_QUEUE ("downloads") task queue and deduplicate concurrent requests for the same tool name or JDK version.
- [serverConfig.ts:parseJsonObject] [serverConfig.ts:SERVER_NAME] Rejects null anywhere inside JSON settings objects (since settings.toml does not support null) and restricts custom server names to /^[A-Za-z0-9][A-Za-z0-9_+-]*$/.
- [missing.ts:promptInstall] Suppresses missing-server prompts for non-formatters when another tool of the same kind covering any of the same languages is already installed/available.

## Side Effects
- [backend.ts:tauriToolsBackend] [jdk.ts:chooseForLanguageServer] Invokes IPC commands.toolsList, commands.toolsInstall, commands.toolsUninstall, commands.lspServers, commands.lspRestart, commands.settingsSet, and @tauri-apps/plugin-dialog.
- [store.ts:installTool] [jdk.ts:downloadJdkFor] Registers/updates workbench background tasks (queueTask, upsertTask) and emits notifications in the "tools" and "lsp" groups.

## Verification
- Run: `pnpm test`
