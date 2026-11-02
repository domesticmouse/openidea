# src

## Overview
OS file clipboard access (`CF_HDROP` on Windows, `NSPasteboard` file URLs on macOS) so Project View copy/paste interoperates with Finder and Explorer.

## Interface
- [lib.rs:files] — Reads file paths currently on the OS clipboard (`Vec<PathBuf>`).
- [lib.rs:has_files] — Checks whether the OS clipboard currently holds file references without decoding all paths.
- [lib.rs:set_files] — Places `paths` onto the OS clipboard as both native file references (`CF_HDROP` / `NSURL`) and newline-joined path strings.
- [lib.rs:encode] / [lib.rs:decode] — Encodes and decodes Win32 `CF_HDROP` (`DROPFILES` struct + UTF-16LE path list).

## Invariants
- [lib.rs:files] / [lib.rs:has_files] / [lib.rs:set_files] — On macOS, callers must invoke these functions on the main thread (`MainThreadMarker::new()` returns `None` and yields empty/`false` off the main thread).
- [lib.rs:encode] / [lib.rs:decode] — Uses a 20-byte `DROPFILES` header (`HEADER = 20`, `fWide = 1` at offset 16) followed by NUL-separated UTF-16LE paths and a trailing NUL.
- [lib.rs:set_files] — Returns `false` immediately without mutating the clipboard when `paths` is empty.

## Side Effects
- [lib.rs:files] / [lib.rs:has_files] / [lib.rs:set_files] — Opens and mutates the system clipboard via Win32 (`OpenClipboard`, `EmptyClipboard`, `GetClipboardData`, `SetClipboardData`, `GlobalAlloc`) on Windows and `NSPasteboard::generalPasteboard` on macOS.

## Verification
- Run: `cargo test -p ide-clipboard`
