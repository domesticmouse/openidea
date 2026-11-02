# settings

## Overview
Settings UI pages for Editor › Live Templates, Editor › General › Postfix Completion, and Editor › File and Code Templates.

## Interface
- [LiveTemplatesPage.tsx:LiveTemplatesPage] Settings page for viewing, editing, duplicating, and adding Live Templates and variables.
- [PostfixPage.tsx:PostfixPage] Settings page for enabling/disabling Postfix Completion and individual postfix templates.
- [FileTemplatesPage.tsx:FileTemplatesPage] Settings page for editing File Templates, Includes, and Code Templates across Default and Project schemes.

## Invariants
- [LiveTemplatesPage.tsx:syncVariables] Synchronizes template variables from $VAR$ tokens in body, preserving existing variable definitions in first-occurrence order.

## Side Effects
- [LiveTemplatesPage.tsx:LiveTemplatesPage] [PostfixPage.tsx:PostfixPage] [FileTemplatesPage.tsx:FileTemplatesPage] Mutates live, postfix, and file template stores and localStorage.

## Verification
- Run: `pnpm test`
