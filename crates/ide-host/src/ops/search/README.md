# search

## Overview
Implements workspace-wide replace-in-files, single-match replace preview, and regex/replacement validation commands.

## Interface
- `pub fn replace_in_files(host: &Host, window: &str, query: ReplaceQuery, channel: Channel<ReplaceEvent>) -> IdeResult<()>` (`[commands.rs:replace_in_files]`)
- `pub async fn replace_preview(host: &Host, window: &str, query: SearchQuery, replacement: String, path: PathBuf, line: u32, column: u32) -> IdeResult<Option<String>>` (`[commands.rs:replace_preview]`)
- `pub fn search_validate(query: SearchQuery, replacement: Option<String>) -> Option<String>` (`[commands.rs:search_validate]`)
- `pub(crate) fn register(r: &mut RpcRegistry)` (`[commands.rs:register]`)

## Invariants
- `DocumentsHost::edit_file` retries up to 3 times if concurrent edits advance the document version, leaves already-open buffers unsaved for undo, and saves/closes files opened transiently (`[commands.rs:DocumentsHost::edit_file]`).

## Side Effects
- Applies text replacements across open documents and disk files, spawns a background thread for `ide_search::replace_all`, and streams `ReplaceEvent` progress over `Channel` (`[commands.rs:replace_in_files]`).

## Verification
- Run: `cargo test -p ide-host`
