# files

## Overview
File and Code Templates (New ▸ …) built-ins, variable/include rendering (#parse), package inference (jvm/go), Default/Project scheme persistence, file creation with reformat-aware caret placement, and the New File dialog.

## Interface
- [builtins.ts:BUILTIN_INCLUDES] [builtins.ts:BUILTIN_CODE_TEMPLATES] [builtins.ts:BUILTIN_FILE_TEMPLATES] Built-in file templates, includes (File Header), and code templates.
- [model.ts:NameStyle] [model.ts:FileTemplateKind] [model.ts:FileTemplate] [model.ts:TemplateFragment] [model.ts:styleName] [model.ts:withKind] [model.ts:PlannedFile] [model.ts:planFile] [model.ts:inferJvmPackage] [model.ts:GO_PACKAGE_RE] [model.ts:inferGoPackage] [model.ts:TemplateEnv] [model.ts:RenderedTemplate] [model.ts:IncludeResolver] [model.ts:fragmentResolver] [model.ts:renderFileTemplate] [model.ts:reformatsOnCreate] [model.ts:caretAfterReformat] File path planning, JVM/Go package inference, template body/include rendering, and post-reformat caret tracking.
- [store.ts:FILE_TEMPLATES_KEY] [store.ts:TemplateScheme] [store.ts:mergeFileTemplates] [store.ts:mergeIncludes] [store.ts:mergeCodeTemplates] [store.ts:useFileTemplates] [store.ts:fileTemplates] [store.ts:templateIncludes] [store.ts:saveFileTemplate] [store.ts:removeFileTemplate] [store.ts:resetFileTemplate] [store.ts:isBuiltinFileTemplate] [store.ts:isModifiedFileTemplate] [store.ts:newFileTemplateId] [store.ts:saveInclude] [store.ts:removeInclude] [store.ts:resetInclude] [store.ts:isBuiltinInclude] [store.ts:isModifiedInclude] [store.ts:saveCodeTemplate] [store.ts:resetCodeTemplate] [store.ts:isModifiedCodeTemplate] [store.ts:setTemplateScheme] [store.ts:reloadFileTemplates] [store.ts:watchTemplateProject] Default and Project scheme persistence store.
- [create.ts:packageFor] [create.ts:defaultDirectory] [create.ts:createFromTemplate] [NewFileDialog.tsx:openNewFileDialog] [NewFileDialog.tsx:NewFileDialogHost] End-to-end file creation workflow and New File modal dialog.

## Invariants
- [model.ts:planFile] Rejects empty names, . or .. segments, and names with characters invalid on Windows/Unix (/[<>:"|?*\0]/).
- [model.ts:renderFileTemplate] Guards #parse against recursive cycles (MAX_INCLUDE_DEPTH = 8), drops lines whose $PACKAGE$ or #parse evaluates empty, and places caret at $END$ (or text.length).
- [model.ts:caretAfterReformat] Preserves logical caret position across code formatting by counting non-whitespace tokens before $END$.

## Side Effects
- [store.ts:saveFileTemplate] [store.ts:setTemplateScheme] Reads/writes localStorage under openidea.fileTemplates.v1*.
- [create.ts:createFromTemplate] Calls commands.fsCreateFile, notifyFileOperation, editorService().openFile, editorService().reformat, and executeAction("file.save").

## Verification
- Run: `pnpm test`
