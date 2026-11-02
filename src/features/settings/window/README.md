# window

## Overview
Native child Settings window (settings-<parent>) UI and Tauri event bridge mirroring the parent project window's registered actions.

## Interface
- [bridge.ts:ActionInfo] [bridge.ts:settingsWindowParent] [bridge.ts:describeActions] [bridge.ts:installSettingsHost] [bridge.ts:installActionProxies] Parent/child window action synchronization over Tauri events.
- [SettingsWindow.tsx:SettingsWindow] Standalone Settings window React root component.

## Invariants
- [bridge.ts:installActionProxies] Locally registered actions in the Settings window (such as file.settings) take precedence and are never overwritten by parent action proxies.

## Side Effects
- [bridge.ts:installSettingsHost] [bridge.ts:installActionProxies] Emits and listens to openidea://settings/request-actions, openidea://settings/actions, and openidea://settings/exec across Tauri webview windows.

## Verification
- Run: `pnpm test`
