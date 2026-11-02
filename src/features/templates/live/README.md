# live

## Overview
Live Templates and Surround With (⌥⌘T) built-in definitions, template variable expression evaluator (expandTemplate), CodeMirror snippet converter, and localStorage-backed customization store.

## Interface
- [builtins.ts:TS_LANGS] [builtins.ts:BUILTIN_TEMPLATES] [builtins.ts:BUILTIN_IDS] Built-in live and surround templates across Rust, TypeScript/JavaScript, Python, Go, Java, Kotlin, SQL, and general contexts.
- [model.ts:TemplateVariable] [model.ts:LiveTemplate] [model.ts:Segment] [model.ts:parseTemplateBody] [model.ts:bodyVariableNames] [model.ts:usesSelection] [model.ts:Expr] [model.ts:parseExpr] [model.ts:calledFunctions] [model.ts:capitalize] [model.ts:decapitalize] [model.ts:camelCase] [model.ts:snakeCase] [model.ts:kebabCase] [model.ts:pascalCase] [model.ts:spaceSeparated] [model.ts:underscoresToCamelCase] [model.ts:escapeString] [model.ts:formatDate] [model.ts:formatTime] [model.ts:TemplateFunctions] [model.ts:ExpansionEnv] [model.ts:evalExpr] [model.ts:ExpandedTemplate] [model.ts:expandTemplate] [model.ts:applicableTemplates] Template body parser, expression evaluator, and CodeMirror snippet expander.
- [store.ts:LIVE_TEMPLATES_KEY] [store.ts:LiveTemplateCustomizations] [store.ts:mergeTemplates] [store.ts:useLiveTemplates] [store.ts:liveTemplates] [store.ts:saveTemplate] [store.ts:removeTemplate] [store.ts:resetTemplate] [store.ts:isModifiedBuiltin] [store.ts:newTemplateId] [store.ts:LIVE_TEMPLATE_OPTIONS_KEY] [store.ts:LiveTemplateOptions] [store.ts:useLiveTemplateOptions] [store.ts:updateLiveTemplateOptions] [store.ts:reloadLiveTemplates] Customization and options store.
- [surrounders.ts:Surrounder] [surrounders.ts:SurroundSelection] [surrounders.ts:surroundersFor] [surrounders.ts:SurroundEntry] [surrounders.ts:surroundEntries] Built-in statement/selection surrounders and mnemonic assignment.

## Invariants
- [model.ts:expandTemplate] Assigns 1-based tab stops only to editable variables (!skipIfDefined || !value), links repeated occurrences to the same tab stop, and always terminates with ${0} ($END$).
- [model.ts:applicableTemplates] Excludes templates with scope === "code" (default) when inCommentOrString is true.
- [surrounders.ts:surroundEntries] Assigns mnemonics 1–9, 0, A–Z to surrounders first, then assigns each live template the first unused character of its abbrev.

## Side Effects
- [store.ts:saveTemplate] [store.ts:removeTemplate] [store.ts:updateLiveTemplateOptions] Persists template customizations and options to localStorage.

## Verification
- Run: `pnpm test`
