# sql

## Overview
Dialect-aware SQL lexer, caret context analyzer, DataGrip-style schema completion candidate generator, identifier resolver and unresolved-symbol inspector, query parameter binder, and SQL code formatter.

## Interface
- [lexer.ts:TokenKind]
- [lexer.ts:Token]
- [lexer.ts:Lexed]
- [lexer.ts:lex]
- [lexer.ts:inLiteral]
- [lexer.ts:identName]
- [dialect.ts:SqlFunction]
- [dialect.ts:CLAUSE_WORDS]
- [dialect.ts:DATA_TYPES]
- [dialect.ts:isReserved]
- [dialect.ts:statementStarters]
- [dialect.ts:joinKeywords]
- [dialect.ts:predicateKeywords]
- [dialect.ts:functions]
- [analyze.ts:TableRef]
- [analyze.ts:Clause]
- [analyze.ts:SqlContext]
- [analyze.ts:Analysis]
- [analyze.ts:ValuesSlot]
- [analyze.ts:analyze]
- [analyze.ts:outputColumns]
- [analyze.ts:templateContexts]
- [analyze.ts:valuesSlot]
- [complete.ts:CandidateKind]
- [complete.ts:SqlCandidate]
- [complete.ts:CompletionInput]
- [complete.ts:shouldAutoCompleteSql]
- [complete.ts:quoteIdent]
- [complete.ts:aliasFor]
- [complete.ts:sqlCandidates]
- [resolve.ts:SqlSymbol]
- [resolve.ts:Span]
- [resolve.ts:Resolved]
- [resolve.ts:SqlProblem]
- [resolve.ts:findTable]
- [resolve.ts:SqlResolver]
- [params.ts:SqlParam]
- [params.ts:findParams]
- [params.ts:bindParams]
- [format.ts:SqlEdit]
- [format.ts:formatSql]
- [format.ts:formatted]

## Invariants
- [lexer.ts:lex] — Handles dialect-specific quoting and comments (MySQL backticks, # comments, backslash escapes; PostgreSQL $tag$ dollar-quoting and $1 parameters) using UTF-16 code-unit offsets matching CodeMirror.
- [params.ts:findParams], [params.ts:bindParams] — Ignores MySQL @var/@@var session variables and PostgreSQL ? JSONB operators when extracting query parameters, and replaces unbound or empty parameter values with 'NULL'.
- [resolve.ts:SqlResolver] — Reports unresolved symbol diagnostics only on CHECKED DML/query statements (SELECT, WITH, INSERT, UPDATE, DELETE, REPLACE) when the schema model is loaded and all FROM references have known column lists.
- [format.ts:formatSql] — Formats each statement independently while preserving keyword case, comments, and text between statements.

## Side Effects
- None

## Verification
- Run: `pnpm test`
