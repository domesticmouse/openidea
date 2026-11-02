# src

## Overview
Identifier-aware spell checker backed by a bundled ~83k English wordlist plus technical terms, splitting camelCase/snake_case tokens and ranking Levenshtein suggestions.

## Interface
- [lib.rs:RegionKind], [lib.rs:Region], [lib.rs:TypoContext], [lib.rs:Typo], [lib.rs:SpellScope], [lib.rs:MAX_TYPOS] — Types describing prose/identifier regions, typo locations (`Range` + `TypoContext`), and spell-checking scope.
- [lib.rs:Speller] — Spell-checking engine (`new`, `is_correct`, `is_correct_identifier`, `suggest`, `check`) combining the static bundled dictionary (`DICT`) with user/project custom word sets.
- [lib.rs:parse_word_list] — Parses newline-separated custom dictionary files (`.idea/dictionaries/*.xml` or wordlists), ignoring `#` comments and lowercasing words.
- [lib.rs:prose_regions] — Fallback scanner that extracts comment (`//`, `#`, `--`, `/* */`) and string literal (`"..."`, `'...'`) byte ranges when no Tree-sitter grammar is available.
- [words.rs:MIN_WORD_CHARS], [words.rs:WordSpan], [words.rs:words] — Splits prose and `camelCase`/`PascalCase`/`SCREAMING_SNAKE_CASE` identifiers into individual `WordSpan`s while skipping URLs, email addresses, hex literals (`0x...`), and CSS hex colors.

## Invariants
- [lib.rs:DICT] Lazily initializes the bundled English + technical word list (`data/english.txt` + `data/tech.txt`) once per process via `LazyLock`.
- [lib.rs:Speller::is_correct] Treats any word containing non-ASCII characters, shorter than `MIN_WORD_CHARS` (4 chars), or <= 4 uppercase letters (acronyms) as valid without dictionary lookup.
- [lib.rs:Speller::check] Deduplicates overlapping regions and stops scanning once `MAX_TYPOS` (500) typos are collected.

## Side Effects
- None

## Verification
- Run: `cargo test -p ide-spell`
