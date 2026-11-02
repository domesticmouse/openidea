# syntax

## Overview
Tree-sitter grammar registry, incremental syntax-tree and query-result LRU cache, and AST-driven queries for outlines, symbol tags, folding ranges, semantic highlights, selection expansion, literal/spelling regions, and HTML/CSS web references.

## Interface
- [mod.rs:MAX_PARSE_BYTES]: Maximum source size (16 MiB) accepted by the Tree-sitter parser.
- [mod.rs:Syntax]: Thread-safe Tree-sitter grammar and query engine.
- [mod.rs:TreeError]: Parse rejection reason (`UnsupportedLanguage`, `TooLarge`, `ParseFailed`).
- [mod.rs:ParsedTree]: Borrowed or owned Tree-sitter `Tree` handle for multi-query passes.
- [cache.rs:MAX_CACHED_TREES]: Maximum number of documents retained in `SyntaxCache` (24).
- [cache.rs:SyntaxCache]: Per-document incremental `Tree` and outline/folding memo cache.
- [cache.rs:SyntaxCacheStats]: Snapshot of cached tree count, cache hits, full parses, and incremental parses.
- [highlight.rs:SyntaxHighlightKind]: Semantic highlight token classification.
- [highlight.rs:SyntaxHighlights]: Packed UTF-16 `[from, to, kind]` highlight buffer.
- [literals.rs:Literal], [literals.rs:LiteralKind]: Detected string, hex-color, regex, and URL literals.
- [literals.rs:SpellingKind], [literals.rs:SpellingRegion]: AST regions eligible for spellchecking.
- [outline.rs:merge_visibility]: Combines symbol visibility annotations across parent/child captures.
- [web.rs:CSS_CLASS], [web.rs:CSS_ID], [web.rs:CSS_VARIABLE], [web.rs:FileReference], [web.rs:WebReference], [web.rs:WebReferenceKind]: Cross-file web selector and asset reference records.

## Invariants
- Source texts larger than `MAX_PARSE_BYTES` (16 MiB) are rejected with `TreeError::TooLarge` rather than parsed (`[mod.rs:MAX_PARSE_BYTES]`, `[mod.rs:Syntax::with_parsed]`).
- `SyntaxCache` retains at most `MAX_CACHED_TREES` (24) parsed documents with LRU eviction and invalidates any cached tree when an incoming `TextEdit` version does not equal `cached.version + 1` (`[cache.rs:MAX_CACHED_TREES]`, `[cache.rs:SyntaxCache::edit]`, `[cache.rs:SyntaxCache::evict]`).
- `SyntaxHighlights::data` stores sorted, non-overlapping `[from, to, kind]` UTF-16 code-unit triples where `from < to` (`[highlight.rs:SyntaxHighlights]`, `[highlight.rs:highlights]`).
- All 47 bundled Tree-sitter grammars and outline queries compile cleanly and expose a deterministic FNV-1a `fingerprint` for persistent index cache invalidation (`[mod.rs:Syntax::fingerprint]`, `[grammars.rs:SPECS]`).

## Side Effects
- Maintains a thread-local `tree_sitter::Parser` instance per OS thread (`[mod.rs:PARSER]`).
- Mutates mutex-guarded LRU tree/memo tables and atomic hit/parse telemetry counters inside `SyntaxCache` (`[cache.rs:SyntaxCache]`).

## Verification
- Run: `cargo test -p ide-language`
