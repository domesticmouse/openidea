# kotlin

## Overview
JDT LS delegate command (`KotlinLibraries`) managing `ai.openidea.kotlin=true` binary classfolders on Eclipse JDT project classpaths and restoring them after Maven reimports.

## Interface
- [KotlinLibraries.java:KotlinLibraries] — Public `IDelegateCommandHandler` handling `openidea.kotlin.replace`
- [KotlinLibraries.java:KotlinLibraries.executeCommand] — Replaces managed Kotlin binary library entries across workspace Java projects from a single snapshot argument `[{projectUri, libraries: [{path, test}]}]` and returns the count of updated projects

## Invariants
- [KotlinLibraries.java:KotlinLibraries.executeCommand] — Rejects commands other than `openidea.kotlin.replace`, argument lists of length != 1, or library paths that are not existing absolute directories
- [KotlinLibraries.java:KotlinLibraries.reconcile] — Removes only `CPE_LIBRARY` classpath entries carrying extra attribute `ai.openidea.kotlin="true"` (`[KotlinLibraries.java:OWNER]`); all other `IClasspathEntry` items are preserved verbatim
- [KotlinLibraries.java:LOCK] — Synchronizes `desired` state updates, `ElementChangedEvent.POST_CHANGE` listener registration, and background `reconcileJob` execution under `LOCK`

## Side Effects
- [KotlinLibraries.java:KotlinLibraries.executeCommand] — Registers a `JavaCore.addElementChangedListener` (`POST_CHANGE`) that schedules `reconcileJob` with a 200ms delay
- [KotlinLibraries.java:KotlinLibraries.reconcile] — Mutates Eclipse JDT project classpaths via `IJavaProject.setRawClasspath`

## Verification
- Run: `JAVA_HOME=/path/to/project/jdk python3 plugins/kotlin-bridge/tests/verify.py /path/to/jdtls`
