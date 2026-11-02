# notebook

## Overview
Jupyter `.ipynb` notebook custom editor and file editor provider featuring serialized backend edit/undo/redo queues, streaming Tauri kernel execution channels, and DOMPurify/CSP output sandboxing.

## Interface
- [index.ts:activate] — Registers the `"notebook"` editor kind (with `EditorDocumentLifecycle`) and `.ipynb` `FileEditorProvider` with alias-safe `ProviderAcquisitions` reference counting.
- [session.ts:NotebookBackend], [session.ts:NotebookViewState], [session.ts:NotebookSession], [session.ts:notebookBackend], [session.ts:notebookSession], [session.ts:existingNotebookSession], [session.ts:closeNotebook], [session.ts:_setNotebookBackendForTests], [session.ts:_resetNotebookSessionsForTests] — Per-file session managing snapshot versions, serialized edit/history queues, kernel execution via `Channel<NotebookOutputEvent>`, and Python interpreter selection.
- [NotebookEditor.tsx:NotebookEditor] — Virtualized React notebook editor with execution/kernel toolbar, Python interpreter selector, and cell list.
- [CellEditor.tsx:CellEditor], [CellEditor.tsx:CellEditorProps] — CodeMirror 6 editor for code (Python) and Markdown cells with shared backend undo/redo bindings and read-only freeze synchronization.
- [OutputView.tsx:CellOutputs], [OutputView.tsx:MarkdownCell] — Renders stream/ANSI text, error tracebacks, image/HTML/text MIME bundles, and GFM Markdown cells with inline attachments.
- [outputs.ts:OutputReducer], [outputs.ts:object], [outputs.ts:own], [outputs.ts:mimeText] — Reduces streaming `NotebookOutputEvent` messages into `NotebookSnapshot` state and flags version gaps or `update_display_data` for authoritative refresh.
- [security.ts:OUTPUT_CSP], [security.ts:sanitizedSvgUrl], [security.ts:staticOutputDocument], [security.ts:markdownRoot], [security.ts:markdownAttachmentNames], [security.ts:resolveMarkdownAttachments], [security.ts:imageMimeUrl] — Sanitizes HTML, Markdown, and SVG outputs with `DOMPurify` and builds sandboxed iframe `srcDoc` documents with `OUTPUT_CSP`.

## Invariants
- [index.ts:activate] — `EditorDocumentLifecycle` probes use `existingNotebookSession` so unopened/disposed tabs never allocate sessions; `.ipynb` open failures never fall back to raw JSON text editors.
- [session.ts:NotebookSession] — Rejects structural cell edits and undo/redo while `running` or `frozen`, and coalesces only trailing uncommitted, non-inflight `setSource` edits for the same cell.
- [session.ts:NotebookSession] — Caps buffered `Channel` output events at 256 per animation frame, triggering `output.recover()` and an authoritative `backend.get()` refresh on overflow or version gaps.
- [CellEditor.tsx:CellEditor] — Omits CodeMirror's local history extension so all cell source and structural changes share the backend undo/redo stack.
- [security.ts:staticOutputDocument], [security.ts:sanitizedSvgUrl] — Sanitizes all untrusted HTML/Markdown/SVG via `DOMPurify` and isolates HTML outputs inside `sandbox=""` iframes with `OUTPUT_CSP` (`script-src 'none'; connect-src 'none'`).

## Side Effects
- [session.ts:notebookBackend], [session.ts:NotebookSession] — Calls backend IPC (`notebookOpen`, `notebookGet`, `notebookEdit`, `notebookSave`, `notebookReload`, `notebookUndo`, `notebookRedo`, `notebookClose`, `notebookInterrupt`, `notebookRestart`, `notebookExecute`, `sdkDetect`, `sdkPythonEffective`, `cancelRequest`), listens to `notebookKernel` IDE events, and persists per-notebook Python interpreter paths in `localStorage` (`openidea.notebook.python:<path>`).

## Verification
- Run: `pnpm test`
