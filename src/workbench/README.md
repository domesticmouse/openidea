# workbench

## Overview
The 'Islands' IDE workbench shell (TitleBar, ToolWindows, EditorArea, StatusBar, OverlayHost, Notifications) and central extension registries (contributions.ts).

## Interface
- [Workbench.tsx:Workbench]: Root shell component switching between the framed project layout, LightEditFrame, and Welcome screen.
- [activate.ts:activateWorkbench]: Initializes tool window and notification services, project/layout sync, focus tracking, task/notification event listeners, browser guards, OS file drop, and built-in shell actions/overlays.
- [activate.ts:activateSecondaryWindow]: Initializes shell services for secondary windows (such as the native Settings window).
- [contributions.ts:useContributions]: Central Zustand registry and registration functions (registerToolWindow, registerStatusItem, registerTitleItem, registerOverlay, registerWorkbenchBanner, registerEditorKind, registerFileEditorProvider, registerTextEditorWrapper, registerServiceProvider, registerServiceView, setFileDecorations, registerSettingsPage).
- [contributions.ts:editorService]: Service accessors and providers (editorService, toolWindowService, notifications, embeddedTerminal).
- [toolwindows](./toolwindows/README.md) - Tool window stripes, primary and split island panels, drag-to-dock placement, resize handles, layout persistence, and Window > Tool Window Layouts actions.

## Invariants
- [contributions.ts:useContributions]: src/workbench/* never imports feature modules (src/features/*); all feature UI is injected via contributions.ts registries and service providers.
- [contributions.ts:resolveFileEditor]: FileEditorProvider resource acquisitions must commit synchronously on tab admission or roll back completely if resolution falls through or fails.

## Side Effects
- [activate.ts:activateWorkbench]: Subscribes to backend IdeEvent::Task and IdeEvent::Notify events, installs window focus/drop/keyboard browser guards, and synchronizes project open state with context keys.

## Verification
- Run: `pnpm test`
