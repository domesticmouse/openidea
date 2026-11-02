# app

## Overview
Desktop shell integration providing the native OS menu bridge, multi-window and LightEdit lifecycle, window close/quit guards, window title sync, i18n locale sync, IDE updates, cache invalidation, and About / Keymap Reference / New Project / Workspace dialogs.

## Interface
- [index.ts:activate]
- [index.ts:activateLocale]
- [appActions.ts:appActions]
- [appActions.ts:updateActions]
- [appActions.ts:editCommand]
- [appActions.ts:showLog]
- [closeGuard.ts:installCloseGuard]
- [closeGuard.ts:decideClose]
- [closeGuard.ts:confirmLeave]
- [closeGuard.ts:quitApp]
- [closeGuard.ts:dirtyCount]
- [closeGuard.ts:confirmExitEnabled]
- [closeGuard.ts:CloseInput]
- [closeGuard.ts:CloseDeps]
- [windows.ts:installLaunchHandling]
- [windows.ts:installLightEditAutoClose]
- [windows.ts:windowActions]
- [windows.ts:openFiles]
- [windows.ts:openPaths]
- [windows.ts:openProjectInWindow]
- [windows.ts:openProjectWithPolicy]
- [windows.ts:openLightEditFileAsProject]
- [windows.ts:consumeLaunch]
- [windows.ts:defaultProjectDirectory]
- [windows.ts:LAUNCH_EVENT]
- [menuBridge.ts:installMenuBridge]
- [menuBridge.ts:buildMenuConfig]
- [menuBridge.ts:dynamicMenuItems]
- [menuBridge.ts:menuActionIds]
- [menuBridge.ts:disabledMenuActions]
- [menuBridge.ts:routeMenuEvent]
- [menuBridge.ts:menuShortcut]
- [menuBridge.ts:MENU_EVENT]
- [locale.ts:installLocaleSync]
- [locale.ts:registerLocalizedActions]
- [locale.ts:localeSetting]
- [title.ts:installWindowTitle]
- [title.ts:windowTitle]
- [host.ts:buildInfo]
- [host.ts:hostOs]
- [host.ts:guessOs]
- [host.ts:HostOs]
- [updates.tsx:checkForUpdates]
- [updates.tsx:checkForUpdatesOnStartup]
- [updates.tsx:installUpdate]
- [updates.tsx:notifyUpdate]
- [updates.tsx:progressText]
- [projectActions.ts:projectActions]
- [projectActions.ts:registerPowerSaveBanner]
- [projectActions.ts:exportedStorage]
- [projectActions.ts:EXPORTED_STORAGE]
- [InvalidateCaches.tsx:InvalidateCachesOverlay]
- [InvalidateCaches.tsx:InvalidateCachesDialog]
- [InvalidateCaches.tsx:registerInvalidateCachesActions]
- [InvalidateCaches.tsx:openInvalidateCaches]
- [InvalidateCaches.tsx:invalidateAndRestart]
- [AboutDialog.tsx:AboutOverlay]
- [AboutDialog.tsx:showAbout]
- [AboutDialog.tsx:useAbout]
- [AboutDialog.tsx:aboutRows]
- [KeymapReference.tsx:KeymapReferenceOverlay]
- [KeymapReference.tsx:showKeymapReference]
- [KeymapReference.tsx:keymapReference]
- [NewProject.tsx:NewProjectOverlay]
- [NewProject.tsx:showNewProject]
- [NewProject.tsx:createProjectFolder]
- [NewProject.tsx:projectNameError]
- [NewProject.tsx:joinPath]
- [WorkspaceDialog.tsx:WorkspaceDialogHost]
- [WorkspaceDialog.tsx:showWorkspaceDialog]
- [LightEditStatus.tsx:LightEditStatus]
- [LightEditStatus.tsx:LIGHT_EDIT_OPEN_PROJECT]
- [UpdateBalloonBody.tsx:UpdateBalloonBody]

## Invariants
- [closeGuard.ts:onCloseRequested] — Prevents re-entrant close handling (if (closing) return false); closing the last open project or LightEdit window when not quitting closes the project to return to the Welcome screen rather than destroying the window, and cancelling a quit notifies the backend via commands.appCancelQuit().
- [closeGuard.ts:decideClose] — Calls session.cancel() in finally whenever close authorization fails or is cancelled so prepared custom editor documents resume editing.
- [updates.tsx:checkForUpdates], [updates.tsx:installUpdate] — Deduplicates concurrent update checks (checking) and update installations (installing), and marks the 'installing' phase uncancellable.
- [NewProject.tsx:projectNameError], [NewProject.tsx:createProjectFolder] — Rejects empty names, '.' / '..', or names containing [/\\:*?"<>|], and refuses to create a project inside an existing non-empty directory.

## Side Effects
- [menuBridge.ts:installMenuBridge] — Listens to window-scoped openidea://menu events and pushes localized labels, keymap shortcuts, dynamic tool window/layout menus, and disabled action IDs to the native OS menu via commands.menuConfigure, commands.menuSetDisabled, and commands.menuRefresh.
- [windows.ts:installLaunchHandling] — Consumes startup launch payloads (commands.windowTakeLaunch), registers the startup launch barrier (provideStartupLaunch, holdSplash), and listens for openidea://launch webview events.
- [closeGuard.ts:installCloseGuard] — Intercepts native window close requests (getCurrentWindow().onCloseRequested), tracks dirty documents via onIdeEvent('docDirty'), and registers provideLeaveProjectGuard.
- [projectActions.ts:exportSettings], [projectActions.ts:importSettings] — Exports and imports IDE settings and whitelisted localStorage keys (EXPORTED_STORAGE) via commands.settingsExport and commands.settingsImport.
- [InvalidateCaches.tsx:invalidateAndRestart] — Schedules cache invalidation via commands.cachesScheduleInvalidation and restarts the application via commands.appRestart.

## Verification
- Run: `pnpm test`
