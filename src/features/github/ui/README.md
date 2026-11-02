# ui

## Overview
Shared GitHub UI primitives: HiDPI user/team avatars with review-state rings, single/multi-select fuzzy chooser popups, state/label/branch chips, sanitized Markdown renderer, relative date formatters, and merge/push confirmation dialogs.

## Interface
- [Avatar.tsx:AVATAR_SIZE], [Avatar.tsx:AvatarProps], [Avatar.tsx:AvatarStackItem], [Avatar.tsx:reviewerRingColor], [Avatar.tsx:avatarSrc], [Avatar.tsx:Avatar], [Avatar.tsx:AvatarStack] — Circular avatar with `s=2x` sizing, deterministic fallback colour/initial, reviewer state outline ring, and overlapping avatar stack.
- [ChooserPopup.tsx:ChooserItemPresentation], [ChooserPopup.tsx:SingleChooserProps], [ChooserPopup.tsx:MultiChooserProps], [ChooserPopup.tsx:ChooserPopupProps], [ChooserPopup.tsx:chooserItems], [ChooserPopup.tsx:ChooserPopup] — Fuzzy-searchable single-select and multi-select popup list.
- [chips.tsx:StateTag], [chips.tsx:labelForeground], [chips.tsx:labelColor], [chips.tsx:LabelChip], [chips.tsx:LabelSwatch], [chips.tsx:BranchChip] — PR state pill (`DRAFT`/`CLOSED`/`MERGED`), contrast-aware label chip/swatch, and branch chip.
- [dialogs.tsx:MergeDialogInput], [dialogs.tsx:MergeDialogResult], [dialogs.tsx:mergeDialog], [dialogs.tsx:confirmPushMerge] — Modal dialogs for Merge / Squash and Merge (with commit message fields and `--delete-branch` option) and push confirmation.
- [Markdown.tsx:MarkdownProps], [Markdown.tsx:Markdown] — Renders GitHub Markdown/HTML via `useMarkupHtml` (`marked` + `DOMPurify`) and routes `http(s)` links to `openInBrowser`.
- [Placeholder.tsx:Placeholder] — Placeholder container for unbuilt sections.
- [prettyDate.ts:prettyDate], [prettyDate.ts:fullDateTime] — Relative date formatter (`just now`, `N minutes ago`, `yesterday`, `on Oct 12`) respecting IDE Date Format settings.

## Invariants
- [ChooserPopup.tsx:chooserItems] — Places initially chosen items first before loaded items while deduplicating by `getKey`.
- [Markdown.tsx:Markdown] — Intercepts all anchor clicks (`e.preventDefault()`) and only opens `http:`/`https:` URLs externally so relative links never navigate the application window.

## Side Effects
- [Markdown.tsx:Markdown] — Opens external `http(s)` links in the system browser via `openInBrowser`.
- [dialogs.tsx:mergeDialog], [dialogs.tsx:confirmPushMerge] — Mounts modal dialogs into `document.body`.

## Verification
- Run: `pnpm test`
