# public

## Overview
Static web assets served at the root URL, including the synchronous pre-CSS theme restoration script (`boot.js`) and startup splash stylesheet (`splash.css`).

## Interface
- [boot.js:addStyle] — Pre-render bootstrap script loaded by `/index.html` that restores `data-theme`, `data-contrast`, `data-color-theme`, `data-plugin-theme`, `data-plugin-theme-kind`, and `data-window="settings"` attributes on `document.documentElement` and injects `#openidea-plugin-theme` / `#openidea-boot-colors` `<style>` elements

## Invariants
- [boot.js:last] — Validates `colorTheme` against `/^[a-z][a-z0-9-]*$/`, rejects `plugin.css` containing `<`, `\`, or `url(`, and validates `--boot-frame`/`--boot-fg`/`--boot-accent` against `/^#[0-9a-f]{3,8}$/i` before injecting DOM styles
- [boot.js:window] — Sets `document.documentElement.dataset.window = "settings"` when `window.__TAURI_INTERNALS__.metadata.currentWindow.label` starts with `"settings-"`

## Side Effects
- [boot.js:root] — Reads `localStorage.getItem("openidea.theme")` and `localStorage.getItem("openidea.pluginTheme")`, mutates `document.documentElement.dataset`, and appends `<style>` tags to `document.head`

## Verification
- Run: `pnpm test`
