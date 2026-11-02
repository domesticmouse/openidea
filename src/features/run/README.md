# run

## Overview
Run/Debug configuration management, auto-detection, context/gutter producers, before-launch tasks, remote run targets (Docker/SSH/WSL), Services provider integration, and the Run tool window with ANSI/terminal console, test tree, and stack trace analyzer.

## Interface
- [index.ts:activate] [index.ts:activateSettingsPages] Registers toolbar run.widget (RunWidget), run tool window (RunView, RunTabs), run service provider (runServiceProvider), run.gutter editor contribution, overlays (run.chooser, run.editConfigs, run.dialogs, run.analyzeStackTrace, run.updateApplication), settings pages (externalTools, runTargets), Run Anything provider, and run.*/tools.* actions.
- [store.ts:RunStatus] [store.ts:RunSession] [store.ts:ChooserMode] [store.ts:MAX_TEMPORARY] [store.ts:useRun] [store.ts:getSessionRuntime] [store.ts:selectedConfig] [store.ts:adoptTemporary] [store.ts:loadConfigs] [store.ts:saveConfig] [store.ts:deleteConfig] [store.ts:startRun] [store.ts:stopSession] [store.ts:waitForExit] [store.ts:rerunSession] [store.ts:rerunFailed] [store.ts:runSelectedTests] [store.ts:sendStdin] [store.ts:writeTerminal] [store.ts:resizeTerminal] [store.ts:closeStdin] [store.ts:clearConsole] [store.ts:closeSessionTab] [store.ts:closeSession] [store.ts:runningSessions] [store.ts:resetRuns] [store.ts:modifyTemporary] Run state store, configuration persistence, process lifecycle, and console/test runtime buffers.
- [RunWidget.tsx:RunWidget] [ConfigChooser.tsx:ConfigChooserHost] [EditConfigsDialog.tsx:EditConfigsDialogHost] [AddConfigPopup.tsx:AddConfigPopup] [ConfigForm.tsx:ConfigForm] [EnvDialog.tsx:EnvDialog] [TomcatSettings.tsx:TomcatSettings] Toolbar widget, configuration chooser popup, Edit Configurations modal, and type-specific editors.
- [RunView.tsx:RunTabs] [RunView.tsx:RunView] [ConsoleView.tsx:ConsoleView] [ConsoleView.tsx:foldRows] [TerminalConsole.tsx:TerminalConsole] [TestTreeView.tsx:TestTreeView] [FindBar.tsx:ConsoleFindBar] Run tool window tabs, split test/console view, ANSI/folded console view, embedded terminal console, and console find bar.
- [console.ts:ConsoleModel] [terminalOutput.ts:TerminalOutput] [links.ts:findOutputLinks] [links.ts:jvmFrameTarget] [segments.ts:segmentLine] [find.ts:findInLines] [navigate.ts:openTarget] [navigate.ts:openUrl] [stackTrace.tsx:openStackTraceDialog] [stackTrace.tsx:StackTraceDialogHost] Streamed output line buffer, ANSI/link/highlight segmentation, source navigation, and stack trace analyzer.
- [context.ts:contextConfig] [context.ts:testConfigFor] [targets.ts:detectRunTargets] [targets.ts:targetConfig] [jvmTargets.ts:scanJvmTargets] [producers.ts:produceContext] [producers.ts:produceContextChoices] [producers.ts:produceDirectory] [producers.ts:fileTargets] [contextRun.ts:runFromContext] [runGutter.ts:runGutter] Caret/file/directory context producers and CodeMirror run gutter.
- [types.ts:registerBuiltinRunConfigTypes] [jvmTypes.ts:applicationType] [jvmTypes.ts:springBootType] [goTypes.ts:goBuildType] [goTypes.ts:goTestType] [dotnetTypes.ts:dotnetProjectType] [tomcat.ts:tomcatType] [application.ts:applicationConfig] [goGenerate.ts:goGenerateConfig] [goActions.ts:runGoGenerate] [beforeLaunch.ts:runBeforeLaunch] [applicationUpdate.ts:updateRunningApplication] [remote.ts:RunTargetsSettings] [services.tsx:runServiceProvider] [externalTools.ts:ExternalToolsSettings] Built-in run configuration types, before-launch runner, hot-swap/update application dialog, remote targets, Services tree integration, and external tools.

## Invariants
- [store.ts:adoptTemporary] Retains at most MAX_TEMPORARY (5) temporary configurations in LRU order while preserving any temporary config with an active running/starting session.
- [console.ts:ConsoleModel] Caps buffered console lines at MAX_LINES (50,000), breaks single lines longer than MAX_LINE_LENGTH (8,000), and never trims the currently open partial line during cycle-buffer trimming.
- [store.ts:startRun] Single-instance configurations (!config.allowParallel) reuse their existing running tab and confirm restart when confirmRerun is enabled, whereas compound configurations (config.kind === "compound") fan out to member configs with cycle detection.

## Side Effects
- [store.ts:startRun] [store.ts:stopSession] [store.ts:saveConfig] [store.ts:deleteConfig] Invokes commands.runStart, commands.runStop, commands.runStdin, commands.runResize, commands.runCloseStdin, commands.runConfigs, commands.runSaveConfig, commands.runDeleteConfig, and streams output via Tauri Channel<OutputChunk>.
- [store.ts:persistSelection] [store.ts:persistTemporary] [TestTreeView.tsx:toggle] [serviceOrder.ts:saveServiceOrder] Persists selected config, temporary configs, test filters, and service display order to localStorage.

## Verification
- Run: `pnpm test`
