# view

## Overview
React view components for the editor area, split panes, tab strips, in-file Find/Replace bar, breadcrumbs, status bar widgets, document banners, and Go to Line dialog.

## Interface
- [EditorArea.tsx:EditorAreaView]: Root React component rendering the recursive split-pane layout, tab strips, drop zones, banners, and active TextEditorHost or custom editor kind.
- [EditorArea.tsx:dropZoneAt]: Computes whether a pointer drop over an editor pane targets an edge split ('left' | 'right' | 'top' | 'bottom') or 'center'.
- [FindBar.tsx:FindBar]: Pane-bound Find/Replace bar component and FindPanel reusable search/replace UI for any CodeMirror view.
- [FindBar.tsx:openFindBar]: Seeds query/selection scope and opens or focuses the Find/Replace bar for a pane.
- [StatusItems.tsx:Breadcrumbs]: Status-bar navigation bar widget rendering project, directory, file, and caret symbol segments.
- [StatusItems.tsx:CaretStatus]: Status-bar widgets (CaretStatus, LineEndingStatus, EncodingStatus, IndentStatus, ReadOnlyStatus, CodeBreadcrumbs).
- [Banner.tsx:EditorBanners]: Renders read-only/binary/too-large/library document banners and contributed editor banners.
- [GotoLineDialog.tsx:GotoLineDialog]: Modal dialog for Go to Line:Column (navigate.gotoLine).
- [NavBarPopup.tsx:NavBarPopup]: Floating keyboard-driven navigation bar popup and child symbol/file list.

## Invariants
- [EditorArea.tsx:PaneContent]: TextEditorWrapperContribution components must always receive and render their children (TextEditorHost) so the underlying EditorView stays mounted.
- [FindBar.tsx:seedFind]: Opening Replace over a multi-line selection scopes search to that selection and clears the query instead of copying the multi-line text into the search box.

## Side Effects
- [FindBar.tsx:FindPanel]: Updates findBarOpen, findBarFocus, and findBarHasResults context keys, claims find.openInWindow shortcuts, and dispatches CodeMirror search/scope effects.
- [StatusItems.tsx:LineEndingStatus]: Calls backend setLineEnding and setEncoding IPC commands and updates DocModel metadata.

## Verification
- Run: `pnpm test`
