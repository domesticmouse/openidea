# src

## Overview
Managed external tool registry, installer, and updater for language servers, DAP debug adapters, Prettier, Lombok, Eclipse Temurin JDK/JRE builds, and `openidea-server` remote binaries.

## Interface
- [lib.rs:Tools], [lib.rs:ToolStatus], [lib.rs:ToolUpdate], [lib.rs:InstallEvent], [lib.rs:tools_dir], [lib.rs:clear_quarantine] — Tool manager (`new`, `with_paths`, `root`, `bin_dir`, `system_path`, `search_path`, `which`, `command`, `lombok_jar`, `status`, `status_of`, `list`, `check_updates`, `install`, `uninstall`, `formatter_command`, `jdk_releases`, `download_jdk`, `download_java_runtime`).
- [registry.rs:ToolKind], [registry.rs:ReleaseSource], [registry.rs:Method], [registry.rs:ToolSpec], [registry.rs:TOOLS], [registry.rs:PLUGIN_HOSTS], [registry.rs:TYPESCRIPT], [registry.rs:find] — Declarative catalog of all installable language servers, debuggers, formatters, and plugins and their primary/fallback installation methods (`Bundled`, `Rustup`, `Npm`, `Go`, `PipVenv`, `PipUser`, `PipTarget`, `Gem`, `DotnetTool`, `Release`).
- [platform.rs:Os], [platform.rs:Arch], [platform.rs:Platform], [platform.rs:AssetRule], [platform.rs:rule], [platform.rs:any_arch], [platform.rs:select_asset], [platform.rs:glob_match] — Host OS/architecture detection and release asset glob matching with automatic ARM64-to-x64 emulation fallback on macOS and Windows.
- [sources.rs:Download], [sources.rs:github_latest_url], [sources.rs:github_pick], [sources.rs:hashicorp_latest_url], [sources.rs:hashicorp_pick], [sources.rs:JDTLS_MILESTONES], [sources.rs:jdtls_latest_milestone], [sources.rs:jdtls_download], [sources.rs:openvsx_latest_url], [sources.rs:openvsx_platform_url], [sources.rs:openvsx_pick], [sources.rs:maven_metadata_url], [sources.rs:maven_pick], [sources.rs:parse_checksum], [sources.rs:sha256_file] — Release metadata parsers and SHA-256 checksum verifiers for GitHub, HashiCorp, Eclipse JDTLS, Open VSX, and Maven Central.
- [archive.rs:ArchiveKind], [archive.rs:extract], [archive.rs:make_executable], [archive.rs:locate], [archive.rs:env_wrapper], [archive.rs:link_executable] — Safe archive extraction (`.zip`/`.vsix`, `.tar.gz`, `.tar.xz`, `.tar.bz2`, `.gz`, `Raw`) with symlink-escape validation and wrapper script generation.
- [jdk.rs:JdkVendor], [jdk.rs:TEMURIN], [jdk.rs:vendors], [jdk.rs:JdkReleases], [jdk.rs:DownloadedJdk], [jdk.rs:jdks_dir], [jdk.rs:adoptium_releases_url], [jdk.rs:parse_releases], [jdk.rs:adoptium_assets_url], [jdk.rs:adoptium_pick], [jdk.rs:jdk_dir_name], [jdk.rs:find_jdk_home] — Adoptium v3 API client and extractor for Eclipse Temurin JDK and JRE runtimes.
- [formatter.rs:find_up], [formatter.rs:arguments], [formatter.rs:run] — Resolves Prettier (custom path, nearest `node_modules/prettier`, managed tool, or `PATH`) and formats unsaved document text over stdin/stdout.
- [process.rs:Emit], [process.rs:Http], [process.rs:run_streamed], [process.rs:run_collect], [process.rs:run_quiet], [process.rs:progress_event] — Process-group runner with cancellation/timeout enforcement and `curl`-backed HTTPS client.
- [server.rs:PLATFORMS], [server.rs:asset_name], [server.rs:cached_path], [server.rs:fetch] — Downloads and SHA-256 verifies `openidea-server-<os>-<arch>.gz` binaries into `<app_data>/server/<version>/<os>-<arch>/openidea-server`.

## Invariants
- [lib.rs:InstallGuard::acquire] Prevents concurrent installations of the same tool or JDK key within the process using a global `Mutex<HashSet<String>>`.
- [lib.rs:fetch_verified], [server.rs:fetch] Enforces SHA-256 verification whenever a release source provides an inline digest or checksum URL, and requires SHA-256 verification unconditionally for `openidea-server` binaries.
- [archive.rs:validate_link], [archive.rs:reject_symlink_components] Rejects any archive entry or symlink that traverses outside the destination root or exceeds 40 symlink hops, and strips setuid/setgid/sticky bits during extraction.
- [lib.rs:Tools::search_path] Managed tools in `<app_data>/tools/bin` and `<app_data>/tools/node_modules/.bin` are prepended ahead of the system `PATH` only when the user has not explicitly uninstalled the tool (`marker_file(name, ".uninstalled")`).

## Side Effects
- [process.rs:Http], [process.rs:run_collect] Spawns `curl`, `npm`/`pnpm`, `pip`/`uv`, `go`, `rustup`, `gem`, `dotnet`, `tar`, and `xattr` child processes in their own process groups and kills the entire group on cancellation or timeout.
- [lib.rs:Tools::install], [lib.rs:Tools::uninstall], [jdk.rs:Tools::download_java], [server.rs:fetch] Downloads archives and writes unpacked toolchains, JDKs, shims, and `.version`/`.method`/`.uninstalled` markers under `<app_data>/tools/` and `<app_data>/server/`.

## Verification
- Run: `cargo test -p ide-tools`
