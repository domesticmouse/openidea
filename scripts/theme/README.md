# theme

## Overview
Theme token extraction, documentation generator (`pnpm gen:theme-docs`), and IntelliJ `.theme.json` / `.icls` theme importer (`pnpm theme:import-intellij`).

## Interface
- [tokens.ts:themeTokenGroups] — Parses `:root` and `:root[data-theme="light"]` CSS custom properties from `src/styles/tokens.css` into `TokenGroup[]` (`[tokens.ts:TokenGroup]`, `[tokens.ts:ThemeToken]`)
- [tokens.ts:tokenSets] — Splits token groups into `{ ui, scheme }` token name arrays based on `SCHEME_GROUPS` (`[tokens.ts:SCHEME_GROUPS]`)
- [tokens.ts:referenceMarkdown] — Renders Markdown tables for themeable tokens and splices them between `REFERENCE_BEGIN` and `REFERENCE_END` via `withReference` (`[tokens.ts:withReference]`)
- [tokens.ts:stripJsonComments] — Strips `//` and `/* */` comments outside JSON string literals
- [intellij.ts:convertIntellijTheme] — Converts IntelliJ `.theme.json` and optional `.icls` XML (`[intellij.ts:convertIcls]`) into an OpenIDEA `ConvertedTheme` (`[intellij.ts:ConvertedTheme]`) using `UI_KEYS`, `ICLS_COLORS`, and `ICLS_ATTRIBUTES`
- [intellij.ts:pluginManifest] — Generates `plugin.toml` content for an imported theme plugin (`[intellij.ts:slug]`)
- [gen-docs.ts:DOC] — CLI script (`node scripts/theme/gen-docs.ts`) updating `docs/plugins/themes.md` from `src/styles/tokens.css`
- [import-intellij-theme.ts:converted] — CLI script (`node scripts/theme/import-intellij-theme.ts`) writing `<out>/plugin.toml` and `<out>/themes/<id>.theme.json` (+ `.scheme.json`)

## Invariants
- [tokens.ts:themeTokenGroups] — Throws an error if `src/styles/tokens.css` lacks a `:root[data-theme="light"]` block or if any blank-line-separated token chunk in the light block lacks a preceding `/* group */` section comment
- [tokens.ts:withReference] — Throws an error if `REFERENCE_BEGIN` or `REFERENCE_END` markers are missing or out of order in `docs/plugins/themes.md`
- [import-intellij-theme.ts:themePath] — Exits with status code `2` when required positional `<file.theme.json>` or `--out <dir>` arguments are missing

## Side Effects
- [gen-docs.ts:DOC] — Reads `src/styles/tokens.css` and `docs/plugins/themes.md`, overwriting `docs/plugins/themes.md` when the generated token table changes
- [import-intellij-theme.ts:converted] — Creates `<out>/themes/` and writes `plugin.toml`, `<id>.theme.json`, and optional `<id>.scheme.json` files to disk

## Verification
- Run: `pnpm test`
