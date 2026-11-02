# web

## Overview
HTML/SVG browser launcher (Open In › Browser via the built-in web server) and side-by-side live HTML preview bar (WebEditor).

## Interface
- [index.ts:activate] Registers the web.html text editor wrapper, web.openInBrowser and web.togglePreview actions, and editor/projectView/openIn browser menu items.
- [browsers.ts:isWebPath] [browsers.ts:isHtmlPath] [browsers.ts:useBrowsers] [browsers.ts:loadBrowsers] [browsers.ts:browserIcon] [browsers.ts:useWebPreview] [browsers.ts:togglePreview] [browsers.ts:webUrl] [browsers.ts:openInBrowser] Installed browser discovery, built-in server URL lookup, and per-file preview state.
- [WebEditor.tsx:WebEditor] HTML editor wrapper displaying the floating browser bar and split iframe preview.

## Invariants
- [browsers.ts:isWebPath] [browsers.ts:isHtmlPath] isWebPath matches .html/.htm/.xhtml/.shtml/.svg for external browser opening, while isHtmlPath restricts the built-in iframe preview and browser bar to HTML documents (excluding .svg).
- [browsers.ts:openInBrowser] Executes file.save before opening the built-in server URL so the external browser reads up-to-date disk contents.

## Side Effects
- [browsers.ts:loadBrowsers] [browsers.ts:webUrl] [browsers.ts:openInBrowser] Invokes IPC commands.webBrowsers(), commands.webUrl(path), and commands.webOpen(url, browser).
- [WebEditor.tsx:WebEditor] Subscribes to fileChanged IDE events to reload the preview iframe when saved.

## Verification
- Run: `pnpm test`
