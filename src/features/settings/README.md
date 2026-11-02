# settings

## Overview
Settings feature entry point activating the Settings dialog/window, runtime settings synchronization (keymap, UI font, zoom, compact mode, idle save, alt-drag guard), File Type overrides, Python interpreter status, and Search Everywhere settings indexing.

## Interface
- [index.ts:SettingsWindow] [index.ts:settingsWindowParent] [index.ts:activate] [index.ts:activateSettingsWindow] Activates the Settings feature in main project windows and child Settings windows.
- [runtime.ts:applyRuntimeSettings] [runtime.ts:trackRuntimeSettings] Applies validated settings to the keymap, theme, editor, and CSS custom properties.
- [appearanceRuntime.ts:applyAppearanceOptions] [appearanceRuntime.ts:nextZoom] [appearanceRuntime.ts:zoomActions] [appearanceRuntime.ts:installAltDragGuard] [appearanceRuntime.ts:installIdleSave] IDE zoom actions, OS theme follower, Linux Alt+drag guard, and idle auto-save timer.
- [fileTypesRuntime.ts:overrideKey] [fileTypesRuntime.ts:activateFileTypes] Plain-text/auto-detect file type override actions and menu contributions.
- [searchIndex.ts:settingsSearchEntries] [nativeToAscii.ts:NATIVE_TO_ASCII_ACTION] [nativeToAscii.ts:nativeToAsciiAction] Search Everywhere settings index builder and .properties native-to-ASCII toggle action.
- [components](./components/README.md) - Reusable Settings form controls (Row, Toggle, NumberInput, Select, ColorSwatches, GlobListEditor, FontPicker, SshConfigSelect) and PageContext/useSetting hooks bound to staged settings keys.
- [dialog](./dialog/README.md) - Settings modal dialog and shared SettingsLayout/SettingsFooter (page tree, search filter, breadcrumb header, Global/Project scope switch, back/forward history, and per-page error boundary).
- [keymap](./keymap/README.md) - Keymap settings page, pure keymap override/conflict/preset model with when-clause overlap analysis, keystroke/mouse shortcut recorder dialog, and Search Everywhere "Assign Shortcut" overlay.
- [languages](./languages/README.md) - SDK/toolchain detection and caching (sdk_* IPC), Python interpreter status bar widget and package manager (pip/venv), JDK downloader, Project Structure folder-role editor, and Go/Java/Node.js/Rust/TypeScript/Prettier/Gradle/Maven settings pages.
- [model](./model/README.md) - Settings validation schemas (ValidSettings), page tree construction and search/highlight matching (buildPageTree, filterPageTree), and the useSettingsDialog staging/persistence store (stage, applySettings).
- [pages](./pages/README.md) - Registry and page specifications for all built-in Settings pages (Appearance & Behavior, Keymap, Editor, Version Control, Build/Execution/Deployment, Languages & Frameworks, Tools, SSH Configurations).
- [theme](./theme/README.md) - WCAG 2.x color contrast calculation (parseHex, relativeLuminance, contrastRatio) and CSS custom property token extraction (readTokens, isColorValue) for theme verification.
- [window](./window/README.md) - Native child Settings window (settings-<parent>) UI and Tauri event bridge mirroring the parent project window's registered actions.

## Invariants
- [appearanceRuntime.ts:installIdleSave] Arms the idle-save timer only after user input (keydown, pointerdown, wheel) when a project is open and saveOnIdle is enabled.
- [fileTypesRuntime.ts:overrideKey] Stores file type overrides under project-relative paths for files inside root, and absolute paths outside.

## Side Effects
- [appearanceRuntime.ts:applyAppearanceOptions] Mutates document.documentElement dataset/CSS variables and webview zoom (getCurrentWebview().setZoom).
- [index.ts:activate] Registers file.settings, file.newProjectsSettings, settings.keymap, zoom actions, editor.encodings.toggleNativeToAscii, overlays, and settings search provider.

## Verification
- Run: `pnpm test`
