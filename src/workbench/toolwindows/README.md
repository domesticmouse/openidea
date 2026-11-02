# toolwindows

## Overview
Tool window stripes, primary and split island panels, drag-to-dock placement, resize handles, layout persistence, and Window > Tool Window Layouts actions.

## Interface
- [ToolWindows.tsx:ToolWindowLayout]: Renders the left/right tool window stripes, primary/split side panels, resize handles, and center editor area.
- [ToolWindows.tsx:ToolWindowStripe]: Renders a side stripe with draggable tool window buttons, badges, and 'More Tool Windows' menu.
- [store.ts:useToolWindowLayout]: Zustand store tracking visible windows per slot ('left', 'left-split', 'bottom', 'bottom-split', 'right', 'right-split'), sizes, split ratios, placements, and focus.
- [store.ts:toolWindowServiceImpl]: Implementation of ToolWindowService (show, hide, toggle, isVisible) provided to the workbench.
- [store.ts:loadToolWindowLayout]: Loads and reconciles per-project tool window layout from localStorage.
- [actions.ts:installToolWindowActions]: Auto-registers 'toolwindow.<id>' toggle actions for every contributed tool window plus Hide All, Jump to Last, Stretch, and Layout management actions.
- [layouts.ts:useToolWindowLayouts]: Saves, restores, renames, and deletes named tool window layouts in localStorage.
- [content.ts:contentTabRegistry]: Registry allowing tool windows with header tabs to handle Close Tool Window Tab (toolwindow.closeContent).

## Invariants
- [store.ts:useToolWindowLayout]: Each of the 6 ToolWindowSlots ('left', 'left-split', 'bottom', 'bottom-split', 'right', 'right-split') shows at most one tool window at a time; showing another window in the same slot replaces it.
- [store.ts:persist]: Entering Distraction Free Mode stashes visible windows in distractionFreeStash; persistence always writes distractionFreeStash ?? visible so temporary hiding is never saved as the project layout.

## Side Effects
- [store.ts:update]: Persists layout snapshots to localStorage under 'openidea.layout.v1:<scope>' and updates 'toolWindow.<id>.visible', 'toolWindowFocus', and 'activeToolWindow' context keys.

## Verification
- Run: `pnpm test`
