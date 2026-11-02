# angular

## Overview
CodeMirror 6 Lezer mixed-parser language support for Angular templates, combining HTML, Angular control-flow/binding syntax, JavaScript expressions, and block folding.

## Interface
- [index.ts:angular]: Returns LanguageSupport for Angular templates with mixed HTML/JS parsing, bracket closing, and block folding.
- [index.ts:angularLanguage]: Configured LRLanguage wrapping @codemirror/lang-html with parseMixed(mixAngular).
- [parser.ts:parser]: Generated Lezer LRParser for Angular template expressions, bindings, microsyntax, and @if/@for/@switch/@defer/@let blocks.

## Invariants
- [index.ts:runsParser]: Separate parser instances (runsParser, documentRunsParser, textParser, attrParser) must remain distinct so parseMixed inner tree reuse does not cross mount kinds.
- [index.ts:entityRuns]: Adjacent Text and EntityReference/CharacterReference children split by '&' must be merged into a single overlay run so expressions like 'a && b' parse as one unit.

## Side Effects
- None

## Verification
- Run: `pnpm test`
