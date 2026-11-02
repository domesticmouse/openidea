# features

## Overview
Self-contained IDE feature modules (editor integrations, tool windows, settings pages, dialogs, and background services) orchestrated by activateFeatures and activateSettingsWindowFeatures.

## Interface
- [index.ts:activateFeatures] Activates all 40 IDE features in deterministic order (installing project-trust, editor, and app launch barriers before welcome restores the last session project) and returns a reverse-order disposer.
- [index.ts:activateSettingsWindowFeatures] [index.ts:SettingsWindow] [index.ts:settingsWindowParent] Activates only settings-page contributions and parent-window bridges when running inside a native child Settings window (settings-<parent>).
- [agents](./agents/README.md) - Coding agents integration (Claude Code, Codex, Gemini CLI, and ACP Registry agents) providing the Agents tool window, session and new-task editor tabs, prompt composer with @ file and / command completion, and the ACP Registry settings page.
- [analysis](./analysis/README.md) - Batch code analysis features including Inspect Code (batch language-server diagnostics), Code Cleanup, Locate Duplicates, and Structural Search & Replace (SSR).
- [app](./app/README.md) - Desktop shell integration providing the native OS menu bridge, multi-window and LightEdit lifecycle, window close/quit guards, window title sync, i18n locale sync, IDE updates, cache invalidation, and About / Keymap Reference / New Project / Workspace dialogs.
- [build](./build/README.md) - Build Output tool window, title bar build widget, and project build/rebuild orchestration over Cargo, Gradle, Maven, Go, npm, CMake, and javac.
- [build-tools](./build-tools/README.md) - Maven and Gradle build tool integration providing dynamic Maven/Gradle tool windows, lifecycle/task trees, background project sync, Execute Goal/Task popup and Run Anything provider, and the Maven Dependency Analyzer editor tab.
- [code-insight](./code-insight/README.md) - Code insight views and language-server navigation features providing the Problems tool window, Find Usages tool window and Show Usages popup (with syntax/text fallback in dumb mode), Type/Call Hierarchy tool window, language server status widget, Go build constraints status widget, and project tree problem decorations.
- [coverage](./coverage/README.md) - Code coverage execution, report importing, editor gutter stripe markers, project tree coverage badges, and the Coverage tool window.
- [database](./database/README.md) - Database tool window, query console editor extension, result-set grid and chart views, table data editor, data extractors/importers, and schema/object management dialogs for PostgreSQL, MySQL/MariaDB, SQLite, SQL Server, and wire-compatible databases.
- [debugger](./debugger/README.md) - Debug Adapter Protocol (DAP) frontend orchestrating debug session lifecycles, launch and attach configurations (Java/Kotlin, Python, JS/TS/Deno, Go, Rust/C/C++, .NET, Godot), JVM HotSwap class reloading, and Services tool window integration.
- [dependencies](./dependencies/README.md) - Dependency manifest editor assistance providing outdated-version quick fixes (Cargo.toml, package.json, pom.xml, build.gradle, go.mod, pyproject.toml, requirements.txt) and package registry version completions.
- [deployment](./deployment/README.md) - SFTP and local folder deployment integration providing Upload, Download, Sync with Deployed, Delete from Server, Automatic Upload on save, and the Remote Host SFTP browser tool window.
- [github](./github/README.md) - GitHub Pull Requests feature contribution registering the `pullRequests` tool window, `github.timeline` editor kind, VCS diff and in-editor review mode extensions, and GitHub workbench actions and menus.
- [http-client](./http-client/README.md) - HTTP Client for `.http` and `.rest` files providing syntax highlighting, per-request run gutter markers, environment selection, response/history inspection in the Services tool window, and Spring Web request generation.
- [libraries](./libraries/README.md) - Exposes detected project SDK and dependency source roots (Python site-packages, JDK src.zip, Node node_modules, Cargo, Go, Maven, Gradle) as a read-only External Libraries tree in the Project tool window.
- [macros](./macros/README.md) - Records, coalesces, persists, edits, and replays user action and editor keystroke macros (`Edit › Macros`), exposing saved macros as keybindable workbench actions.
- [notebook](./notebook/README.md) - Jupyter `.ipynb` notebook custom editor and file editor provider featuring serialized backend edit/undo/redo queues, streaming Tauri kernel execution channels, and DOMPurify/CSP output sandboxing.
- [perf](./perf/README.md) - Memory indicator status bar widget, cache-clearing actions, and developer performance diagnostics overlay measuring keystroke-to-paint latency, IPC round-trip times, and browser long tasks.
- [plugins](./plugins/README.md) - Settings › Plugins UI (Available local catalog and Installed tabs) and runtime bridge connecting the backend WASM plugin host (ide-plugin) to workbench actions, themes, notifications, and active editor text edits.
- [profiler](./profiler/README.md) - Sampling profiler run executor (profiler) and Profiler tool window rendering interactive flame graphs, top-down call trees, and sortable method lists.
- [project-tree](./project-tree/README.md) - Virtualized, lazily loaded Project tool window (project) supporting multiple view panes (Project, Project Files, Open Files, All Changed Files, Scratches and Consoles), file/folder creation, safe delete, reference-updating move/rename refactorings with preview and undo, OS clipboard/drag-and-drop integration, and directory role marking.
- [project-trust](./project-trust/README.md) - Project trust prompt dialog, Restricted Mode workbench banner, Security › Project Trust settings page, and trust management actions.
- [remote-dev](./remote-dev/README.md) - SSH Remote Development connection dialog (remote.connect), remote folder browser, disconnect action (remote.disconnect), and login-shell/devcontainer environment reload (remote.reloadEnvironment).
- [run](./run/README.md) - Run/Debug configuration management, auto-detection, context/gutter producers, before-launch tasks, remote run targets (Docker/SSH/WSL), Services provider integration, and the Run tool window with ANSI/terminal console, test tree, and stack trace analyzer.
- [run-anything](./run-anything/README.md) - Run Anything popup (⌃⌃) for executing arbitrary shell commands in the project directory, run configurations, or build tool goals contributed via registerRunAnythingProvider.
- [scratch](./scratch/README.md) - Scratch Files (⇧⌘N) stored in <appData>/scratches, registered as a Scratches root in the Project view, with a language chooser popup prioritizing recently used languages.
- [search](./search/README.md) - Top-level search, navigation, bookmarks, recent files/locations, and structure feature entry point wiring tool windows, overlays, editor gutter contributions, and keyboard actions across its 6 subpackages (./bookmarks/README.md, ./common/README.md, ./everywhere/README.md, ./find/README.md, ./recent/README.md, ./structure/README.md).
- [services](./services/README.md) - Services tool window (services) hosting extensible service views and providers, with built-in Docker daemon/registry management, container log/inspect streaming, Dockerfile/Compose editor gutter runners, and Docker run configuration types.
- [settings](./settings/README.md) - Settings feature entry point activating the Settings dialog/window, runtime settings synchronization (keymap, UI font, zoom, compact mode, idle save, alt-drag guard), File Type overrides, Python interpreter status, and Search Everywhere settings indexing.
- [ssh](./ssh/README.md) - SSH credential prompt queue (askpass) and modal dialog for ssh/git child processes, plus Tools › Start SSH Session… chooser and terminal session launcher.
- [templates](./templates/README.md) - Templates feature entry point integrating Live Templates (⌘J, Tab), Surround With (⌥⌘T), Postfix Completion, Emmet (Tab, Update Tag), and File Templates (New ▸ …) with CodeMirror 6, project menus, and Settings.
- [terminal](./terminal/README.md) - Integrated xterm.js terminal tool window and embeddedTerminal provider with lazy runtime loading, tabs/splits, shell/CWD selection, scrollback search, clickable URL/file links, theme/font synchronization, OS file drop, remote image paste, and IME deduplication.
- [todo](./todo/README.md) - Project-wide and scoped TODO index (search_start), in-editor TODO comment highlighter (todoEditorExtension), TODO tool window (Project, Current File, Scope Based, Changes tabs with source preview), and Settings › Editor › TODO page.
- [tools](./tools/README.md) - Language server, debug adapter, and formatter installer/configurator (Settings › Languages & Frameworks › Language Servers), missing server install prompts, and one-click Eclipse Temurin JDK downloader.
- [vcs](./vcs/README.md) - Git version control, changelists, shelf, stash, interactive rebase, branch management, side-by-side/unified diff viewer, 3-way merge conflict resolver, gutter change markers, blame annotations, and Local History.
- [viewers](./viewers/README.md) - Non-text file editors (zoomable/pannable ImageViewer and paged virtualized read-only HexViewer for binary/large files) and Markdown editor/preview integration.
- [vim](./vim/README.md) - IdeaVim-style Vim emulation mode for CodeMirror editors with lazy engine loading, .openideavimrc/.ideavimrc parsing, IDE shortcut conflict resolution, status bar mode widget, and settings page.
- [web](./web/README.md) - HTML/SVG browser launcher (Open In › Browser via the built-in web server) and side-by-side live HTML preview bar (WebEditor).
- [welcome](./welcome/README.md) - Welcome screen (Projects, Customize, Plugins, Learn), Recent Projects popup and title bar project switcher, and automatic last-session project reopening on startup.

## Invariants
- [index.ts:activateFeatures] Isolates activation failures per feature with try/catch so a single failing feature never aborts startup of remaining features, and disposes active features in reverse registration order.
- [index.ts:features] Activates project-trust, editor, editor-lsp, and app before welcome so the startup launch barrier and editor service are registered before reopenLastProject() runs.

## Side Effects
- [index.ts:activateFeatures] [index.ts:activateSettingsWindowFeatures] Invokes each feature's activate / activateSettingsPages entrypoint and logs any activation error via console.error.

## Verification
- Run: `pnpm test`
