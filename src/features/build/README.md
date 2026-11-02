# build

## Overview
Build Output tool window, title bar build widget, and project build/rebuild orchestration over Cargo, Gradle, Maven, Go, npm, CMake, and javac.

## Interface
- [index.ts:activate] Registers the build tool window, BuildTitleItem title bar widget, and build.project / build.rebuildProject / build.cancel / build.reimport actions.
- [store.ts:BuildState] [store.ts:useBuild] [store.ts:buildProject] [store.ts:cancelBuild] [store.ts:reimportProject] [store.ts:clearBuild] [store.ts:openProblem] [store.ts:installBuildEvents] Zustand store and IPC wrappers for commands.buildProject, commands.buildCancel, and commands.buildDiscover.
- [model.ts:PhaseStatus] [model.ts:BuildPhase] [model.ts:ProblemGroup] [model.ts:detectSummary] [model.ts:problemCounts] [model.ts:groupProblems] [model.ts:filterProblems] [model.ts:visibleErrors] [model.ts:problemKey] [model.ts:locationLabel] [model.ts:formatDuration] Pure build phase, problem grouping, and summary formatting helpers.
- [BuildView.tsx:BuildView] [BuildView.tsx:BuildHeader] [BuildTitleItem.tsx:BuildTitleItem] Build Output tool window view and title bar build action button.

## Invariants
- [store.ts:buildProject] Ignores concurrent build requests while running is true unless an explicit cancelBuild() precedes it.
- [model.ts:groupProblems] Groups compiler diagnostics by file path in first-seen order while separating fileless messages.

## Side Effects
- [store.ts:buildProject] [store.ts:cancelBuild] [store.ts:reimportProject] Invokes IPC commands.buildProject, commands.buildCancel, and commands.buildDiscover, and saves modified documents before building.

## Verification
- Run: `pnpm test`
