# markdown

## Overview
Live Markdown preview, split editor/preview layout wrapper, GFM + DOMPurify HTML sanitizer, line-based scroll sync, and themed Mermaid diagram renderer with pan/zoom.

## Interface
- [MarkdownEditor.tsx:MarkdownEditor] [MarkdownLayoutActions.tsx:MarkdownLayoutActions] Text-editor wrapper rendering Editor Only / Editor and Preview / Preview Only modes and header layout buttons.
- [modes.ts:MarkdownMode] [modes.ts:useMarkdownModes] [modes.ts:modeOf] [modes.ts:setMarkdownMode] [live.ts:LiveMarkdown] [live.ts:useLiveMarkdown] [live.ts:registerMarkdownLiveSource] Per-file display mode store and live unsaved buffer subscription.
- [render.ts:ANCHOR_PREFIX] [render.ts:slugify] [render.ts:renderMarkdown] [render.ts:LinkTarget] [render.ts:joinPath] [render.ts:resolveLink] [render.ts:previewOffsetForLine] Markdown-to-sanitized-HTML pipeline with source-line annotations (data-source-line), link target classifier, and scroll offset interpolator.
- [mermaid.ts:MERMAID_SELECTOR] [mermaid.ts:themeKey] [mermaid.ts:subscribeTheme] [mermaid.ts:hasMermaid] [mermaid.ts:MermaidPass] [mermaid.ts:renderMermaidIn] [mermaid.ts:resetMermaidForTests] Lazy Mermaid diagram renderer with theme synchronization and interactive pan/zoom.

## Invariants
- [render.ts:purify] Moves <img src> to data-src during sanitization so remote images are blocked and local file images are loaded via IPC commands.fsReadImage (IMAGE_CACHE_LIMIT = 64).
- [mermaid.ts:enqueue] [mermaid.ts:renderMermaidIn] Serializes mermaid.render calls through a single promise queue (Mermaid is not re-entrant across config changes), preserves stale SVG nodes while re-rendering to prevent flicker, and removes temporary measurement DOM nodes (d<id>, i<id>, <id>).

## Side Effects
- [modes.ts:setMarkdownMode] Writes openidea.markdown.defaultMode to localStorage.
- [MarkdownEditor.tsx:imageUrl] [MarkdownEditor.tsx:openExternal] Reads local images via IPC commands.fsReadImage and opens external links via @tauri-apps/plugin-opener or window.open.
- [mermaid.ts:subscribeTheme] Observes root theme attribute mutations via MutationObserver and mutates preview DOM placeholders with rendered SVGs.

## Verification
- Run: `pnpm test`
