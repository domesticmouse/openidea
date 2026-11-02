# editor

## Overview
Declarative Editor settings page builder (editorPage) and Editor settings pages (General & sub-pages, Code Editing, Inlay Hints, Font, Color Scheme with .icls import/export, Code Style & per-language pages, File Encodings, Inspections & profile manager, Natural Languages & Spelling).

## Interface
- [index.ts:EDITOR_PAGES] [index.ts:codeStyleLanguagePages] Built-in Editor settings pages and per-language Code Style page factory.
- [kit.tsx:Inline] [kit.tsx:Item] [kit.tsx:EditorSection] [kit.tsx:EditorPageDef] [kit.tsx:ItemView] [kit.tsx:EditorSections] [kit.tsx:itemLabels] [kit.tsx:sectionLabels] [kit.tsx:editorPage] Declarative control tree and search-label extractors for Editor settings pages.
- [general.tsx:useLanguages] [general.tsx:GENERAL_PAGES] Editor › General and sub-pages (Auto Import, Appearance, Breadcrumbs, Code Completion, Code Folding, Console, Editor Tabs, Gutter Icons, Inline Completion, Smart Keys).
- [font.tsx:FONT_PAGE] [colorScheme.tsx:COLOR_SCHEME_PAGE] Editor › Font and Editor › Color Scheme (.icls import/export and token attribute overrides) pages.
- [codeStyle.tsx:CODE_STYLE_PAGES] [codeStyle.tsx:codeStyleLanguagePages] [codeStyleScheme.tsx:CODE_STYLE_SCOPE] [codeStyleScheme.tsx:CODE_STYLE_KEYS] [codeStyleScheme.tsx:copyScheme] [codeStyleScheme.tsx:SchemeRow] [codeStyleScheme.tsx:FORMATTER_SECTIONS] [codeStyleScheme.tsx:CodeStyleMain] [codeStyleScheme.tsx:formatterLabels] Editor › Code Style scheme selector, formatter sections, and per-language tabs.
- [inspections.tsx:INSPECTIONS_PAGE] [inspectionsModel.ts:InspectionNode] [inspectionsModel.ts:LanguageServers] [inspectionsModel.ts:buildInspectionTree] [inspectionsModel.ts:filterInspectionTree] [inspectionsModel.ts:inspectionIds] [inspectionsModel.ts:isEnabled] [inspectionsModel.ts:CheckState] [inspectionsModel.ts:checkState] [inspectionsModel.ts:setEnabled] [inspectionsModel.ts:setLevel] [inspectionsModel.ts:setOption] [inspectionsModel.ts:pathToInspection] [inspectionsModel.ts:AS_REPORTED] [inspectionsModel.ts:LEVEL_OPTIONS] [inspectionsModel.ts:INSPECTION_KEYWORDS] [inspectionsModel.ts:withProfileOverrides] [inspectionsModel.ts:copyProfile] [inspectionsModel.ts:renameProfile] [inspectionsModel.ts:deleteProfile] Editor › Inspections tree, tri-state check model, severity/option overrides, and custom profile CRUD.
- [spelling.tsx:NATURAL_LANGUAGES_PAGE] [spelling.tsx:SPELLING_PAGE] Editor › Natural Languages and Spelling dictionary/word list pages.

## Invariants
- [kit.tsx:CheckItem] Dependent children and inline controls are disabled whenever the parent checkbox is unchecked (off = parentDisabled || !checked), and invert flips the stored boolean.
- [codeStyleScheme.tsx:SchemeRow] [codeStyle.tsx:SCHEME_SCOPE] All Code Style pages share one scope selection (scopeGroup: "editor.codeStyle"), and copyScheme stages all CODE_STYLE_KEYS into the target scope (global or project).
- [inspectionsModel.ts:checkState] [inspectionsModel.ts:setEnabled] Enabling an inspection removes its enabled override (enabled: undefined), and group checkboxes report "mixed" when only a subset of descendant inspections are enabled.

## Side Effects
- [colorScheme.tsx:ColorSchemePage] Registers editor.colorScheme.import, editor.colorScheme.export, and editor.colorScheme.delete actions and reads/writes .icls files via @tauri-apps/plugin-dialog, commands.fsReadText, commands.fsDelete, and commands.fsCreateFileBytes.
- [general.tsx:useLanguages] [codeStyle.tsx:codeStyleLanguagePages] [inspections.tsx:loadLanguages] Queries commands.languages() over IPC.

## Verification
- Run: `pnpm test`
