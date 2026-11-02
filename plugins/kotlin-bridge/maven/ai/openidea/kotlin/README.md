# kotlin

## Overview
Maven lifecycle participant (`IsolatedOutput`) that redirects reactor build and Kotlin compiler outputs into staging directories and emits `modules.json` dependency and goal metadata.

## Interface
- [IsolatedOutput.java:IsolatedOutput] — Public `AbstractMavenLifecycleParticipant` registered under role-hint `openidea-kotlin-output`
- [IsolatedOutput.java:IsolatedOutput.afterProjectsRead] — Reads `-Dopenidea.kotlin.staging`, redirects project build/output directories and `kotlin-maven-plugin` / `maven-compiler-plugin` configurations, and proxies the session `ExecutionListener`
- [IsolatedOutput.java:IsolatedOutput.afterSessionEnd] — Writes `<staging-parent>/modules.json` containing resolved Maven artifact closures, output directories, `hasKotlin`, and `kotlinGoals` statuses

## Invariants
- [IsolatedOutput.java:IsolatedOutput.afterProjectsRead] — Throws `MavenExecutionException("Missing Kotlin staging directory")` if `openidea.kotlin.staging` user property is unset
- [IsolatedOutput.java:redirect] — Forces `myIncremental=false` and redirects `output`, `testOutput`, and `incrementalCachesRoot` into per-project staging subdirectories
- [IsolatedOutput.java:hasKotlin] — Traverses module directories without following symlinks (`LinkOption.NOFOLLOW_LINKS`) while skipping `target`, `build`, `out`, `bin`, `.git`, `node_modules`, `.openidea`, and nested reactor module base directories

## Side Effects
- [IsolatedOutput.java:IsolatedOutput.afterProjectsRead] — Mutates in-memory `MavenProject` build directories, plugin DOM configurations, and the session `ExecutionListener`
- [IsolatedOutput.java:IsolatedOutput.afterSessionEnd] — Creates the staging directory and writes `modules.json` on disk

## Verification
- Run: `JAVA_HOME=/path/to/project/jdk python3 plugins/kotlin-bridge/tests/verify.py /path/to/jdtls`
