# model

## Overview
Settings validation schemas (ValidSettings), page tree construction and search/highlight matching (buildPageTree, filterPageTree), and the useSettingsDialog staging/persistence store (stage, applySettings).

## Interface
- [schema.ts:ThemeId] [schema.ts:LocaleId] [schema.ts:CustomKeymapSettings] [schema.ts:ValidSettings] [schema.ts:LanguageServerEntry] [schema.ts:SpellingValid] [schema.ts:ACCENT_PRESETS] [schema.ts:DEFAULT_SETTINGS] [schema.ts:LIMITS] [schema.ts:HEX_COLOR] [schema.ts:validateSettings] [schema.ts:getAt] [schema.ts:setAt] [schema.ts:putAt] [schema.ts:deepEqual] Core settings schema, validator, and dotted-key accessors.
- [behaviorSchema.ts:Antialiasing] [behaviorSchema.ts:OpenProjectIn] [behaviorSchema.ts:NotificationDisplay] [behaviorSchema.ts:ConcreteTheme] [behaviorSchema.ts:AppearanceExtras] [behaviorSchema.ts:DateFormatValid] [behaviorSchema.ts:GeneralExtras] [behaviorSchema.ts:NotificationGroupValid] [behaviorSchema.ts:NotificationsValid] [behaviorSchema.ts:IDLE_LIMITS] [behaviorSchema.ts:DEFAULT_APPEARANCE_EXTRAS] [behaviorSchema.ts:DEFAULT_GENERAL_EXTRAS] [behaviorSchema.ts:DEFAULT_NOTIFICATIONS] [behaviorSchema.ts:validateAppearanceExtras] [behaviorSchema.ts:validateGeneralExtras] [behaviorSchema.ts:validateNotifications] Appearance, General, and Notifications validation schemas.
- [pages.ts:PageNode] [pages.ts:groupKey] [pages.ts:buildPageTree] [pages.ts:flatten] [pages.ts:breadcrumb] [pages.ts:queryTerms] [pages.ts:matchesTerms] [pages.ts:highlightIndices] [pages.ts:labelMatches] [pages.ts:pageTexts] [pages.ts:filterPageTree] Settings page hierarchy builder and multi-term search/highlight matcher.
- [staging.ts:Scope] [staging.ts:StagedChange] [staging.ts:SettingsDialogState] [staging.ts:DEFAULT_PAGE] [staging.ts:SSH_SETTINGS_PAGE] [staging.ts:useSettingsDialog] [staging.ts:splitPageTarget] [staging.ts:openSettings] [staging.ts:settingsTitle] [staging.ts:setNewProjects] [staging.ts:closeSettings] [staging.ts:selectPage] [staging.ts:setQuery] [staging.ts:setPageScope] [staging.ts:stagedValue] [staging.ts:stage] [staging.ts:stageSshSecret] [staging.ts:resetPage] [staging.ts:pendingChanges] [staging.ts:PersistDeps] [staging.ts:PersistResult] [staging.ts:persistChanges] [staging.ts:applySettings] Staged settings store, scope management, and atomic Apply persistence.

## Invariants
- [schema.ts:validateSettings] [behaviorSchema.ts:validateAppearanceExtras] Never throws on malformed or unknown TOML values; clamps numbers to LIMITS and falls back to DEFAULT_SETTINGS.
- [staging.ts:stage] [staging.ts:pendingChanges] Drops staged entries when reverted to baseFor(s, scope), merges child edits into a staged parent map entry, and sorts global changes before project changes so project overrides win on Apply.
- [staging.ts:applySettings] Never writes a staged SSH secret if its SSH configuration was removed or uses auth === "openSsh".

## Side Effects
- [staging.ts:persistChanges] [staging.ts:applySettings] Calls commands.settingsSet, commands.settingsGetGlobal, commands.sshSetSecret, and loadSettings().

## Verification
- Run: `pnpm test`
