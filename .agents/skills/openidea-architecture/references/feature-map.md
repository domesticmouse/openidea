# OpenIDEA Frontend Layer & Feature Map (`src/`)

Every frontend directory has a `README.md` contract documenting its exported symbols (`Interface`), `Invariants`, `Side Effects`, and `Verification` command (`pnpm test`).

## 1. Core Frontend Layers (`src/`)

| Layer | Contract Path | Subsystems & Responsibility |
| :--- | :--- | :--- |
| **Root (`src`)** | `src/README.md` | `main.tsx` bootstrap, `bindings.ts` (auto-generated `tauri-specta` IPC types & `commands`). |
| **Platform (`src/platform`)** | `src/platform/README.md` | `ipc.ts`, `tauriCore.ts`, `remote.ts`, `actions.ts`, `context.ts`, `keymap.ts`, `keymaps/` (`src/platform/keymaps/README.md`), `project.ts`, `projectRoots.ts`, `settings.ts`, `workflowSettings.ts`, `editorSettings.ts`, `i18n.ts` (`src/platform/locales/README.md`). |
| **UI (`src/ui`)** | `src/ui/README.md` | Shared React 19 primitives (`Tree`, `List`, `Dialog`, `Popup`, `Menu`, `Tabs`, `Splitter`, `Input`, `Select`, `Button`, `Checkbox`, `Badge`, `Tooltip`) and icon registries (`src/ui/icons/README.md`). |
| **Workbench (`src/workbench`)** | `src/workbench/README.md` | "Islands" shell (`Workbench.tsx`, `TitleBar.tsx`, `ToolWindows.tsx`, `EditorArea.tsx`, `StatusBar.tsx`, `NavigationBar.tsx`, `Notifications.tsx`), contribution registries (`contributions.ts`), background tasks (`tasks.ts`), and window management (`projectWindow.ts`). |
| **Editor (`src/editor`)** | `src/editor/README.md` | Public feature extension API (`api.ts`), CodeMirror 6 core & `DocSync` (`src/editor/core/README.md`, `src/editor/core/cm/README.md`, `src/editor/core/model/README.md`, `src/editor/core/sync/README.md`), LSP client integration (`src/editor/lsp/README.md`), navigation (`src/editor/navigation/README.md`), structure outline (`src/editor/structure/README.md`), and refactorings (`src/editor/refactor/README.md`). |

---

## 2. Feature Modules (`src/features/`, 38 Packages)

Activated by `activateFeatures()` (and `activateSettingsWindowFeatures(parent)` for settings-window pages) in `src/features/index.ts` (`src/features/README.md`).

| Feature | Contract Path | Subdirectories | Summary |
| :--- | :--- | :--- | :--- |
| `agents` | `src/features/agents/README.md` | `chat`, `diff`, `model`, `panel`, `settings` | Coding agents tool window (Claude Code, Codex, ACP), isolated git worktree review, and inline diffs. |
| `analysis` | `src/features/analysis/README.md` | — | Inspect Code, Duplicate Code detection, and Structural Search & Replace. |
| `app` | `src/features/app/README.md` | — | Application lifecycle, File menu actions, LightEdit mode, multi-window management, auto-updater, and i18n locale activation. |
| `build` | `src/features/build/README.md` | — | Build Output tool window, title bar build button, and project build/rebuild orchestration. |
| `build-tools` | `src/features/build-tools/README.md` | — | Maven, Gradle, and Cargo tool windows, task runners, and build model sync. |
| `code-insight` | `src/features/code-insight/README.md` | — | Parameter info, Quick Documentation, Code Lens, Inlay Hints, Call/Type Hierarchy, Generate menu, and Problem detector. |
| `coverage` | `src/features/coverage/README.md` | — | Run with Coverage, gutter hit/miss markers, project tree coverage percentages, and Coverage tool window. |
| `database` | `src/features/database/README.md` | `console`, `data`, `diagram`, `model`, `tree` | Database tool window, data source configuration, SQL console, paginated table editor, and ER diagrams. |
| `debugger` | `src/features/debugger/README.md` | `breakpoints`, `data`, `editor`, `ui` | DAP debugger tool window, breakpoints dialog, inline variable values, evaluate expression, and JVM HotSwap. |
| `dependencies` | `src/features/dependencies/README.md` | — | Package/dependency manager tool window for Cargo, npm, Maven, Gradle, Go, and Python. |
| `deployment` | `src/features/deployment/README.md` | — | SFTP remote host browser, upload/download/sync actions, and auto-upload on save. |
| `github` | `src/features/github/README.md` | `api`, `create`, `data`, `details`, `diff`, `list`, `reviewMode`, `timeline`, `toolwindow`, `ui` | GitHub Pull Requests tool window, PR creation, timeline/checks, and in-editor review mode. |
| `http-client` | `src/features/http-client/README.md` | `spring` | `.http`/`.rest` editor gutter runner, environment selector, response viewer, and Spring endpoint discovery. |
| `libraries` | `src/features/libraries/README.md` | — | External Libraries & SDK root browsing in the Project tree and decompiled `.class` file viewing. |
| `macros` | `src/features/macros/README.md` | — | Editor action/typing macro recording, playback, and persistence. |
| `notebook` | `src/features/notebook/README.md` | — | Native `.ipynb` Jupyter notebook editor, cell execution, ZeroMQ kernel picker, and rich MIME output renderer. |
| `perf` | `src/features/perf/README.md` | — | Status bar memory indicator, cache invalidation, and developer frame/IPC latency diagnostics overlay. |
| `plugins` | `src/features/plugins/README.md` | — | Settings › Plugins manager (Catalog & Installed) and WASM plugin action/theme/editor bridge. |
| `profiler` | `src/features/profiler/README.md` | — | CPU profiler runner, flamegraph viewer, and top-down/bottom-up call tree tool window. |
| `project-tree` | `src/features/project-tree/README.md` | — | Project tool window (`Project`, `Packages`, `Project Files`, `Problems`, `Scratches`), file operations, and OS clipboard copy/paste. |
| `project-trust` | `src/features/project-trust/README.md` | — | Restricted Mode untrusted-project banner, trust prompt dialog, and `projectTrusted` context synchronization. |
| `remote-dev` | `src/features/remote-dev/README.md` | — | SSH Remote Development connection wizard, `openidea-server` bootstrap progress, and status bar latency/connection indicator. |
| `run` | `src/features/run/README.md` | — | Run/Debug configuration selector, gutter test/main runners, Run tool window, and structured test tree. |
| `run-anything` | `src/features/run-anything/README.md` | — | Double-`Ctrl` "Run Anything" popup for ad-hoc commands, run configs, and recent projects. |
| `scratch` | `src/features/scratch/README.md` | — | Scratch files and scratch buffers stored in global app config directory. |
| `search` | `src/features/search/README.md` | `bookmarks`, `common`, `everywhere`, `find`, `recent`, `structure` | Search Everywhere (`⇧⇧`), Find/Replace in Files (`⇧⌘F`/`⇧⌘R`), Bookmarks, Recent Files (`⌘E`), and Structure tool window. |
| `services` | `src/features/services/README.md` | — | Services tool window aggregating Docker containers/images/compose, Run configurations, and connections. |
| `settings` | `src/features/settings/README.md` | `components`, `dialog`, `keymap`, `languages`, `model`, `pages`, `pages/editor`, `theme`, `window` | Settings modal dialog (`⌘,`), native child Settings window, keymap editor, SDK/Python/JDK management, and runtime theme/font/zoom sync. |
| `ssh` | `src/features/ssh/README.md` | — | `SSH_ASKPASS` / `GIT_ASKPASS` credential prompt queue and `Tools › Start SSH Session…` launcher. |
| `templates` | `src/features/templates/README.md` | `emmet`, `files`, `live`, `popup`, `postfix`, `settings` | Live Templates (`⌘J`, `Tab`), Surround With (`⌥⌘T`), Postfix Completion, Emmet, and File & Code Templates (`New ▸ …`). |
| `terminal` | `src/features/terminal/README.md` | — | Integrated xterm.js terminal tool window and `embeddedTerminal` provider with tabs, splits, links, and IME gate. |
| `todo` | `src/features/todo/README.md` | — | Project-wide/scoped TODO index, in-editor TODO comment highlighting, and TODO tool window. |
| `tools` | `src/features/tools/README.md` | — | Managed language server, debug adapter, and formatter installer (`Settings › Languages & Frameworks › Language Servers`) and missing-tool prompts. |
| `vcs` | `src/features/vcs/README.md` | `comparison` | Git integration, Commit & Git Log tool windows, changelists, shelf, stash, interactive rebase, branch widget, Diff/Merge/Local History editors, and gutter markers. |
| `viewers` | `src/features/viewers/README.md` | `markdown` | Zoomable `ImageViewer`, 64 KB-paged `HexViewer` for binary/large files, and live Markdown split preview (`src/features/viewers/markdown/README.md`). |
| `vim` | `src/features/vim/README.md` | — | IdeaVim-style emulation for CodeMirror, `.openideavimrc`/`.ideavimrc` parser, and IDE shortcut conflict handler. |
| `web` | `src/features/web/README.md` | — | `Open In › Browser` via built-in web server and live side-by-side HTML preview bar (`WebEditor`). |
| `welcome` | `src/features/welcome/README.md` | — | Welcome screen, Recent Projects popup, title bar project switcher, and startup session project restoration. |
