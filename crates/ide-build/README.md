# ide-build

## Overview
Crate root for `ide-build`, providing Maven/Gradle project models, background sync, build execution, and JVM resource/WAR/Kotlin-bridge compilation.

## Subdirectories & Boundaries
- [examples](./examples/README.md) - Standalone CLI example (`model`) for inspecting and syncing a project's Maven or Gradle build model.
- [src](./src/README.md) - Maven and Gradle build models, POM XML parser/editor, background sync, Kotlin bridge compiler, and exploded WAR / resource hot-update support.
