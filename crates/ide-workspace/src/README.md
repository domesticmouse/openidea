# src

## Overview
Implements OpenIDEA's core workspace state: rope-backed open documents with UTF-16 incremental patching and undo/redo, layered TOML settings, content-addressed local history, `.editorconfig` evaluation, DevContainer configuration parsing, atomic multi-file moves with import/path rewriting, multi-root workspace files, and OS process/memory attribution.

## Interface
- [lib.rs:Documents], [lib.rs:DocumentInfo], [lib.rs:TextSnapshot], [lib.rs:DetectLanguage], [documents.rs:DocumentObserver], [documents.rs:DetectUiMode]: Thread-safe rope-backed document buffer store supporting open, incremental UTF-16 patch, undo/redo, reload from disk, atomic save, and ordered observer notifications.
- [lib.rs:SettingsScope], [lib.rs:SettingsStore]: Layered settings store merging embedded defaults (`defaults/settings.toml`), global user settings (`<config>/settings.toml`), and project settings (`<project>/.openidea/settings.toml`).
- [lib.rs:LocalHistory], [lib.rs:HistoryEntry], [lib.rs:HistoryKind], [lib.rs:DirectoryHistory], [lib.rs:Retention]: Content-addressed (`blake3`) per-project file history store supporting snapshots, labels, diffs, restoration, deletion recovery, and retention pruning.
- [lib.rs:EditorConfig], [lib.rs:EditorConfigProps], [lib.rs:FileDefaults]: Cached `.editorconfig` hierarchy resolver and new-file indentation/encoding/line-ending defaults.
- [lib.rs:FileMoves], [lib.rs:FileMove], [lib.rs:UndoOutcome], [text_refs.rs:move_replacements], [text_refs.rs:find_text_occurrences], [file_refs.rs:PathCompletion], [file_refs.rs:is_file_reference], [file_refs.rs:path_part], [file_refs.rs:base_dirs], [file_refs.rs:candidates], [file_refs.rs:resolve], [file_refs.rs:complete]: Relative file-path reference completion/resolution, move refactoring rewrite computation, and transactional multi-file move undo/redo.
- [lib.rs:RecentProjects], [lib.rs:RecentProject], [window_groups.rs:TabbingPreference], [window_groups.rs:WindowGroup], [window_groups.rs:WindowGroups], [window_groups.rs:should_group_new_project], [workspace.rs:FILE], [workspace.rs:Workspace]: Recent projects persistence, window tab-group persistence, and `.openidea/workspace.json` multi-folder workspace management.
- [caches.rs:InvalidateCachesOptions], [caches.rs:cache_dirs], [caches.rs:schedule], [caches.rs:pending], [caches.rs:cancel], [caches.rs:apply_pending]: Startup cache invalidation scheduling and directory cleanup.
- [devcontainer.rs:DevContainer], [devcontainer.rs:LifecycleCommand], [devcontainer.rs:find], [devcontainer.rs:load], [devcontainer.rs:parse], [devcontainer.rs:substitute], [devcontainer.rs:resolve_env]: `.devcontainer` discovery, JSONC parsing, variable substitution, and environment resolution.
- [memory.rs:ProcEntry], [memory.rs:ProcessTable], [memory.rs:MemoryCategory], [memory.rs:MemoryRoot], [memory.rs:ProcessMemory], [memory.rs:WebKitPids], [memory.rs:attribute], [memory.rs:footprint_bytes], [memory.rs:webkit_process_ids], [memory.rs:responsible_webkit_helpers], [process.rs:mark_start], [process.rs:uptime], [process.rs:before_main], [process.rs:process_age], [process.rs:resident_bytes], [process.rs:heap_bytes], [process.rs:total_memory_bytes]: Cross-platform process tree memory attribution and process startup/memory metrics.
- [dependencies](./dependencies/README.md) - Parses manifest dependencies, caches registry version lists in memory and on disk, and calculates `Cargo.toml` dependency version upgrades.
- [roots](./roots/README.md) - Computes and auto-detects project folder classifications (`source`, `test`, `resource`, `testResource`, `generated`, `excluded`) across user settings and Cargo, Maven, Gradle, Node/TypeScript, Python, Go, and unmanaged Java project layouts.

## Invariants
- [documents.rs:Documents]: Serializes all mutations and observer callbacks for a given path under a per-document `Slot` mutex so `DocumentObserver`s (`on_open`, `on_change`, `on_save`, `on_close`) observe strictly monotonic versions; binary and `too_large` documents are never forwarded to text observers; and `patch` rejects stale versions (`InvalidInput`) or modifications to `readonly` buffers (`PermissionDenied`).
- [settings.rs:SettingsStore]: Deep-merges `defaults -> global -> project`, strips `editor.customSchemes` from project-level settings so custom schemes remain global-only, and persists only the minimal diff relative to embedded defaults in `save_global`.
- [workspace.rs:Workspace::save], [workspace.rs:Workspace::validate_name]: Enforces `version == 1`, non-empty single-line workspace names, no duplicate folders, no workspace directory inside any linked project folder, and no nested workspaces.
- [history.rs:LocalHistory]: Deduplicates consecutive identical `blake3` content hashes for the same path, skips automatic snapshots for files > `AUTO_SNAPSHOT_LIMIT` (2 MiB) or containing NUL bytes, and caps directory restoration to paths strictly inside the project root (`[history.rs:safe_project_target]`).
- [file_moves.rs:FileMoves]: Caps undo/redo stacks at `MAX_RECORDS = 20` and performs all-or-nothing rollback of file renames and document edits if any step of an undo/redo batch fails.

## Side Effects
- [documents.rs:Documents]: Reads and atomically writes workspace files via `ide_vfs`, and emits `IdeEvent::DocOpened`, `IdeEvent::DocPatched`, `IdeEvent::DocSaved`, and `IdeEvent::DocClosed` via `SharedSink`.
- [settings.rs:SettingsStore], [recent.rs:RecentProjects], [window_groups.rs:WindowGroups], [workspace.rs:Workspace], [caches.rs]: Read and write `settings.toml`, `recent.json`, `window-groups.json`, `.openidea/workspace.json`, and `invalidate-caches.json` on disk.
- [history.rs:LocalHistory]: Appends to `<data-dir>/history/<project-hash>/index.jsonl`, writes content-addressed blobs under `blobs/`, and prunes expired blobs.
- [memory.rs:imp], [process.rs:imp]: Query OS kernel/process interfaces (macOS Mach `proc_pid_rusage` / `proc_listpids`, Linux `/proc`, Windows Toolhelp32 / `K32GetProcessMemoryInfo` / `GlobalMemoryStatusEx`).

## Verification
- Run: `cargo test -p ide-workspace`
