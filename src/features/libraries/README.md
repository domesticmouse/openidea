# libraries

## Overview
Exposes detected project SDK and dependency source roots (Python site-packages, JDK src.zip, Node node_modules, Cargo, Go, Maven, Gradle) as a read-only External Libraries tree in the Project tool window.

## Interface
- [index.ts:EXTERNAL_LIBRARIES_LABEL] — Display label (`"External Libraries"`) for the project view root.
- [index.ts:touchesManifest] — Tests whether `fsChanged` file paths outside `node_modules` touch a known SDK/package manifest (`package.json`, `Cargo.toml`, `pyproject.toml`, `pyvenv.cfg`, `pom.xml`, `build.gradle`, `go.mod`, etc.).
- [index.ts:librariesRoot] — Builds the `ProjectViewRootContribution` (`id: "externalLibraries"`, `order: 5`) from `LibraryGroup[]`.
- [index.ts:activate] — Registers the External Libraries project view root, `"libraries.reload"` action, project/settings listeners, and debounced `fsChanged` manifest watcher.

## Invariants
- [index.ts:activate] — Clears library roots and skips `commands.sdkProjectLibraries()` when no project is open, `project.trusted === false`, or `!isTauri()`.
- [index.ts:activate] — Discards stale `sdkProjectLibraries` responses using a monotonic `seq` counter and skips re-registering roots when the serialized `LibraryGroup[]` key (`shown`) is unchanged.
- [index.ts:touchesManifest] — Ignores file changes inside `node_modules` and debounces manifest-triggered refreshes by `MANIFEST_REFRESH_DELAY` (500ms).

## Side Effects
- [index.ts:activate] — Calls backend IPC `commands.sdkProjectLibraries()`, registers read-only library roots via `setLibraryRoots("externalLibraries", ...)`, registers the project view root via `registerProjectViewRoot`, registers action `"libraries.reload"`, and subscribes to `useProject` and `onIdeEvent("fsChanged")`.

## Verification
- Run: `pnpm test`
