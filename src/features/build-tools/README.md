# build-tools

## Overview
Maven and Gradle build tool integration providing dynamic Maven/Gradle tool windows, lifecycle/task trees, background project sync, Execute Goal/Task popup and Run Anything provider, and the Maven Dependency Analyzer editor tab.

## Interface
- [index.ts:activate]
- [index.ts:MAVEN_WHEN]
- [index.ts:GRADLE_WHEN]
- [store.ts:useBuild]
- [store.ts:loadModel]
- [store.ts:syncTool]
- [store.ts:cancelSync]
- [store.ts:resetBuild]
- [store.ts:addProjects]
- [store.ts:unlinkProjects]
- [store.ts:runConfig]
- [store.ts:createRunConfig]
- [store.ts:openAnalyzer]
- [store.ts:useAnalyzer]
- [store.ts:ANALYZER_KIND]
- [store.ts:readSettings]
- [store.ts:updateSettings]
- [store.ts:toggleMavenOffline]
- [store.ts:toggleSkipTests]
- [store.ts:toggleGradleOffline]
- [store.ts:setProfile]
- [store.ts:readHistory]
- [store.ts:pushHistory]
- [store.ts:initialExpanded]
- [store.ts:setExpanded]
- [store.ts:setCurrentProject]
- [model.ts:BuildNode]
- [model.ts:MavenRunOptions]
- [model.ts:GradleRunOptions]
- [model.ts:LIFECYCLE]
- [model.ts:ALL_PHASES]
- [model.ts:mavenRoots]
- [model.ts:mavenChildren]
- [model.ts:gradleRoots]
- [model.ts:gradleChildren]
- [model.ts:gradleSubprojects]
- [model.ts:gradleTaskGroups]
- [model.ts:mavenGoalConfig]
- [model.ts:gradleTaskConfig]
- [model.ts:withGradleOptions]
- [model.ts:goalCompletions]
- [model.ts:taskCompletions]
- [model.ts:expandableKeys]
- [model.ts:isRunnable]
- [model.ts:nodeLabel]
- [model.ts:nodeIcon]
- [model.ts:profileChecked]
- [model.ts:profilesArg]
- [model.ts:profilesList]
- [analyzer.ts:AnalyzedArtifact]
- [analyzer.ts:DependencyUsage]
- [analyzer.ts:DependencyTreeNode]
- [analyzer.ts:AnalyzerFilter]
- [analyzer.ts:analyze]
- [analyzer.ts:filterArtifacts]
- [analyzer.ts:filterTree]
- [analyzer.ts:exclusionFor]
- [analyzer.ts:usageStatus]
- [analyzer.ts:statusText]
- [analyzer.ts:sortedUsages]
- [analyzer.ts:scopesOf]
- [analyzer.ts:artifactKey]
- [analyzer.ts:coordinates]
- [runAnything.ts:buildToolsRunAnything]
- [runAnything.ts:executeBuildLine]
- [runAnything.ts:lineTool]
- [BuildToolView.tsx:MavenView]
- [BuildToolView.tsx:GradleView]
- [BuildToolView.tsx:nodeConfig]
- [ExecutePopup.tsx:ExecutePopupHost]
- [DependencyAnalyzer.tsx:DependencyAnalyzer]

## Invariants
- [index.ts:apply] — Dynamically registers the 'maven' and 'gradle' tool windows and sets 'build.maven' / 'build.gradle' context keys only while the open project's BuildModel includes that build system; automatically triggers an initial syncTool once per (tool, root) when syncedAt == null.
- [store.ts:loadModel] — Skips commands.buildModel() when the project is untrusted (project.trusted === false) and discards out-of-order responses using generation.
- [model.ts:expandableKeys] — Stops recursion at 'dependency' nodes so 'Expand All' opens the Dependencies folder without expanding large transitive dependency trees.

## Side Effects
- [store.ts:loadModel], [store.ts:syncTool] — Loads build models via commands.buildModel() and runs background Maven/Gradle syncs via commands.buildSync with Channel<SyncProgress> and workbench task reporting (upsertTask).
- [store.ts:setLinked], [DependencyAnalyzer.tsx:exclude] — Updates 'languages.java.maven.unlinkedProjects' project settings via commands.settingsSet and modifies pom.xml exclusions via commands.mavenExcludeDependency.
- [store.ts:writeSettings], [store.ts:pushHistory] — Persists per-project build settings (openidea.buildTools:<root>) and command history (openidea.buildTools.history:<tool>:<root>) in localStorage.

## Verification
- Run: `pnpm test`
