# templates

## Overview
Templates feature entry point integrating Live Templates (⌘J, Tab), Surround With (⌥⌘T), Postfix Completion, Emmet (Tab, Update Tag), and File Templates (New ▸ …) with CodeMirror 6, project menus, and Settings.

## Interface
- [index.ts:activateSettingsPages] [index.ts:activate] Registers template settings pages, actions, menus, overlays, and editor extensions.
- [editor.ts:EXTRA_COMPLETION_KEY] [editor.ts:EXCLUSIVE_COMPLETION_KEY] [editor.ts:insertLiveTemplate] [editor.ts:SurroundRange] [editor.ts:surroundTarget] [editor.ts:surroundWith] [editor.ts:expandLiveTemplateAtCaret] [editor.ts:expandPostfixAtCaret] [editor.ts:templateCompletionSource] [editor.ts:templateEditorExtension] CodeMirror 6 completion source, Tab expansion handler, and surround/insert commands.
- [editorContext.ts:enclosingName] [editorContext.ts:inComment] [editorContext.ts:inCommentOrString] [editorContext.ts:CommentTokens] [editorContext.ts:commentTokens] [editorContext.ts:suggestVariableName] [editorContext.ts:TemplateEnvInput] [editorContext.ts:templateFunctions] Syntax-tree context queries and live template variable function implementations.
- [storage.ts:loadJson] [storage.ts:saveJson] [sync.ts:syncTemplatesFromStorage] Safe localStorage JSON helpers and cross-window storage event listener.
- [emmet](./emmet/README.md) - Emmet abbreviation expansion (Tab) in markup and stylesheet contexts and "Update Tag with Emmet" enclosing-tag editor and prompt dialog.
- [files](./files/README.md) - File and Code Templates (New ▸ …) built-ins, variable/include rendering (#parse), package inference (jvm/go), Default/Project scheme persistence, file creation with reformat-aware caret placement, and the New File dialog.
- [live](./live/README.md) - Live Templates and Surround With (⌥⌘T) built-in definitions, template variable expression evaluator (expandTemplate), CodeMirror snippet converter, and localStorage-backed customization store.
- [popup](./popup/README.md) - Caret-anchored popup chooser (ChooserHost, showChooser) supporting prefix/fuzzy filtering, section headers, and underlined single-key mnemonics for ⌘J Insert Live Template, ⌥⌘T Surround With, and multi-expression Postfix Completion.
- [postfix](./postfix/README.md) - Postfix completion (expr.if, .not, .var, .return, etc.) built-in templates across TypeScript/JavaScript, Rust, Python, Go, Java, and Kotlin, AST/line expression target detector (findPostfixTarget), and localStorage settings store.
- [settings](./settings/README.md) - Settings UI pages for Editor › Live Templates, Editor › General › Postfix Completion, and Editor › File and Code Templates.

## Invariants
- [editor.ts:templateEditorExtension] Tab attempts Postfix expansion first, then Live Template expansion (when expandWithTab is enabled), then Emmet expansion, only when the editor is editable and completion is not active.
- [editor.ts:surroundTarget] Awaits whenSynced(view) before querying backend structure ranges when no selection exists.

## Side Effects
- [index.ts:activate] Registers editor contribution templates.live, overlays (templates.chooser, templates.newFile, emmet.updateTag), menu groups (file.new.primary, file.new.templates, editor.generate), settings pages, and storage event listeners.

## Verification
- Run: `pnpm test`
