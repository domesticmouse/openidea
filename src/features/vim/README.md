# vim

## Overview
IdeaVim-style Vim emulation mode for CodeMirror editors with lazy engine loading, .openideavimrc/.ideavimrc parsing, IDE shortcut conflict resolution, status bar mode widget, and settings page.

## Interface
- [index.ts:VIM_SETTINGS_PAGE] [index.ts:activateSettingsPages] [index.ts:activate] Registers the Tools › Vim Emulation settings page, tools.vim toggle action, status bar widget, and lazy Vim engine activation.
- [engine.ts:vimExtension] [engine.ts:runIdeAction] [engine.ts:defineExCommands] [engine.ts:applyRcCommands] [engine.ts:_handleEx] CodeMirror @replit/codemirror-vim extension, Ex command bridge (:action, :w, :q, :e, :split, :vsplit), and rc command applier.
- [conflicts.ts:VimKey] [conflicts.ts:VIM_KEYS] [conflicts.ts:handlerFor] [conflicts.ts:ConflictRow] [conflicts.ts:conflictRows] [conflicts.ts:VIM_EDITOR_ATTR] [conflicts.ts:vimKeyClaim] Ctrl-key conflict table and global key-claim callback between Vim and the IDE.
- [rc.ts:MapMode] [rc.ts:RcCommand] [rc.ts:RcIgnored] [rc.ts:RcParseResult] [rc.ts:RC_FILES] [rc.ts:exOfRhs] [rc.ts:sourcedPaths] [rc.ts:parseVimrc] [rc.ts:IDEA_ACTIONS] [rc.ts:resolveActionId] [rcFile.ts:RcFile] [rcFile.ts:readRcFile] [rcFile.ts:readSourcedFiles] [rcFile.ts:RcStatus] [rcFile.ts:useVimRc] [rcFile.ts:loadRcFile] .openideavimrc / .ideavimrc parser, <Action>(...) resolver, and recursive source loader.
- [settings.ts:Handler] [settings.ts:VimSettings] [settings.ts:VIM_SETTINGS_KEY] [settings.ts:DEFAULT_VIM_SETTINGS] [settings.ts:parseVimSettings] [settings.ts:useVimSettings] [settings.ts:updateVimSettings] [settings.ts:setVimEnabled] [settings.ts:syncVimSettingsFromStorage] [mode.ts:VimModeState] [mode.ts:useVimMode] [mode.ts:modeLabel] [VimStatus.tsx:VimStatus] [VimSettingsPage.tsx:VimSettingsPage] Vim settings persistence, mode indicator state, status bar widget, and settings page.

## Invariants
- [index.ts:activate] Increments generation on each toggle/dispose so stale async ./engine or rc loads cannot register duplicate editor contributions.
- [engine.ts:escapeClosesPopupsFirst] In non-Visual modes, plain Escape runs editor-scope handlers (closing popups/lookup/find/snippets) before reaching Vim; in Visual mode, Escape always exits Visual mode.
- [rcFile.ts:readSourcedFiles] [rc.ts:expandSources] Caps sourced files at MAX_SOURCED_FILES (32) and rejects cyclic source inclusions ("file sources itself").

## Side Effects
- [settings.ts:updateVimSettings] [settings.ts:syncVimSettingsFromStorage] Reads/writes localStorage key openidea.vim.v1 and listens to window storage events.
- [rcFile.ts:readRcFile] [rcFile.ts:loadRcFile] Reads ~/.openideavimrc or ~/.ideavimrc and sourced files from disk via commands.fsReadText.
- [index.ts:activate] Updates vimMode context key and registers global key claims and CodeMirror editor contributions.

## Verification
- Run: `pnpm test`
