# src

## Overview
Maven and Gradle build models, POM XML parser/editor, background sync, Kotlin bridge compiler, and exploded WAR / resource hot-update support.

## Interface
- [lib.rs:BuildTool] / [lib.rs:BuildModel] / [lib.rs:DependencyCoordinate] / [lib.rs:load] / [lib.rs:load_linked] / [lib.rs:dependency_coordinates] / [lib.rs:affects_build] — Unified Maven/Gradle build model loader and build-file watcher predicate.
- [maven.rs:MavenModel] / [maven.rs:MavenProject] / [maven.rs:MavenPlugin] / [maven.rs:MavenDependency] / [maven.rs:MavenProfile] / [maven.rs:Omission] — Maven workspace model and POM / local repository (`~/.m2`) parser.
- [gradle.rs:GradleModel] / [gradle.rs:GradleProject] / [gradle.rs:GradleTask] — Gradle settings/subproject parser and standard lifecycle task model.
- [sync.rs:SyncOptions] / [sync.rs:SyncProgress] / [sync.rs:sync_maven] / [sync.rs:sync_gradle] — Runs Maven (`help:effective-pom`, `dependency:tree`) and Gradle (`--init-script` `openideaSync`) sync and persists `maven.json` / `gradle.json` caches.
- [pom_edit.rs:ArtifactKey] / [pom_edit.rs:Exclusion] / [pom_edit.rs:declaration] / [pom_edit.rs:add_exclusion] / [pom_edit.rs:declaring_poms] — Locates dependency declarations in `pom.xml` files and inserts `<exclusions>` blocks preserving formatting.
- [application_update.rs:ResourceBuild] / [application_update.rs:plan] / [application_update.rs:publish] — Copies modified static resources into target output directories (`Update Resources`).
- [kotlin_bridge.rs:Library] / [kotlin_bridge.rs:ProjectLibraries] / [kotlin_bridge.rs:Outcome] / [kotlin_bridge.rs:has_kotlin] / [kotlin_bridge.rs:build] — Stages and compiles mixed Kotlin/Java modules into `.openidea/kotlin-bridge` jars for JDT LS consumption.
- [web.rs:WebArtifact] / [web.rs:WebBuild] / [web.rs:artifacts] / [web.rs:build] — Discovers `<packaging>war</packaging>` and Gradle `war` modules and assembles exploded WAR directories.
- [compile](./compile/README.md) - Project build planner, compiler output parser, and incremental launch fingerprinter across Maven, Gradle, Cargo, Go, and TypeScript.

## Invariants
- [sync.rs:apply_maven_cache] — Discards cached effective-POM plugins and dependency trees when the FNV-1a hash of build files (`[sync.rs:hash_files]`) does not match the cached hash.
- [kotlin_bridge.rs:build] — Verifies that `generation()` still equals the staged `generation` before publishing `.openidea/kotlin-bridge/modules` so stale builds never overwrite newer outputs.
- [pom_edit.rs:find_dependency] — Ignores `<dependency>` tags nested inside `<dependencyManagement>` when locating direct dependency declarations or inserting `<exclusions>`.

## Side Effects
- [sync.rs:sync_maven] / [sync.rs:sync_gradle] — Spawns `mvn` / `gradle` subprocesses and writes `maven.json`, `gradle.json`, and `openidea-sync.init.gradle` into `cache_dir`.
- [kotlin_bridge.rs:build] — Compiles Kotlin and Java sources via `kotlinc`/`javac` or `mvn`/`gradle` into `<project>/.openidea/kotlin-bridge/`.
- [application_update.rs:publish] / [web.rs:build] — Copies static resources and assembles exploded `WEB-INF` WAR trees under `target/` or `build/`.

## Verification
- Run: `cargo test -p ide-build`
