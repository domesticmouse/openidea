# detect

## Overview
Build-system run configuration detectors for CMake File API v1 codemodels and .NET project/solution files.

## Interface
- [cmake.rs:detect] — Scans `<root>/build` and `<root>/cmake-build-*` for CMake File API v1 replies (`index-*.json` and `codemodel-v2`) or falls back to `CMakeLists.txt` `add_executable` targets to produce `RunConfig` entries.
- [cmake.rs:prepare] — Clears stale `CMakeCache.txt` and `CMakeFiles` when the cached `CMAKE_GENERATOR` disagrees with `-G`, and writes the `.cmake/api/v1/query/client-openidea/codemodel-v2` query file before configuring.
- [dotnet.rs:detect] — Walks the workspace up to depth 4 (or solution project paths from `.sln`/`.slnx`) to discover runnable `.csproj` projects (`OutputType=Exe`/`WinExe` or `Microsoft.NET.Sdk.Web`/`Worker` or `IsTestProject`) and `Properties/launchSettings.json` `Project` profiles.

## Invariants
- [cmake.rs:reply_file] Rejects any reply filename that is not a single normal path component within `.cmake/api/v1/reply`.
- [cmake.rs:targets] Ignores CMake File API codemodels whose canonical source path does not match the canonical workspace root.
- [dotnet.rs:detect] Skips hidden directories and `bin`, `obj`, `node_modules`, `target`, `build`, `dist`, `vendor`, and `packages` during directory traversal.

## Side Effects
- [cmake.rs:prepare] Deletes `CMakeCache.txt` and `CMakeFiles` on generator mismatch and creates `.cmake/api/v1/query/client-openidea/codemodel-v2` on disk.
- [cmake.rs:detect] Reads `.cmake/api/v1/reply/*.json`, `CMakeCache.txt`, and `CMakeLists.txt` from disk.
- [dotnet.rs:detect] Reads `.sln`, `.slnx`, `.csproj`, and `Properties/launchSettings.json` files from disk.

## Verification
- Run: `cargo test -p ide-run`
