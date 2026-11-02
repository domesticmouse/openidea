# cm

## Overview
Base CodeMirror 6 extensions, compartments, language mode loading, syntax highlighting, code folding, find/replace state, multi-caret/column selection, and smart editing commands.

## Interface
- [setup.ts:baseExtensions]: Builds the complete CodeMirror Extension array and runtime Compartments for an editor slot.
- [setup.ts:compartments]: Compartment map (language, comments, indent, readOnly, wrap, sticky, font, heavy, contributions, lineNumbers, folding, caret, prefs, distractionFree) for live reconfiguration.
- [setup.ts:effectiveIndent]: Computes effective tab/indent settings from .editorconfig, detected file indents, and per-language Code Style settings.
- [languages.ts:loadLanguageInfos]: Fetches and caches backend LanguageInfo definitions and maps uiMode/filenames to CodeMirror LanguageDescriptions.
- [languages.ts:findLanguageDescription]: Resolves a LanguageDescription from contributed modes, built-in modes, or filename patterns.
- [languages.ts:loadSupport]: Lazily loads and patches LanguageSupport for a LanguageDescription.
- [preview.ts:previewExtensions]: Returns read-only CodeMirror extensions (font, theme, line numbers, syntax highlighting) for code previews outside editor tabs.
- [angular](./angular/README.md) - CodeMirror 6 Lezer mixed-parser language support for Angular templates, combining HTML, Angular control-flow/binding syntax, JavaScript expressions, and block folding.

## Invariants
- [setup.ts:HEAVY_LIMIT]: Documents exceeding 8,000,000 UTF-16 units skip heavy per-line decorations (bracket matching, indent guides, rainbow brackets, selection matches).
- [languages.ts:fallbackIndent]: Fallback indentation service yields whenever a full CodeMirror language facet is active on the state.

## Side Effects
- [languages.ts:loadLanguageInfos]: Invokes backend.languages() IPC once and caches the resulting map in module state.
- [languages.ts:loadSupport]: Dynamically imports language grammar bundles and caches promises in module state.

## Verification
- Run: `pnpm test`
