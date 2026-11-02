---
name: openidea-frontend-contributions
description: >-
  Guide for building and extending frontend features in OpenIDEA (src/features/*,
  src/workbench/contributions.ts, src/editor/api.ts, src/platform/actions.ts,
  src/features/settings/*). Use this skill when adding or modifying tool windows,
  actions, keybindings, context keys, status bar widgets, settings pages, custom
  editor kinds, diff viewer providers, or CodeMirror 6 editor extensions.
---

# Frontend Features & Workbench Contributions in OpenIDEA

OpenIDEA's frontend (`src/`) uses a modular contribution architecture. Neither `src/workbench/` nor `src/editor/core/` imports feature modules (`src/features/*`). Instead, each feature exports an `activate(): () => void` function and registers its capabilities into central stores.

Before modifying an existing feature or subsystem, read its local `README.md` contract (e.g., `src/features/<name>/README.md`, `src/workbench/README.md`, `src/editor/README.md`).

## 1. Anatomy of a Feature (`src/features/<name>/index.ts`)

1. Create `src/features/<name>/index.ts` exporting `activate(): () => void`.
2. If the feature also provides pages for the native Settings window (`settings-<parent>`), export `activateSettingsPages(): () => void` and register it in both `activateFeatures` and `activateSettingsWindowFeatures` inside [`src/features/index.ts`](../../../src/features/index.ts).
3. Always collect and return disposers in reverse order:
   ```ts
   export function activate(): () => void {
     const disposers = [
       registerToolWindow({ ... }),
       registerActions([ ... ]),
       registerEditorContribution({ ... }),
       onIdeEvent("fsChanged", (e) => { ... }),
     ];
     return () => disposers.reverse().forEach((d) => d());
   }
   ```
4. Use `lazyComponent(() => import("./MyHeavyView").then((m) => m.MyHeavyView))` from `@/ui` for views not visible on initial startup so the startup bundle stays fast.

---

## 2. Actions, Context Keys & Keymaps (`src/platform/`)

- **Registering Actions (`@/platform/actions`)**:
  ```ts
  registerAction({
    id: "myFeature.doSomething",
    title: "Do Something",
    category: "Tools",
    icon: "run",
    when: "projectOpen && editorFocus",
    keyWhen: "!inPopup", // Extra guard applied only to keyboard shortcuts
    run: ({ ctx, arg }) => { ... },
  });
  ```
- **Context Keys (`@/platform/context`)**:
  - Set reactive context state via `setContext("myKey", true)`.
  - Evaluate expressions (`!a && (b || c == 'foo')`) via `when` and `keyWhen`.
- **Keybindings & Shortcut Interception (`@/platform/keymap` & `@/platform/keymaps/*`)**:
  - Default shortcuts live in keymap preset definitions (`intellij-macos.ts`, `intellij-windows.ts`, `vscode-macos.ts`, etc.), **not** inline on the action registration.
  - When building a shortcut recorder or modal key capture input, call `suspendKeyboardDispatch()` (`@/platform/keymap`) while focused so typed keys do not trigger global IDE actions.
  - Features that intercept editor or terminal keys (such as `src/features/vim/conflicts.ts` via `registerKeyClaim` or `src/features/terminal/keys.ts` in capture phase) must coordinate with `@/platform/keymap`.

---

## 3. Workbench Extension Points (`@/workbench/contributions`)

Import registration functions from `@/workbench/contributions`:

| Extension Point | Registration Function | Purpose |
| :--- | :--- | :--- |
| **Tool Window** | `registerToolWindow({ id, title, icon, anchor, order, secondary, stripe, component, headerActions, headerContent, headerMenu, titleMenu, useBadge, tabsReplaceTitle })` | Docks a panel on `"left"`, `"right"`, or `"bottom"` and auto-creates action `toolwindow.<id>`. |
| **Status Bar Widget** | `registerStatusItem({ id, align, priority, component, title, lightEdit })` | Adds a widget to the left or right of the bottom status bar. |
| **Title Bar Item** | `registerTitleItem({ id, align, priority, component, title })` | Adds a toolbar widget to the main window title bar. |
| **Overlay / Modal** | `registerOverlay({ id, component })` | Mounts a popup/dialog host above the workbench (Search Everywhere, Settings, etc.). |
| **Banner** | `registerWorkbenchBanner({ id, component })` | Renders a full-width notification banner below the title bar (e.g., Untrusted Project banner). |
| **Custom Editor Kind** | `registerEditorKind({ kind, component, title, icon, restorable, document })` | Registers non-text tabs (diff viewer, notebooks, images, hex viewer). Implement `EditorDocumentLifecycle` if it has unsaved/dirty state. |
| **File Editor Provider** | `registerFileEditorProvider({ id, order, resolve })` | Intercepts `openFile(path)` before the text editor to open binary/custom files (images, `.ipynb`, `.class`) in a custom editor kind. |
| **Text Editor Wrapper** | `registerTextEditorWrapper({ id, matches, component, tabActions })` | Wraps matching text editors (e.g. Markdown split preview, HTML browser preview). Must keep rendering `children` (hidden via CSS if needed) so the editor view stays mounted. |
| **Menu Group** | `registerMenuGroup({ id, menus, order, items })` | Contributes dynamic context/main menu items (`editor`, `editorTab`, `projectView`, `openIn`, `file.new.primary`, `editor.generate`, etc.). |
| **Services Tree** | `registerServiceProvider({ id, useRoots, watch, actions, menu, detail })` / `registerServiceView(...)` | Contributes nodes (Docker, Run configs, DB, HTTP) to the Services tool window. |
| **File Decorations** | `setFileDecorations(providerId, map)` / `useFileDecoration(path)` | Colors, badges, and error underlines on paths in Project Tree and Editor Tabs (`vcs`, `problems`). |
| **Settings Page** | `registerSettingsPage({ id, title, group, order, keywords, scope, scopeGroup, component })` | Adds a page to the `⌘,` Settings tree and Search Everywhere settings index. |

### Core Workbench Services
- `editorService()`: `openFile(path, options)`, `openInput(input, options)`, `activePath()`, `activeCursor()`, `saveAll()`, `reformat(path)`, `focus()`.
- `toolWindowService()`: `show(id, focus)`, `hide(id)`, `toggle(id)`, `isVisible(id)`.
- `notifications().notify({ level, title, body, actions, sticky, key, group })`: Displays notification balloons and logs to the Notifications tool window.
- `queueTask(queueId, spec)` / `upsertTask(spec)` (`@/workbench/tasks`): Runs serialized or background tasks with progress in the status bar.

---

## 4. Settings Pages & Staging (`src/features/settings/`)

Settings changes are **staged** until the user clicks **Apply** or **OK** (`src/features/settings/model/staging.ts`):
- **Built-in Declarative Pages**:
  - For general settings pages, use `PageSpec` / `OptionsPage` (`src/features/settings/pages/spec.ts`) or form controls from `src/features/settings/components/Form.tsx` (`Section`, `Row`, `SettingToggle`, `SettingNumber`, `SettingSelect`, `SettingText`, `useSetting`).
  - For Editor settings pages, use `editorPage(def)` (`src/features/settings/pages/editor/kit.tsx`) so controls automatically participate in Settings search highlighting and parent-checkbox enable/disable cascading.
- **Standalone / Feature Settings Pages**:
  - Register via `registerSettingsPage({ id, title, group, order, keywords, component })` in `activateSettingsPages()` so the page is available in both the modal Settings dialog and the native child `settings-<parent>` window.

---

## 5. Extending the CodeMirror 6 Editor (`@/editor/api`)

Features **must not** import from `@/editor/core/*`. Always import from `@/editor/api`:

- **Per-Editor Extensions**:
  ```ts
  registerEditorContribution({
    id: "myFeature.decorations",
    order: 100,
    embedded: false, // true to also load in editable diff/merge editors
    extensions: (ctx: EditorDocContext) => {
      if (ctx.virtual) return []; // Skip file-only features on virtual docs
      return [myStateField, myKeymap];
    },
  });
  ```
- **Ignoring External/Mirrored Edits**:
  - In CodeMirror `updateListener` or `StateField.update`, check `isExternalChange(tr)` (`remoteChange` from backend patches or `mirroredChange` from split panes) so you don't trigger typing-only side effects (like autocompletion) on remote updates.
- **Awaiting Backend Document Sync**:
  - Before invoking a backend command that reads the Rust document buffer at the current caret/selection (such as LSP format, rename, structure outline, or code actions), **always await** `await whenSynced(view)` (or `await whenAllSynced()`).
- **Built-in Visual Facets in `@/editor/api`**:
  - `scrollbarMarks`: Right-hand overview stripe marks (`ScrollbarMark`).
  - `gutterLaneMarkers`: 3px VCS/change bar lane right of line numbers (`LaneMarker`).
  - `codeLenses`: Clickable inlay hints rendered above declarations (`CodeLens`).
  - `intentionProviders` & `quickFixProviders`: Lightbulb context actions (`⌥↵`).
  - `registerSaveParticipant`: Pre-save hooks (e.g. format on save, organize imports) run on explicit `⌘S` / Save All.

---

## 6. Verification & Contract Maintenance

1. Update the feature's `README.md` contract (`## Interface`, `## Invariants`, `## Side Effects`) whenever you add or change exported functions, stores, actions, or invariants.
2. Run verification checks:
   - `pnpm typecheck`
   - `pnpm test`
   - `pnpm lint`
