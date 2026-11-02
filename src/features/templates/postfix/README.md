# postfix

## Overview
Postfix completion (expr.if, .not, .var, .return, etc.) built-in templates across TypeScript/JavaScript, Rust, Python, Go, Java, and Kotlin, AST/line expression target detector (findPostfixTarget), and localStorage settings store.

## Interface
- [model.ts:PostfixTemplate] [model.ts:POSTFIX_TEMPLATES] [model.ts:POSTFIX_GROUPS] [model.ts:postfixGroupOf] [model.ts:postfixTemplatesFor] [model.ts:PostfixCandidate] [model.ts:PostfixTarget] [model.ts:findPostfixTarget] [model.ts:isCompound] [model.ts:candidatesFor] Postfix template definitions and expression target extraction.
- [store.ts:POSTFIX_KEY] [store.ts:PostfixSettings] [store.ts:usePostfixSettings] [store.ts:updatePostfixSettings] [store.ts:setPostfixTemplateEnabled] [store.ts:reloadPostfixSettings] Postfix completion enablement and per-template settings store.

## Invariants
- [model.ts:findPostfixTarget] Rejects .. range operators and trailing dots inside candidate expressions, and marks a candidate statement: true only when the line is blank before from.
- [model.ts:isCompound] Identifies binary/whitespace/ternary expressions at parenthesis depth 0 while ignoring :: and ?. so wrapCompound templates parenthesize $EXPR$ only when needed.

## Side Effects
- [store.ts:updatePostfixSettings] [store.ts:setPostfixTemplateEnabled] Persists PostfixSettings to localStorage (openidea.postfix.v1).

## Verification
- Run: `pnpm test`
