# plugins

## Overview
Settings › Plugins UI (Available local catalog and Installed tabs) and runtime bridge connecting the backend WASM plugin host (ide-plugin) to workbench actions, themes, notifications, and active editor text edits.

## Interface
- [index.ts:activate] [index.ts:activateSettingsPages] Registers the plugins settings page, plugin actions (plugins.settings, plugins.installFromDisk, plugins.reloadAll), starts the plugin bridge, and refreshes plugins on project root/trust changes.
- [bridge.ts:pluginActionId] [bridge.ts:pluginActions] [bridge.ts:syncPluginActions] [bridge.ts:PLUGIN_THEME_STYLE_ID] [bridge.ts:pluginThemeCss] [bridge.ts:applyPluginTheme] [bridge.ts:PLUGIN_THEME_CACHE_KEY] [bridge.ts:CachedPluginTheme] [bridge.ts:trackPluginTheme] [bridge.ts:applyTextToActiveEditor] [bridge.ts:handlePluginEvent] [bridge.ts:startPluginBridge] Synchronizes active plugin commands into ActionManager, injects plugin theme CSS variables, caches theme in localStorage, tracks active editor file, and handles PluginEvents.
- [store.ts:SETTINGS_PAGE_ID] [store.ts:usePlugins] [store.ts:errorText] [store.ts:refreshPlugins] [store.ts:setPluginEnabled] [store.ts:reloadPlugin] [store.ts:uninstallPlugin] [store.ts:refreshCatalog] [store.ts:setAllInstalledEnabled] [store.ts:compareVersions] [store.ts:installFromDisk] [store.ts:selectPluginTheme] [store.ts:runPluginCommand] Zustand store and async operations for plugin installation, enablement, reload, uninstallation, and theme selection.
- [backend.ts:PluginsBackend] [backend.ts:tauriPluginsBackend] [backend.ts:sampleCatalog] [backend.ts:createMemoryPluginsBackend] [backend.ts:pluginsBackend] [backend.ts:setPluginsBackend] Abstraction over Tauri plugin_* IPC commands + Channel<PluginEvent> and an in-memory browser simulation.
- [PluginsPage.tsx:PERMISSION_TEXT] [PluginsPage.tsx:NO_REPOSITORY] [PluginsPage.tsx:filterPlugins] [PluginsPage.tsx:filterCatalog] [PluginsPage.tsx:catalogState] [PluginsPage.tsx:PluginsPage] Settings page component and filtering/catalog comparison helpers.

## Invariants
- [store.ts:refreshPlugins] Uses a monotonically increasing generation counter so stale concurrent list() snapshots are discarded.
- [bridge.ts:pluginThemeCss] Rejects theme token names not matching /^--[a-z0-9-]+$/ or values containing /[;{}<>\\]/ to prevent CSS injection.
- [bridge.ts:applyTextToActiveEditor] Refuses to insert or replace text when no editor is active or editor.ctx.readOnly is true, emitting a warning notification instead.

## Side Effects
- [backend.ts:tauriPluginsBackend] Invokes commands.pluginList, commands.pluginSetEnabled, commands.pluginInstallDir, commands.pluginCatalog, commands.pluginUninstall, commands.pluginReload, commands.pluginRunCommand, commands.pluginSetActiveFile, commands.pluginSetTheme, commands.pluginSetThemeFrame, commands.pluginSubscribe, and opens native folder picker via @tauri-apps/plugin-dialog.
- [bridge.ts:applyPluginTheme] [bridge.ts:trackPluginTheme] Mutates document.head (#openidea-plugin-theme <style>), document.documentElement.dataset (data-plugin-theme, data-plugin-theme-kind), and localStorage (openidea.pluginTheme).
- [bridge.ts:applyTextToActiveEditor] Dispatches CodeMirror transactions (userEvent: "input.plugin") on the active editor view.

## Verification
- Run: `pnpm test`
