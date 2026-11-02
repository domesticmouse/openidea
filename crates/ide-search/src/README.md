# src

## Overview
Ripgrep-backed (`grep-searcher` + `ignore`) project-wide Find in Files, context-filtered search (comments/strings/code literals), word candidate scanning, and multi-file Replace in Files with preview and undo support.

## Interface
- [lib.rs:SearchQuery], [lib.rs:SearchContext], [lib.rs:SearchScope], [lib.rs:ProjectFileSet], [lib.rs:PathFilter], [lib.rs:LineMatch], [lib.rs:FileMatches], [lib.rs:SearchSummary], [lib.rs:Overlay], [lib.rs:LiteralKind], [lib.rs:LiteralRange], [lib.rs:Literals] — Core types for configuring, filtering, and streaming Find in Files queries and unsaved buffer overlays.
- [lib.rs:search], [lib.rs:search_filtered], [lib.rs:search_in_context], [lib.rs:validate_query], [lib.rs:replace_in_text] — Executes parallel or scoped file searches, validates regex/scope patterns, filters matches by `SearchContext` (`Any`, `InComments`, `InStringLiterals`, `ExceptComments`, `ExceptStringLiterals`, `ExceptCommentsAndStringLiterals`), and performs single-buffer regex replacement.
- [files.rs:ScopeFile], [files.rs:scope_files] — Enumerates candidate files across `Project`, `Directory`, `ScopePattern`, or `Files` scopes while merging open-document `Overlay` buffers.
- [mask.rs:FileMask] — Parses comma-separated glob masks (`*.rs, !*_test.rs`) into positive and negative `GlobSet` filters.
- [pattern.rs:ScopePattern] — Parses IntelliJ-style scope expressions (`file:src/**/*.rs&&!file:*test*`) with boolean `&&`, `||`, `!`, and parentheses.
- [replace.rs:ReplaceTarget], [replace.rs:ReplaceEvent], [replace.rs:ReplaceSummary], [replace.rs:ReplacePreview], [replace.rs:ReplaceHost], [replace.rs:preserve_case], [replace.rs:replacement_edits], [replace.rs:apply_edits], [replace.rs:preview], [replace.rs:replace_files] — Computes case-preserving replacement edits, generates diff previews, and applies batch replacements across open buffers and disk files.
- [words.rs:WordQuery], [words.rs:WordFiles], [words.rs:files_with_word] — Scans project files in parallel (with optional trigram pre-filtering) for whole-word identifier occurrences used by cross-file Find Usages and Rename.

## Invariants
- [engine.rs:MAX_MATCHES] Caps total reported matches per search at 20,000 (`MAX_MATCHES`) and sets `SearchSummary::truncated = true` when exceeded.
- [engine.rs:MAX_PREVIEW_CHARS] Truncates `LineMatch::line_text` previews to at most 400 characters and rebases `match_ranges` UTF-16 offsets onto the truncated slice.
- [replace.rs:apply_edits] Applies edits in reverse byte order and rejects overlapping byte spans with an error rather than corrupting text.
- [words.rs:MAX_FILES] Caps `files_with_word` results at 2,000 files (`MAX_FILES`) and marks `WordFiles::truncated = true` when the limit is hit.

## Side Effects
- [engine.rs:search_in_context], [words.rs:files_with_word] Spawns parallel `ignore::WalkParallel` directory walkers that read project files from disk.
- [replace.rs:replace_files] Mutates open documents and writes modified files to disk via `ReplaceHost` (`ide_vfs::write_atomic` in production).

## Verification
- Run: `cargo test -p ide-search`
