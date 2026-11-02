# src

## Overview
Provides virtual filesystem operations, character encoding detection and conversion, `.properties` Unicode escape handling, atomic file writes, read-only ZIP/JAR archive browsing, directory tree comparison, and debounced filesystem event watching.

## Interface
- [lib.rs:EntryKind], [lib.rs:FileEntry], [lib.rs:LoadedText], [lib.rs:FsBatch]: Core types for directory listings, decoded text documents with encoding/line-ending metadata, and partitioned filesystem watch batches.
- [lib.rs:mtime_ms], [lib.rs:natural_cmp], [lib.rs:read_dir], [lib.rs:load_text], [lib.rs:load_text_with_fallback], [lib.rs:load_text_as], [lib.rs:save_text], [lib.rs:create_file], [lib.rs:create_file_bytes], [lib.rs:create_dir], [lib.rs:set_writable], [lib.rs:rename], [lib.rs:delete], [lib.rs:delete_permanently], [lib.rs:copy], [lib.rs:walk_files], [lib.rs:detect_encoding]: Public filesystem and text I/O functions.
- [archive.rs:ARCHIVE_EXTENSIONS], [archive.rs:is_archive_file], [archive.rs:split_archive_path], [archive.rs:is_archive_path], [archive.rs:archive_root], [archive.rs:archive_entry_path], [archive.rs:read_archive_dir], [archive.rs:read_archive_entry]: Virtual `<archive.jar>!/<entry>` path parsing and cached ZIP/JAR archive reading.
- [bytes.rs:MAX_RANGE_LEN], [bytes.rs:MAX_IMAGE_SIZE], [bytes.rs:FileProbe], [bytes.rs:ByteRange], [bytes.rs:probe], [bytes.rs:read_range], [bytes.rs:image_mime], [bytes.rs:read_data_url], [bytes.rs:base64]: Binary probing, bounded byte-range reads (up to 64 KiB), and base64 image data URL encoding (up to 20 MiB).
- [compare.rs:DirectoryCompareStatus], [compare.rs:DirectoryCompareEntry], [compare.rs:DirectoryCompareProgress], [compare.rs:compare_directories], [compare.rs:copy_merge]: Recursive directory comparison (by size, mtime, or content) and non-destructive directory tree merging.
- [encoding.rs:LARGE_FILE_THRESHOLD], [encoding.rs:READ_ONLY_THRESHOLD], [encoding.rs:MAX_OPEN_SIZE], [encoding.rs:decode_properties_escapes], [encoding.rs:encode_properties_ascii], [encoding.rs:save_properties_text], [encoding.rs:encode], [encoding.rs:write_atomic], [encoding.rs:write_atomic_strict]: File size thresholds, Java `.properties` ISO-8859-1 `\uXXXX` conversion, and atomic file persistence.
- [rules.rs:ExcludeMatcher]: Glob, gitignore, and default directory exclusion matcher for filesystem walks and watchers.
- [watch.rs:WatchMode], [watch.rs:Watcher]: Debounced recursive or flat filesystem watcher emitting `IdeEvent::FsChanged` and `IdeEvent::ExcludedDirsChanged`.

## Invariants
- [encoding.rs:MAX_OPEN_SIZE], [encoding.rs:READ_ONLY_THRESHOLD], [encoding.rs:LARGE_FILE_THRESHOLD]: `[encoding.rs:load_text_inner]` rejects files over 512 MiB (`MAX_OPEN_SIZE`), marks files over 50 MiB (`READ_ONLY_THRESHOLD`) as `too_large` (read-only without decoding full text), and marks files over 5 MiB (`LARGE_FILE_THRESHOLD`) as `large`.
- [encoding.rs:encode]: Returns `io::ErrorKind::InvalidInput` if any character in the text cannot be losslessly represented in the target `encoding_rs` charset.
- [ops.rs:create_file], [ops.rs:create_dir], [ops.rs:rename], [ops.rs:copy]: Refuse to overwrite an existing destination path (`AlreadyExists`), except when `[ops.rs:rename]` detects a case-only rename of the same underlying file via `[ops.rs:same_file]`.
- [lib.rs:FsBatch], [watch.rs:classify_batch]: Separate `.git` metadata events (`HEAD`, `index`, `packed-refs`, `MERGE_HEAD`, `REBASE_HEAD`, `CHERRY_PICK_HEAD`, `REVERT_HEAD`, `FETCH_HEAD`, `config`, `refs/**`, `worktrees/**`) into `vcs` while dropping `.git/objects`, `.git/logs`, `.git/hooks`, `.git/*.lock`, and `.openidea/changelists.json*` noise.

## Side Effects
- [encoding.rs:write_atomic], [encoding.rs:write_atomic_strict]: Write temporary sibling files (`.<name>.<pid>.<seq>.ide-tmp`), preserve permissions, and atomically rename over target files (falling back to direct write when atomic rename fails in `[encoding.rs:write_atomic]`).
- [ops.rs:delete], [ops.rs:delete_permanently]: Move files/directories to the OS trash via `trash::delete` or permanently remove them from disk.
- [archive.rs:CACHE]: Caches up to 8 open `ZipArchive<File>` handles keyed by `(PathBuf, mtime_ms, len)` in a process-global `Mutex`.
- [watch.rs:Watcher::start_with]: Spawns background OS file-watcher threads via `notify-debouncer-full` and emits `IdeEvent::FsChanged` and `IdeEvent::ExcludedDirsChanged` through `SharedSink`.

## Verification
- Run: `cargo test -p ide-vfs`
