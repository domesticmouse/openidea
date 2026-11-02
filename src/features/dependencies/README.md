# dependencies

## Overview
Dependency manifest editor assistance providing outdated-version quick fixes (Cargo.toml, package.json, pom.xml, build.gradle, go.mod, pyproject.toml, requirements.txt) and package registry version completions.

## Interface
- [index.ts:activate]
- [index.ts:dependencyFixes]
- [index.ts:versionCompletions]

## Invariants
- [index.ts:parseData] — Generates dependency upgrade quick fixes only for diagnostics whose source === 'dependencies' and whose data payload carries string current and latest versions.
- [index.ts:versionCompletions] — Queries registry versions only when ctx.path matches MANIFEST_FILE, returns at most MAX_VERSIONS (30) items, and skips implicit completions when the version prefix is empty.

## Side Effects
- [index.ts:activate] — Registers the dependency code action provider (registerCodeActionProvider), completion provider (registerCompletionProvider), and 'dependencies.updateAll' action (commands.depsUpdateAll + commands.applyWorkspaceEdit).
- [index.ts:versionCompletions] — Fetches available package versions from the backend via commands.depsVersions.

## Verification
- Run: `pnpm test`
