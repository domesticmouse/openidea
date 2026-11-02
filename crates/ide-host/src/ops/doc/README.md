# doc

## Overview
Implements document, syntax-structure, and file-level local history RPC/IPC command handlers for `ide_host::ops::doc`.

## Interface
- `pub async fn blocking<T, F>(f: F) -> IdeResult<T>` (`[commands.rs:blocking]`)
- `pub async fn doc_open_virtual(host: &Host, window: &str, uri: String, title: String, language: Option<LanguageId>) -> IdeResult<DocumentSnapshot>` (`[commands.rs:doc_open_virtual]`)
- `pub fn doc_ui_mode(host: &Host, window: &str, doc: DocId) -> IdeResult<String>` (`[commands.rs:doc_ui_mode]`)
- `pub fn doc_set_encoding(host: &Host, window: &str, doc: DocId, encoding: String) -> IdeResult<DocumentSnapshot>` (`[commands.rs:doc_set_encoding]`)
- `pub fn doc_set_line_ending(host: &Host, window: &str, doc: DocId, line_ending: LineEnding) -> IdeResult<()>` (`[commands.rs:doc_set_line_ending]`)
- `pub async fn doc_outline(host: &Host, window: &str, doc: DocId) -> IdeResult<Vec<DocumentSymbol>>` (`[commands.rs:doc_outline]`)
- `pub async fn doc_folding(host: &Host, window: &str, doc: DocId) -> IdeResult<Vec<FoldingRange>>` (`[commands.rs:doc_folding]`)
- `pub async fn doc_highlights(host: &Host, window: &str, doc: DocId, pos: Pos) -> IdeResult<Vec<DocumentHighlight>>` (`[commands.rs:doc_highlights]`)
- `pub async fn doc_selection_ranges(host: &Host, window: &str, doc: DocId, positions: Vec<Pos>) -> IdeResult<Vec<SelectionRange>>` (`[commands.rs:doc_selection_ranges]`)
- `pub async fn history_list(host: &Host, window: &str, path: PathBuf) -> IdeResult<Vec<HistoryEntry>>` (`[commands.rs:history_list]`)
- `pub async fn history_content(host: &Host, window: &str, id: String) -> IdeResult<String>` (`[commands.rs:history_content]`)
- `pub(crate) fn register(r: &mut RpcRegistry)` (`[commands.rs:register]`)

## Invariants
- Documents marked `snap.large` skip tree-sitter outline, folding, document highlights, and syntax-tree selection ranges (`[commands.rs:doc_outline]`, `[commands.rs:doc_folding]`, `[commands.rs:doc_highlights]`, `[commands.rs:doc_selection_ranges]`).

## Side Effects
- Fetches virtual document content and document symbols from LSP servers (`[commands.rs:doc_open_virtual]`, `[commands.rs:doc_outline]`).
- Mutates open document encoding and line endings in `Documents` (`[commands.rs:doc_set_encoding]`, `[commands.rs:doc_set_line_ending]`) and reads snapshots from `LocalHistory` (`[commands.rs:history_list]`, `[commands.rs:history_content]`).

## Verification
- Run: `cargo test -p ide-host`
