# http-client

## Overview
HTTP Client for `.http` and `.rest` files providing syntax highlighting, per-request run gutter markers, environment selection, response/history inspection in the Services tool window, and Spring Web request generation.

## Interface
- [index.ts:NEW_REQUEST_TEMPLATE], [index.ts:activate] — Registers the HTTP language description, `.http` and Spring editor extensions, `"httpClient"` service view, menu groups (`http.new`, `http.spring`), and actions (`http.runRequest`, `http.runAll`, `http.newRequest`, `http.generateSpringRequest`, `http.generateSpringControllerRequests`, `http.showToolWindow`).
- [editor.tsx:HTTP_LANGUAGE], [editor.tsx:requestsField], [editor.tsx:requestAtCaret], [editor.tsx:refreshRequests], [editor.tsx:httpEditorExtension] — CodeMirror 6 extension providing debounced request parsing, top toolbar panel (Run All, Stop, environment selector), and ▶ gutter markers.
- [HttpClientView.tsx:HttpClientView], [HttpClientView.tsx:HttpClientHeaderActions] — Services tool window view displaying request history and selected `HttpExchange` details (pretty/raw body, headers table, and sent request).
- [language.ts:METHODS], [language.ts:httpParser], [language.ts:httpLanguage], [language.ts:httpLanguageDescription] — CodeMirror `StreamParser` and `LanguageDescription` for `.http`/`.rest` files.
- [store.ts:SERVICE_VIEW], [store.ts:RunningRequest], [store.ts:HttpClientState], [store.ts:useHttpClient], [store.ts:setEnvironment], [store.ts:loadEnvironments], [store.ts:effectiveEnvironment], [store.ts:refreshHistory], [store.ts:selectEntry], [store.ts:clearHistory], [store.ts:parseRequests], [store.ts:runRequest], [store.ts:runAll], [store.ts:cancelRunning], [store.ts:resetHttpClient] — Zustand store and IPC wrappers for HTTP environments, active request execution, and exchange history.
- [format.ts:BodyKind], [format.ts:bodyKind], [format.ts:prettyJson], [format.ts:prettyMarkup], [format.ts:prettyBody], [format.ts:formatSize], [format.ts:formatMs] — Content-type detection and pretty-printing for JSON, XML, and HTML response bodies, plus byte and duration formatters.
- [spring](./spring/README.md) - Parses Spring Web controller and endpoint annotations in Java and Kotlin editors, providing gutter markers, intentions, and context actions that generate `.http` requests into a scratch file.

## Invariants
- [store.ts:runRequest] — Cancels any previously running request via `commands.cancelRequest` before starting a new request so at most one request is active at a time.
- [store.ts:runAll] — Runs requests sequentially in file order and stops on the first cancelled or failed request.
- [format.ts:prettyMarkup] — Preserves `<script>`, `<style>`, `<pre>`, and HTML comments verbatim without re-indenting their contents.

## Side Effects
- [store.ts:runRequest], [store.ts:loadEnvironments], [store.ts:refreshHistory], [store.ts:selectEntry], [store.ts:clearHistory] — Invokes backend IPC (`httpEnvironments`, `httpHistory`, `httpHistoryGet`, `httpHistoryClear`, `httpRequests`, `httpExecute`, `cancelRequest`), opens the `"httpClient"` service view, and persists per-file environment choices in `localStorage` (`openidea.httpClient.environments`).
- [index.ts:activate] — Creates `requests[_n].http` files in `<appDataDir>/scratches` or the selected project directory via `commands.fsCreateDir` and `commands.fsCreateFile`.

## Verification
- Run: `pnpm test`
