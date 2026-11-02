# ui

## Overview
Reusable React design-system primitives, icons, keyboard-navigable Tree/List/Tabs/Menu/Dialog/Popup components, split panels, pointer drag-and-drop, and fuzzy matching.

## Interface
- [index.ts:Icon]: Exports all UI primitives from './icons', './Button', './FloatingToolbar', './Kbd', './Tooltip', './Input', './Checkbox', './Select', './Tabs', './Tree', './List', './Popup', './Menu', './Dialog', './Splitter', './SourcePreview', './Feedback', './HighlightedText', and './BarButton'.
- [pointerDrag.ts:startPointerDrag]: Pointer-event drag-and-drop engine (startPointerDrag, registerDropTarget, FILES_DRAG) working around webview native file-drop limitations.
- [lazy.ts:lazyComponent]: Wraps dynamic component imports with background preloading via prefetchLazyComponents().
- [match.ts:fuzzyMatch]: Fuzzy and word matching scorers for speed-search and completion lists.
- [popupState.ts:acquirePopup]: Manages popup stacking and sets the popupOpen context key.

## Invariants
- [popupState.ts:acquirePopup]: Opening any Popup, Menu, or Dialog must acquire a popup token so the global popupOpen context key accurately reflects active overlays.
- [pointerDrag.ts:startPointerDrag]: Internal drag-and-drop uses pointer events rather than HTML5 dragevents so Tauri's native OS file-drop handler does not swallow in-app drags.

## Side Effects
- [pointerDrag.ts:startPointerDrag]: Attaches window pointermove/pointerup/keydown listeners and renders floating drag ghost elements in document.body.
- [lazy.ts:prefetchLazyComponents]: Schedules background dynamic imports via requestIdleCallback / timeouts after initial render.

## Verification
- Run: `pnpm test`
