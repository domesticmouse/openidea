# coverage

## Overview
Code coverage execution, report importing, editor gutter stripe markers, project tree coverage badges, and the Coverage tool window.

## Interface
- [index.ts:activate]
- [executor.ts:coverageExecutor]
- [executor.ts:installConfig]
- [executor.ts:notifyMissingTool]
- [store.ts:useCoverage]
- [store.ts:Suite]
- [store.ts:MAX_SUITES]
- [store.ts:activeSuite]
- [store.ts:shownFileCoverage]
- [store.ts:addSuite]
- [store.ts:selectSuite]
- [store.ts:closeSuite]
- [store.ts:setCoverageVisible]
- [store.ts:resetCoverage]
- [store.ts:syncCoverageViews]
- [store.ts:ImportedReport]
- [store.ts:importedReports]
- [store.ts:rememberImport]
- [store.ts:forgetImport]
- [store.ts:ReportEntry]
- [store.ts:reportEntries]
- [model.ts:CoverageNode]
- [model.ts:SortKey]
- [model.ts:LineState]
- [model.ts:programName]
- [model.ts:coverageSupports]
- [model.ts:percent]
- [model.ts:formatPercent]
- [model.ts:coverageTree]
- [model.ts:sortTree]
- [model.ts:coverageBadges]
- [model.ts:lineState]
- [gutter.ts:coverageGutter]
- [gutter.ts:coverageMarkers]
- [reports.tsx:importReport]
- [reports.tsx:openManageReports]
- [reports.tsx:ManageReportsOverlay]
- [CoverageView.tsx:CoverageView]
- [CoverageView.tsx:CoverageTabs]
- [CoverageView.tsx:CoverageHeaderActions]

## Invariants
- [store.ts:addSuite] — Retains at most MAX_SUITES (5) recent suites in memory and replaces any earlier suite imported from the same report file.
- [model.ts:coverageTree] — Compacts single-child directory chains (compactDirs = true) and recursively sums linesTotal, linesCovered, branchesTotal, and branchesCovered for every directory node.
- [gutter.ts:coverageField] — Maps coverage gutter LaneMarker positions through document edits (value.map(tr.changes)) until the next coverage run or suite change.

## Side Effects
- [executor.ts:coverageExecutor] — Prepares coverage runs via commands.coveragePrepare and collects coverage reports on process completion via commands.coverageCollect.
- [reports.tsx:importReport], [store.ts:rememberImport] — Opens native file dialogs to import LCOV, JaCoCo XML, or Go coverprofile reports via commands.coverageImport and persists up to MAX_IMPORTED (20) imported report paths per project in localStorage['openidea.coverage.imported.v1:<root>'].
- [store.ts:syncCoverageViews] — Publishes coverageVisible and coverageSuites context keys and updates project tree file badges via setFileDecorations('coverage', ...).

## Verification
- Run: `pnpm test`
