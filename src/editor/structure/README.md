# structure

## Overview
Tree-sitter syntactic structure client ('dumb mode' fallback) providing AST node, expression, local symbol, identifier, and structural range queries when LSP is unavailable or slow.

## Interface
- [index.ts:structure]: Client facade with support, nodeAt, expressionAt, syntaxErrors, locals, identifiers, identifiersInFiles, and structuralRanges queries.
- [index.ts:withFallback]: Executes an LSP query first and falls back to a tree-sitter structure query on notReady, error, empty, or slow conditions.
- [index.ts:QueryOptions]: Options supporting AbortSignal-driven backend request cancellation.

## Invariants
- [index.ts:withFallback]: LSP is always tried first unless skipLsp is set; explicit request cancellation (isCancelled) never triggers the syntax fallback.
- [index.ts:structure]: Callers must await whenSynced(view) after local edits before invoking structure queries because the backend parses its own document buffer.

## Side Effects
- [index.ts:structure]: Invokes commands.syntax* and commands.cancelRequest IPC calls and caches per-language StructureSupport promises.

## Verification
- Run: `pnpm test`
