# viewers

## Overview
Non-text file editors (zoomable/pannable ImageViewer and paged virtualized read-only HexViewer for binary/large files) and Markdown editor/preview integration.

## Interface
- [index.ts:activate] Registers ImageViewer and HexViewer custom editor kinds, Markdown text editor wrapper, and image/markdown actions.
- [fileKinds.ts:IMAGE_KIND] [fileKinds.ts:BINARY_KIND] [fileKinds.ts:isImagePath] [fileKinds.ts:isSvgPath] [fileKinds.ts:isMarkdownPath] [fileKinds.ts:imageInput] [fileKinds.ts:BinaryInputData] [fileKinds.ts:binaryInput] [fileKinds.ts:wantsHexView] [fileKinds.ts:formatSize] File extension/probe classifiers and EditorInput constructors.
- [ImageViewer.tsx:ImageViewerHandle] [ImageViewer.tsx:useImageViewers] [ImageViewer.tsx:toggleChessboard] [ImageViewer.tsx:ImageViewer] [imageZoom.ts:MIN_ZOOM] [imageZoom.ts:MAX_ZOOM] [imageZoom.ts:clampZoom] [imageZoom.ts:stepZoom] [imageZoom.ts:fitZoom] [imageZoom.ts:initialZoom] [imageZoom.ts:zoomLabel] [imageZoom.ts:View] [imageZoom.ts:zoomAt] Image viewer component and zoom math.
- [HexViewer.tsx:HexViewer] [hex.ts:BYTES_PER_ROW] [hex.ts:PAGE_SIZE] [hex.ts:MAX_SCROLL_PX] [hex.ts:decodeBase64] [hex.ts:offsetDigits] [hex.ts:formatOffset] [hex.ts:hexByte] [hex.ts:asciiChar] [hex.ts:rowCount] [hex.ts:pageOf] [hex.ts:rowBytes] [hex.ts:pagesForRows] [hex.ts:HexViewport] [hex.ts:hexViewport] [hex.ts:scrollTopForRow] Virtualized 64 KB-paged hexadecimal/ASCII viewer.
- [forwardFocus.ts:useForwardFocus] [panZoom.ts:attachPanZoom] Shared focus forwarding hook and pointer/wheel pan-zoom controller.
- [markdown](./markdown/README.md) - Live Markdown preview, split editor/preview layout wrapper, GFM + DOMPurify HTML sanitizer, line-based scroll sync, and themed Mermaid diagram renderer with pan/zoom.

## Invariants
- [index.ts:activate] [fileKinds.ts:wantsHexView] viewers.image (order 10) runs before viewers.hex (order 50); wantsHexView claims files where probe.binary || probe.tooLarge.
- [hex.ts:rowBytes] [hex.ts:hexViewport] PAGE_SIZE (65536) is an exact multiple of BYTES_PER_ROW (16) so rows never straddle pages; virtual scroll height is capped at MAX_SCROLL_PX (8,000,000 px).
- [imageZoom.ts:stepZoom] Stepping zoom across 1 always snaps to 1 (100% actual size).

## Side Effects
- [ImageViewer.tsx:ImageViewer] Reads image data URLs and file probes via IPC commands.fsReadImage and commands.fsProbe, updates imageViewerActive/imageViewerFocus context keys, and persists openidea.imageViewer.chessboard in localStorage.
- [HexViewer.tsx:HexViewer] Fetches 64 KB file ranges via IPC commands.fsReadRange.

## Verification
- Run: `pnpm test`
