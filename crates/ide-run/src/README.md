# src

## Overview
Core implementation of run configuration auto-detection, `.openidea/run.toml` persistence, PTY/pipe process lifecycle management, Java command-line shortening, JUnit XML parsing, and Tomcat `CATALINA_BASE` staging.

## Interface
- [lib.rs:RunConfig] — Serializable run/debug configuration model (`id`, `name`, `command`, `args`, `cwd`, `env`, `env_file`, `before_launch`, `compound`, `single_instance`, `stop_command`, `stop_signal`, `input_file`, `output_file`, `pty`, `shared`, `extras`).
- [lib.rs:BeforeLaunchTask] — Pre-launch task enum (`Shell`, `Config`, `Build`).
- [lib.rs:RunExtras] — Language- and framework-specific configuration extensions (`Java`, `Go`, `Tomcat`, `Docker`, `Ssh`, `Coverage`).
- [lib.rs:OutputChunk] — Streamed process output chunk (`Stdout`, `Stderr`, `System`).
- [lib.rs:RunInfo] — Snapshot of a running or finished process (`id`, `config_id`, `name`, `pid`, `running`, `exit_code`, `started_at`, `finished_at`).
- [lib.rs:detect], [lib.rs:detect_excluding], [lib.rs:detect_with_profiles] — Auto-detects `RunConfig`s across Cargo, npm/yarn/pnpm/deno/bun, Python, Go, Maven, Gradle, CMake, .NET, Make, Just, Composer, Mix, Zig, Swift, sbt, Stack/Cabal, Rake, Taskfile, and shell scripts.
- [lib.rs:load_user_configs], [lib.rs:save_user_configs], [lib.rs:load_suppressed_config_ids], [lib.rs:save_user_configs_with_removals], [lib.rs:load_templates], [lib.rs:save_templates], [lib.rs:user_configs_path] — Loads and persists shared (`.openidea/run.toml`) and local (`.openidea/run.local.toml`) run configurations, suppressed auto-detected IDs, and per-kind templates.
- [lib.rs:system_env], [lib.rs:resolved_env] — Captures inherited system environment variables and resolves effective run environment maps (`env_file` + inline `env`).
- [process.rs:ProcessManager], [process.rs:StartOptions] — Manages active and finished child processes (`new`, `start`, `resize`, `write_stdin`, `close_stdin`, `stop`, `stop_with_grace`, `list`, `kill_project`, `kill_all`) over PTYs (`portable-pty`) or standard pipes.
- [dotnet.rs:Project], [dotnet.rs:Profile], [dotnet.rs:parse_project], [dotnet.rs:parse_profiles], [dotnet.rs:solution_projects], [dotnet.rs:resolve], [dotnet.rs:target_path] — Parses `.csproj`, `launchSettings.json`, `.sln`, and `.slnx` metadata and resolves built `.dll` target paths.
- [envfile.rs:parse], [envfile.rs:load] — Parses `.env` files (`export`, quotes, escapes, comments) into key-value pairs.
- [go.rs:Plan], [go.rs:plan], [go.rs:package_sources], [go.rs:prepare_test_file], [go.rs:work_modules] — Plans `go run`/`go test` invocations, filters non-`_test.go` package sources, stages single-file Go tests, and parses `go.work` `use` directives.
- [gradle_jvm.rs:init_script], [gradle_jvm.rs:default_dir], [gradle_jvm.rs:write_init_script] — Generates and writes Gradle init scripts (`--init-script`) that inject JVM arguments and working directories into `JavaExec` and `Test` tasks.
- [java_cmdline.rs:Shortening], [java_cmdline.rs:Shortened], [java_cmdline.rs:shorten], [java_cmdline.rs:choose], [java_cmdline.rs:shortening_for], [java_cmdline.rs:java_feature_version], [java_cmdline.rs:write_pathing_jar], [java_cmdline.rs:manifest], [java_cmdline.rs:argfile_text] — Shortens over-limit Java command lines via `@argfile` (Java 9+) or pathing `MANIFEST.MF` JARs.
- [junit.rs:TestCaseStatus], [junit.rs:TestCaseReport], [junit.rs:parse_report], [junit.rs:report_dirs], [junit.rs:read_reports] — Parses Surefire/Failsafe/Gradle `TEST-*.xml` JUnit reports modified after a timestamp.
- [python_path.rs:extend] — Prepends source/content roots to `PYTHONPATH` without duplicating existing entries.
- [tomcat.rs:TomcatHome], [tomcat.rs:validate_home], [tomcat.rs:prepare], [tomcat.rs:update], [tomcat.rs:script_command], [tomcat.rs:explode], [tomcat.rs:patch_server_xml] — Validates Tomcat installations and stages isolated per-run `CATALINA_BASE` directories with patched `server.xml` ports and exploded WAR contexts.
- [which.rs:executable_names], [which.rs:find_executable], [which.rs:find_executable_in] — Resolves executables across `PATH` and `PATHEXT`.
- [wrap.rs:PythonCommand], [wrap.rs:run_dir], [wrap.rs:program_name], [wrap.rs:is_python], [wrap.rs:search_path], [wrap.rs:node_bin], [wrap.rs:node_package], [wrap.rs:node_package_version], [wrap.rs:find_tool], [wrap.rs:probe], [wrap.rs:python_command], [wrap.rs:npm_script], [wrap.rs:passthrough_args], [wrap.rs:go_module], [wrap.rs:new_output_dir] — Toolchain inspection helpers used by coverage, profiling, and debug wrappers.
- [detect](./detect/README.md) - Build-system run configuration detectors for CMake File API v1 codemodels and .NET project/solution files.

## Invariants
- [detect.rs:detect_with_profiles] Never fails on malformed build manifests and deduplicates detected configurations by `RunConfig::id`.
- [config.rs:load] Shared configurations in `.openidea/run.toml` take precedence over `.openidea/run.local.toml` entries with the same `id`.
- [process.rs:MAX_FINISHED] `ProcessManager` retains at most 20 finished `RunInfo` records per manager instance.
- [java_cmdline.rs:manifest] Wraps `Class-Path` header lines in `MANIFEST.MF` at 72 UTF-8 bytes without splitting multi-byte characters.
- [tomcat.rs:prepare] Never modifies `CATALINA_HOME`; all generated configuration and exploded webapps are isolated inside `CATALINA_BASE`.

## Side Effects
- [process.rs:ProcessManager::start] Spawns OS child processes (in their own process group on Unix or PTY via `portable-pty`), pipes stdin/stdout/stderr, and optionally redirects output to files.
- [process.rs:ProcessManager::stop_with_grace] Executes custom stop commands and sends `SIGINT`/`SIGTERM`/`SIGKILL` (or `taskkill /T /F` on Windows) to process groups.
- [config.rs:save] Writes `.openidea/run.toml`, `.openidea/run.local.toml`, and `.openidea/.gitignore`.
- [gradle_jvm.rs:write_init_script] Writes temporary `.gradle` init scripts to the OS temp directory.
- [java_cmdline.rs:shorten] Writes temporary `@argfile` and pathing `.jar` files to the temp directory.
- [tomcat.rs:prepare] Creates `CATALINA_BASE` directory trees, extracts `.war` archives, and binds ephemeral localhost TCP sockets in `[tomcat.rs:free_port]`.

## Verification
- Run: `cargo test -p ide-run`
