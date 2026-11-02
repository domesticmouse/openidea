# components

## Overview
Reusable Settings form controls (Row, Toggle, NumberInput, Select, ColorSwatches, GlobListEditor, FontPicker, SshConfigSelect) and PageContext/useSetting hooks bound to staged settings keys.

## Interface
- [context.ts:PageContextValue] [context.ts:PageContext] [context.ts:usePageContext] [context.ts:useSetting] [context.ts:useStagedValue] [context.ts:useSettingsFormFor] [context.ts:useSearchQuery] [context.ts:useLabelMatch] React context and hooks reading/staging settings values for the active page and scope.
- [Form.tsx:SearchLabel] [Form.tsx:Section] [Form.tsx:RowProps] [Form.tsx:Row] [Form.tsx:ToggleProps] [Form.tsx:Toggle] [Form.tsx:NumberInputProps] [Form.tsx:parseNumberInput] [Form.tsx:NumberInput] [Form.tsx:SelectProps] [Form.tsx:Select] [Form.tsx:ColorSwatchesProps] [Form.tsx:ColorSwatches] [Form.tsx:GlobListEditorProps] [Form.tsx:GlobListEditor] [Form.tsx:SettingToggle] [Form.tsx:SettingNumber] [Form.tsx:SettingSelect] [Form.tsx:SettingText] Form primitives and key-bound setting controls.
- [FontPicker.tsx:FontPicker] [SshConfigSelect.tsx:useSshConfigs] [SshConfigSelect.tsx:SshConfigSelect] System font family combobox and SSH configuration selector with link to Tools › SSH Configurations.

## Invariants
- [Form.tsx:parseNumberInput] [Form.tsx:NumberInput] Commits numeric edits only while within [min, max] (and integer when integer is true); invalid draft text is flagged and discarded on blur.
- [Form.tsx:GlobListEditor] Trims entries, deduplicates non-empty items (new Set(next)), and removes an existing item when its inline edit is committed empty.
- [FontPicker.tsx:FontPicker] Filtering system font suggestions never restricts manual font family input, and preserves unknown saved font names.

## Side Effects
- [context.ts:useSetting] [context.ts:useSettingsFormFor] Stages edits in useSettingsDialog via stage(pageId, scope, key, value).
- [FontPicker.tsx:FontPicker] Triggers loadSystemFonts(true) when refreshing system fonts.

## Verification
- Run: `pnpm test`
