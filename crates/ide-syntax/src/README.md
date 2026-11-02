# src

## Overview
Tree-sitter structure service providing AST node/expression lookup, syntax error reporting, lexical local-variable scope resolution (`locals.scm`), identifier occurrence scanning, structural ranges, and JVM type presentation.

## Interface
- [lib.rs:MAX_SYNTAX_ERRORS], [lib.rs:StructureError], [lib.rs:StructureResult], [lib.rs:Snapshot], [lib.rs:StructureService] — Entry point (`new`, `cache`, `syntax`, `support`, `node_at`, `expression_at`, `syntax_errors`, `locals`, `identifiers`, `identifiers_in_files`, `structural_ranges`, `jvm_presentation`) backed by `Arc<SyntaxCache>`.
- [types.rs:SyntaxNode], [types.rs:NodeAt], [types.rs:ExpressionAt], [types.rs:SyntaxErrorKind], [types.rs:SyntaxError], [types.rs:SyntaxErrors], [types.rs:LocalKind], [types.rs:LocalSymbol], [types.rs:NotLocalReason], [types.rs:Locals], [types.rs:IdentifierOccurrence], [types.rs:StructuralRanges], [types.rs:StructureSupport], [types.rs:FileIdentifiers] — Serializable IPC types for syntax structure queries (0-based lines, UTF-16 columns).
- [jvm.rs:JvmTypeKind], [jvm.rs:JvmPresentation], [jvm.rs:StructureService::jvm_presentation] — Classifies the primary top-level Java/Kotlin declaration (`Class`, `AbstractClass`, `Interface`, `Enum`, `Record`, `Annotation`, `Exception`, `Object`) and detects `@SpringBootApplication` and runnable `main` methods.
- [nodes.rs:node_at], [nodes.rs:expression_at], [nodes.rs:structural_ranges], [nodes.rs:syntax_errors] — Implements AST node/ancestor lookup, Extract Variable expression candidate discovery, statement/declaration sibling navigation, and `ERROR`/`MISSING` node collection.
- [locals.rs:LocalsQuery], [locals.rs:locals] — Compiles bundled `queries/<grammar>/locals.scm` files and resolves lexical local definitions, scopes, references, shadowing/redeclaration semantics, and ambiguity conflicts.
- [identifiers.rs:identifiers_named] — Fast `memchr::memmem` + `TreeCursor` scanner that locates all non-comment, non-string leaf identifier tokens matching a name and flags member-qualified occurrences.
- [kinds.rs:Redeclaration], [kinds.rs:LangKinds], [kinds.rs:Kinds], [kinds.rs:locals_source], [kinds.rs:field_name], [kinds.rs:in_comment_or_string], [kinds.rs:is_comment] — Per-grammar node-kind tables and supertype resolvers for 18+ languages.
- [lines.rs:Lines], [lines.rs:utf16_len] — `memchr`-backed line-start index converting between UTF-8 byte offsets and 0-based line / UTF-16 column `Pos`/`Range` coordinates.

## Invariants
- [nodes.rs:syntax_errors] Caps returned syntax errors at `MAX_SYNTAX_ERRORS` (200) in document order and sets `SyntaxErrors::truncated = true` when more exist.
- [locals.rs:locals] Only function-level scopes (blocks, functions, lambdas, loops, catch clauses) qualify as `Locals::Local`; file, class, or module-level declarations and member positions return `Locals::NotLocal`.
- [locals.rs:hidden_occurrences] Marks `LocalSymbol::certain = false` whenever the scope contains uncaptured occurrences of the name inside `ERROR` nodes or `$name` string templates, or has conflicting same-scope declarations under `Redeclaration::Conflict`.
- [lines.rs:Lines::pos] Clamps out-of-range positions and rounds byte offsets that land inside a multi-byte UTF-8 codepoint or UTF-16 surrogate pair down to the character start.

## Side Effects
- [lib.rs:StructureService::identifiers_in_files] Uses a `rayon` parallel iterator to parse and scan multiple files concurrently while checking `CancellationToken`.
- [kinds.rs:Kinds::for_grammar], [locals.rs:LocalsQuery::for_grammar] Caches compiled `Kinds` and `LocalsQuery` instances for the lifetime of the process in `OnceLock<Mutex<HashMap<...>>>`.

## Verification
- Run: `cargo test -p ide-syntax`
