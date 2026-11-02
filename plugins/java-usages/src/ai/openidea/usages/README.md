# usages

## Overview
JDT LS OSGi delegate command (`openidea.java.usages`) and AST-based structural read/write usage classifier for Java fields, getters, setters, builders, and Lombok `@Accessors`.

## Interface
- [UsagesCommand.java:UsagesCommand] — Public `IDelegateCommandHandler` implementation handling `openidea.java.usages`
- [UsagesCommand.java:UsagesCommand.executeCommand] — Executes reference search for `[uri, line, column, declarations]` and returns `List<Map<String, Object>>` entries `{location, kind, access}`
- [UsageAccess.java:UsageAccess] — Package-private query-local AST analyzer classifying matched `Location` entries into `read`, `write`, `readWrite`, or `unknown` (`[UsageAccess.java:UsageAccess.classify]`)

## Invariants
- [UsagesCommand.java:NATIVE_PLAN_SUPPORTED] — Optimized single-pass native target discovery is enabled only when `ReferencesHandler.class` SHA-256 equals `31ff5a10c7d69d58c6a9b619ef17e3ec41b11b545751ba428618a76cc1341064` (`[UsagesCommand.java:nativePlanSupported]`); otherwise falls back to `ReferencesHandler.findReferences` plus deduplicated category searches
- [UsagesCommand.java:addConfiguredAccessors] — Resolves only explicit `@Accessors` / `lombok.experimental.Accessors` annotations on the field or enclosing types; `lombok.config`-only naming is never guessed
- [UsageAccess.java:UsageAccess.classify] — Returns `"unknown"` if the source buffer changed since parsing, if AST nodes are `MALFORMED`/`RECOVERED`, if the node is a declaration or `MethodReference`, or if `kind` is `"unknown"`
- [UsageAccess.java:UsageAccess.index] — Builds a query-local exact AST range index only when a file has at least 16 requested locations; duplicate or uncontained ranges fall back to `NodeFinder.perform`

## Side Effects
- [UsagesCommand.java:UsagesCommand.executeCommand] — Queries Eclipse JDT `SearchEngine` via `ReferencesHandler` and reflects `ReferencesHandler.getBuilderName(IField)` (`[UsagesCommand.java:addNativeBuilderTargets]`)
- [UsageAccess.java:UsageAccess.classify] — Parses referenced compilation unit buffers into JDT DOM `CompilationUnit` ASTs via `ASTParser.newParser(AST.getJLSLatest())`

## Verification
- Run: `python3 plugins/java-usages/tests/verify.py /path/to/jdtls /path/to/lombok.jar`
