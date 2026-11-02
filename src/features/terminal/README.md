# terminal

## Overview
Integrated xterm.js terminal tool window and embeddedTerminal provider with lazy runtime loading, tabs/splits, shell/CWD selection, scrollback search, clickable URL/file links, theme/font synchronization, OS file drop, remote image paste, and IME deduplication.

## Interface
- [index.ts:activate] [loader.ts:TerminalRuntime] [loader.ts:terminalRuntime] [loader.ts:loadTerminalRuntime] [runtime.ts:activeSession] [runtime.ts:applySettingsToAll] [runtime.ts:applyThemeToAll] [runtime.ts:closeAll] [runtime.ts:closePane] [runtime.ts:newTab] [runtime.ts:newTabAt] [runtime.ts:requestClosePane] [runtime.ts:sessionForTerminalId] [runtime.ts:sessionFromElement] [runtime.ts:splitActive] [runtime.ts:startDirectory] [runtime.ts:useTerminals] [runtime.ts:TerminalTabs] [runtime.ts:TerminalView] [runtime.ts:applyThemeToEmbedded] [runtime.ts:createEmbeddedTerminal] [runtime.ts:closeTerminalFind] [runtime.ts:openTerminalFind] [runtime.ts:useTerminalFind] Feature activator, lazy runtime loader, and re-exported runtime API.
- [keys.ts:TerminalKeyResult] [keys.ts:TERMINAL_ALLOWED_ACTIONS] [keys.ts:isAllowedIdeShortcut] [keys.ts:terminalKeyAction] [keys.ts:installTerminalKeys] Capture-phase keyboard interceptor routing shortcuts between xterm and the IDE.
- [store.ts:SplitOrientation] [store.ts:PaneLayout] [store.ts:PaneSplit] [store.ts:TerminalTab] [store.ts:useTerminals] [store.ts:tabTitle] [store.ts:tabFullTitle] [store.ts:getSession] [store.ts:sessionForTerminalId] [store.ts:startDirectory] [store.ts:newTab] [store.ts:newTabAt] [store.ts:closeTab] [store.ts:requestCloseTab] [store.ts:requestClosePane] [store.ts:closeAll] [store.ts:selectTab] [store.ts:cycleTab] [store.ts:renameTab] [store.ts:focusPane] [store.ts:cyclePane] [store.ts:splitActive] [store.ts:closePane] [store.ts:activeSession] [store.ts:sessionFromElement] [store.ts:applySettingsToAll] [store.ts:applyThemeToAll] [store.ts:publishFocus] Terminal tab and recursive split-pane state store.
- [session.ts:SessionStatus] [session.ts:SessionSnapshot] [session.ts:SessionOptions] [session.ts:TerminalSearchOptions] [session.ts:TerminalSearchResult] [session.ts:LINK_HANDLER] [session.ts:openExternal] [session.ts:openFileLink] [session.ts:fontFor] [session.ts:osc7Path] [session.ts:stripOsc7] [session.ts:TerminalSession] [embedded.ts:applyThemeToEmbedded] [embedded.ts:createEmbeddedTerminal] xterm.js + backend PTY session lifecycle and embedded terminal factory.
- [TerminalView.tsx:TerminalView] [TerminalView.tsx:TerminalTabs] [FindBar.tsx:useTerminalFind] [FindBar.tsx:openTerminalFind] [FindBar.tsx:closeTerminalFind] [FindBar.tsx:TerminalFindBar] Tool window view, tab bar, and scrollback search bar.
- [theme.ts:parseColor] [theme.ts:Rgba] [theme.ts:toHex] [theme.ts:mix] [theme.ts:MIN_CONTRAST_RATIO] [theme.ts:TokenReader] [theme.ts:buildTerminalTheme] [theme.ts:readTerminalTheme] [theme.ts:readSearchDecorations] [theme.ts:TerminalFontSettings] [theme.ts:EditorFont] [theme.ts:TerminalFont] [theme.ts:withNerdFonts] [theme.ts:terminalFont] [theme.ts:remeasureWhenFontsLoad] [links.ts:TerminalLink] [links.ts:trimUrl] [links.ts:findTerminalLinks] [links.ts:resolvePath] [fit.ts:FIT_INTERVAL_MS] [fit.ts:PROCESS_RESIZE_DELAY_MS] [fit.ts:FitScheduler] [fit.ts:fitScheduler] [fit.ts:settledResize] [drop.ts:droppedPathsText] [drop.ts:installTerminalDrop] [paste.ts:RemotePasteTarget] [paste.ts:installTerminalPaste] [paste.ts:pasteTerminalClipboard] [imeCommit.ts:IME_COMMIT_TIMEOUT_MS] [imeCommit.ts:ImeScheduler] [imeCommit.ts:ImeCommitGate] ANSI palette/font helpers, URL/file link detector, resize coalescer, OS file drop, clipboard/remote image paste, and IME duplicate-commit gate.

## Invariants
- [loader.ts:loadTerminalRuntime] Lazily loads runtime.ts (and @xterm/xterm) only on first terminal or embedded terminal use.
- [keys.ts:installTerminalKeys] Runs in capture phase on window before the global keyboard dispatcher; when overrideIdeShortcuts is enabled, Ctrl/Escape strokes bound to TERMINAL_ALLOWED_ACTIONS still pass through to the IDE.
- [imeCommit.ts:ImeCommitGate] Deduplicates repeated onData emissions from a single WebKitGTK/fcitx5 IME commit while standing down (native = true) when compositionstart is fired.

## Side Effects
- [session.ts:TerminalSession] [paste.ts:pasteImage] Spawns/writes/resizes/kills backend PTYs (commands.termSpawn, commands.termWrite, commands.termResize, commands.termKill, commands.termStoreImage).
- [index.ts:activate] Registers the terminal tool window, embeddedTerminal provider, terminal.* actions, capture-phase key listener, and MutationObserver on document.documentElement.

## Verification
- Run: `pnpm test`
