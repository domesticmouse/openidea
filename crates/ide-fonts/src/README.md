# src

## Overview
Enumerates installed system font families across macOS, Windows, and Linux/Unix and bundles macOS user font faces for WebView `@font-face` injection.

## Interface
- `pub struct FontFamily { pub name: String, pub monospace: bool }` (`[lib.rs:FontFamily]`)
- `pub fn families(refresh: bool) -> Arc<[FontFamily]>` (`[lib.rs:families]`)
- `pub fn normalize(raw: impl IntoIterator<Item = (String, bool)>) -> Vec<FontFamily>` (`[lib.rs:normalize]`)
- `pub fn parse_fc_list(output: &str) -> Vec<FontFamily>` (`[lib.rs:parse_fc_list]`)
- `pub fn user_font_data(family: &str) -> Vec<(String, &'static str, &'static str, Vec<u8>)>` (`[lib.rs:user_font_data]`)

## Invariants
- `normalize` trims family names, drops empty or `.`-prefixed hidden system UI names, OR-merges per-face `monospace` flags across case-insensitive duplicates, and sorts case-insensitively (`[lib.rs:normalize]`).
- `user_font_data` restricts file access to canonicalized paths under `/Library/Fonts` and `$HOME/Library/Fonts` (`[lib.rs:user_font_roots]`, `[lib.rs:user_font_path]`), selects at most 4 faces (weights 400 and 700, regular and italic) (`[lib.rs:family_faces]`), and rejects files replaced by symlinks (`[lib.rs:bundle_family]`).

## Side Effects
- Caches enumerated `FontFamily` slices in a static `Mutex<Option<Arc<[FontFamily]>>>` until `families(true)` forces a refresh (`[lib.rs:families]`).
- Queries CoreText `CTFontCollectionCreateFromAvailableFonts` and reads font files on macOS (`[lib.rs:imp::list]`, `[lib.rs:bundle_family]`), queries DirectWrite `GetSystemFontCollection` on Windows (`[lib.rs:imp::read]`), and spawns `fc-list` on other Unix platforms (`[lib.rs:imp::list]`).

## Verification
- Run: `cargo test -p ide-fonts`
