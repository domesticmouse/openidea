# spring

## Overview
Parses Spring Web controller and endpoint annotations in Java and Kotlin editors, providing gutter markers, intentions, and context actions that generate `.http` requests into a scratch file.

## Interface
- [editor.ts:SPRING_LANGUAGES], [editor.ts:GENERATED_REQUESTS], [editor.ts:GENERATE_REQUEST], [editor.ts:GENERATE_CONTROLLER_REQUESTS], [editor.ts:Target], [editor.ts:generateRequests], [editor.ts:springMenuItems], [editor.ts:menuGroupItems], [editor.ts:generateAtCaret], [editor.ts:springEditorExtension] — CodeMirror 6 extension, gutter markers, intentions, and request generation commands for Spring controllers.
- [endpoints.ts:QueryParam], [endpoints.ts:Endpoint], [endpoints.ts:Controller], [endpoints.ts:Constants], [endpoints.ts:joinPaths], [endpoints.ts:controllersOf], [endpoints.ts:springControllers], [endpoints.ts:springAt] — Extracts `@RestController`/`@Controller` classes and `@RequestMapping`/`@GetMapping`/`@PostMapping`/`@PutMapping`/`@DeleteMapping`/`@PatchMapping` endpoints, resolving intra-file constants, `@PathVariable`, `@RequestParam`, headers, media types, and `@RequestBody` types.
- [request.ts:Json], [request.ts:RenderOptions], [request.ts:SourceReader], [request.ts:parseProperties], [request.ts:parseYaml], [request.ts:hostFromConfig], [request.ts:bareType], [request.ts:skeleton], [request.ts:renderRequest], [request.ts:appendRequests], [request.ts:configCandidates], [request.ts:findHost], [request.ts:bodyLookup] — Resolves server host/port/context-path from `application.properties`/`application.yml`, synthesizes JSON request body skeletons, and formats `###` HTTP blocks.
- [source.ts:Tok], [source.ts:Expr], [source.ts:Annotation], [source.ts:Param], [source.ts:Field], [source.ts:Method], [source.ts:ClassDecl], [source.ts:SourceFile], [source.ts:tokenize], [source.ts:parseSource], [source.ts:allClasses] — Lightweight lexer and declaration parser for Java and Kotlin classes, records, objects, methods, fields, and annotations.

## Invariants
- [editor.ts:springEditorExtension] — Ignores virtual documents (`ctx.virtual || isVirtualUri(ctx.path)`) and non-Java/Kotlin languages; debounces marker recomputation by 400ms and places at most one gutter marker per line.
- [endpoints.ts:springControllers] — Fast-paths out without tokenizing unless the source matches `/@(?:[\w.]+\.)?(?:Rest)?Controller\b/`.
- [request.ts:skeleton] — Limits recursive DTO field expansion to `depth <= 1` and skips `static` fields.

## Side Effects
- [editor.ts:generateRequests] — Reads Spring config and DTO class files via `commands.fsReadText`, creates `<appDataDir>/scratches/generated-requests.http` via `commands.fsCreateDir` and `commands.fsCreateFile`, emits a local `fsChanged` event, opens the scratch file via `editorService().openFile`, and dispatches an editor insertion transaction.

## Verification
- Run: `pnpm test`
