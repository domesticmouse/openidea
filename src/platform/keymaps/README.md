# keymaps

## Overview
Built-in keyboard and mouse shortcut preset tables for macOS and Windows/Linux across IntelliJ IDEA, VS Code, and Eclipse keymaps.

## Interface
- [intellij-macos.ts:intellijMacos]: Default IntelliJ IDEA macOS keymap mapping action IDs to shortcut strings.
- [intellij-windows.ts:intellijWindows]: Default IntelliJ IDEA Windows/Linux keymap mapping action IDs to shortcut strings.
- [vscode-macos.ts:vscodeMacos]: VS Code macOS keymap preset.
- [vscode-windows.ts:vscodeWindows]: VS Code Windows/Linux keymap preset.
- [eclipse-macos.ts:eclipseMacos]: Eclipse macOS keymap preset.
- [eclipse-windows.ts:eclipseWindows]: Eclipse Windows/Linux keymap preset.

## Invariants
- [intellij-macos.ts:intellijMacos]: Keymap tables are pure static maps from canonical action IDs to arrays of normalized stroke/chord/double-modifier/mouse binding strings.

## Side Effects
- None

## Verification
- Run: `pnpm test`
