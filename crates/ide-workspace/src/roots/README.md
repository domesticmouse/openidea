# roots

## Overview
Computes and auto-detects project folder classifications (`source`, `test`, `resource`, `testResource`, `generated`, `excluded`) across user settings and Cargo, Maven, Gradle, Node/TypeScript, Python, Go, and unmanaged Java project layouts.

## Interface
- [mod.rs:RootKind], [mod.rs:SourceFolder], [mod.rs:ProjectRoots]: Folder classification enum, root descriptor (`path`, `kind`, `auto_detected`, `package_prefix`), and resolved root table with query helpers (`compute`, `root_for`, `is_excluded`, `is_test`, `is_generated`).
- [mod.rs:normalize], [mod.rs:is_test_file_name]: Project-relative forward-slash path normalizer and filename-based test file heuristic (`*_test.*`, `*.test.*`, `*.spec.*`, `test_*.*`, `*Test.java`, `*Tests.java`, `*IT.java`).
- [detect.rs:affects_roots], [detect.rs:detect]: Checks whether a changed path can affect root detection and scans a project tree to auto-detect source/test/resource/generated/excluded folders.
- [java.rs:unmanaged_java_source_paths]: Detects source roots for standalone Java projects that lack Maven, Gradle, or Eclipse build descriptors.

## Invariants
- [mod.rs:ProjectRoots::compute]: User-configured marks in `ProjectStructureSettings` always override auto-detected roots at the same normalized path; paths in `ps.unmarked` suppress auto-detected roots; and the resulting `roots` list is sorted lexicographically by `path`.
- [mod.rs:ProjectRoots::root_for]: Matches the deepest (longest `path`) root containing the queried relative path so nested roots override enclosing roots.
- [detect.rs:detect]: Bounds recursive directory scanning to `MAX_DEPTH = 6` and `MAX_DIRS = 4000`, skipping `SKIP_DIRS` (`.git`, `.hg`, `.svn`, `.openidea`, `.idea`, `.vscode`, `node_modules`, `target`, `dist`, `build`, `out`, `.gradle`, `.next`, `.nuxt`, `.turbo`, `.venv`, `venv`, `__pycache__`).
- [java.rs:unmanaged_java_source_paths]: Returns `None` if `server_root` contains `pom.xml`, `build.gradle`, `build.gradle.kts`, `settings.gradle`, `settings.gradle.kts`, `.project`, or `.classpath`; otherwise infers source roots from `package` declarations or common Java directory conventions (`src/main/java`, `src`, project root).

## Side Effects
- [detect.rs:detect], [java.rs:unmanaged_java_source_paths], [maven.rs:detect_maven_dir], [gradle.rs:detect_gradle_dir]: Walk project directories and read `pom.xml`, `build.gradle`, `build.gradle.kts`, and `.java` files from disk.

## Verification
- Run: `cargo test -p ide-workspace`
