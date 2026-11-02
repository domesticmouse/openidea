# platform

## Overview
Frontend platform layer providing typed Tauri/remote IPC, action registration, context-key when evaluation, keyboard dispatch, project/settings stores, i18n, and path/font utilities.

## Interface
- [ipc.ts:call]: Unwraps tauri-specta Result promises into data or throws IpcError; exports commands, onIdeEvent, nextRequestId, and isCancelled.
- [actions.ts:registerAction]: Registers and executes IDE actions (registerAction, registerActions, executeAction, useActions, isActionEnabled).
- [context.ts:setContext]: Publishes reactive context keys and compiles/evaluates boolean when expressions (compileWhen, evaluateWhen).
- [keymap.ts:installKeyboardDispatcher]: Installs the global capture-phase keyboard and mouse shortcut dispatcher, chord/double-modifier state machine, and keymap preset/override store (useKeymap).
- [project.ts:useProject]: Zustand store and lifecycle actions (openProject, openLightEdit, closeProject, loadSettings) for the current window's project.
- [remote.ts:connectRemote]: Manages SSH remote development sessions, transparent command routing (remoteRouting), and recent remote projects.
- [tauriCore.ts:setInvokeRoute]: Vite-aliased @tauri-apps/api/core wrapper allowing remote windows to intercept invoke() calls.
- [i18n.ts:tx]: Localization store (useI18n) and message/template lookup helpers (t, tx, formatMessage).
- [keymaps](./keymaps/README.md) - Built-in keyboard and mouse shortcut preset tables for macOS and Windows/Linux across IntelliJ IDEA, VS Code, and Eclipse keymaps.

## Invariants
- [tauriCore.ts:invoke]: All frontend IPC calls pass through tauriCore.ts (aliased from @tauri-apps/api/core) so remote windows transparently route server commands via remote_invoke while keeping client UI settings local.
- [keymap.ts:installKeyboardDispatcher]: Runs in the capture phase so IDE shortcuts take precedence over CodeMirror defaults, while respecting registerKeyClaim handlers, IME composition (isImeKey), and Windows AltGr characters (isAltGrTyping).

## Side Effects
- [ipc.ts:onIdeEvent]: Subscribes to window-scoped Tauri 'ide://event' events and invokes backend Tauri commands.
- [keymap.ts:installKeyboardDispatcher]: Registers capture-phase keydown, keyup, mousedown, mouseup, auxclick, and blur listeners on window.
- [remote.ts:connectRemote]: Connects to remote openidea-server over SSH/JSON-RPC, updates global invoke routing, and persists recent remotes in localStorage.

## Verification
- Run: `pnpm test`
