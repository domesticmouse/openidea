# src

## Overview
Discovers installed JDKs, Python interpreters/virtualenvs, Go/Rust/Node/Ruby/PHP/Elixir/Haskell/Julia/OCaml/.NET toolchains, external library roots, and applies user SDK overrides to language servers, build tools, terminals, and run/debug configurations.

## Interface
- [lib.rs:SdkKind], [lib.rs:Sdk], [lib.rs:venv_of] — SDK kind enum (`Python`, `Node`, `Rust`, `Go`, `Java`, `Kotlin`, `Maven`, `Gradle`, `C`, `Ruby`, `Php`, `Elixir`, `Dotnet`, `Haskell`, `Julia`, `Ocaml`, `Dart`, `Zig`) and discovered SDK descriptor.
- [discover.rs:detect], [discover.rs:jdk_sdk] — Scans project roots, virtualenvs, version managers (`pyenv`, `nvm`, `fnm`, `volta`, `asdf`, `mise`, `rustup`, `sdkman`, `rbenv`, `rvm`, `ghcup`, `juliaup`, `opam`), and `PATH` for available SDKs.
- [apply.rs:setting_path], [apply.rs:python_interpreter], [apply.rs:project_jdk], [apply.rs:jdtls_jdk], [apply.rs:maven_command], [apply.rs:gradle_command], [apply.rs:gradle_env], [apply.rs:maven_options], [apply.rs:maven_local_repository], [apply.rs:tsdk], [apply.rs:configure_maven_import], [apply.rs:tool_command], [apply.rs:resolve_run_command], [apply.rs:configure_server], [apply.rs:language_servers], [apply.rs:with_custom_servers], [apply.rs:server_launch_overrides], [apply.rs:server_protocol_overrides], [apply.rs:apply_to_run_configs], [apply.rs:terminal_activation] — Applies workspace `Settings` SDK selections and overrides to LSP servers, Maven/Gradle commands, run configurations, and terminal shells.
- [go.rs:custom_tags], [go.rs:build_settings], [go.rs:apply_to_run], [go.rs:apply_to_debug], [go.rs:debug_environment], [go.rs:module_environment] — Applies Go `GOOS`/`GOARCH`/`CGO_ENABLED`/build tags, `GONOPROXY`/`GONOSUMDB`/`GOPRIVATE`, and vendoring flags to `go`, `gopls`, and `dlv`.
- [godot.rs:COMMAND], [godot.rs:NOT_FOUND], [godot.rs:find], [godot.rs:is_project] — Locates the Godot 4 editor executable across settings, `PATH`, and OS application directories.
- [java.rs:Jdk], [java.rs:JDTLS_MIN_JAVA], [java.rs:parse_release], [java.rs:jdk_at], [java.rs:add_jdk_root], [java.rs:is_downloaded], [java.rs:rescan_jdks], [java.rs:installed_jdks], [java.rs:jdtls_jdks], [java.rs:language_server_runtimes_dir], [java.rs:cap_java_home], [java.rs:jdtls_runtime], [java.rs:jdtls_min_java], [java.rs:runtimes], [java.rs:configure_jdtls], [java.rs:missing_jdk_message], [java.rs:java_executable], [java.rs:pick_jdk], [java.rs:project_java_version] — Discovers and caches JDK installations, parses `release` files and project Java versions (`pom.xml`, `build.gradle`, `.java-version`, `.sdkmanrc`, `.tool-versions`, `.mise.toml`), and configures JDTLS runtimes.
- [jdtls.rs:JdtlsInstall], [jdtls.rs:DEFAULT_VM_ARGS], [jdtls.rs:jdtls_home], [jdtls.rs:config_dir_names], [jdtls.rs:find_install], [jdtls.rs:default_vm_args], [jdtls.rs:add_default_vm_args], [jdtls.rs:java_args], [jdtls.rs:launch_directly] — Resolves Eclipse JDTLS equinox launcher JARs and OS configuration directories (`config_mac_arm`, `config_linux`, `config_win`, etc.) so JDTLS can be launched directly via `java -jar` without Python.
- [libraries.rs:LibraryGroup], [libraries.rs:LibraryRoot], [libraries.rs:BuildDependency], [libraries.rs:project_libraries], [libraries.rs:invalidate_go], [libraries.rs:python_libraries] — Enumerates and caches external dependency roots for Rust (`cargo metadata`), Node (`node_modules`), Python (`site-packages`), Go (`go list -m`), and Maven/Gradle (`~/.m2`, `~/.gradle`).
- [locate.rs:LocatedTool], [locate.rs:parse_tool_version], [locate.rs:locate_tools], [locate.rs:locate_tools_in], [locate.rs:compile_dir] — Probes well-known install locations for external tools (`git`, `docker`, `mvn`, `gradle`, `cmake`, `clangd`, `dlv`, `gdb`, `lldb-dap`, `netcoredbg`) and caches `--version` output by `(path, mtime)`.
- [lombok.rs:LOMBOK_MIN], [lombok.rs:LombokAgent], [lombok.rs:agent_in_args], [lombok.rs:lombok_agent], [lombok.rs:uses_lombok] — Detects Lombok usage in Maven/Gradle/source files and resolves a JDTLS-compatible `lombok.jar` (`>= 1.18.30`) for `-javaagent:` injection.
- [pinned.rs:pinned_version], [pinned.rs:pinned_install] — Reads `.nvmrc`, `.node-version`, `.python-version`, `rust-toolchain.toml`, `.go-version`, `.ruby-version`, `.tool-versions`, and `.mise.toml` to resolve pinned toolchain installations.
- [python.rs:PythonPackage], [python.rs:create_venv], [python.rs:list_packages], [python.rs:install_package], [python.rs:uninstall_package] — Creates Python virtual environments (`uv venv` or `python -m venv`) and lists, installs, or uninstalls packages via `uv pip` or `pip`.

## Invariants
- [java.rs:JDKS] Discovered JDKs are cached in a process-wide `OnceLock<RwLock<Vec<Jdk>>>` and only rescanned on `rescan_jdks` or `add_jdk_root`.
- [java.rs:jdtls_min_java] Parses `Bundle-RequiredExecutionEnvironment: JavaSE-N` from JDTLS `plugins/org.eclipse.jdt.ls.core_*.jar` `META-INF/MANIFEST.MF` (defaulting to `JDTLS_MIN_JAVA = 21`) and caches it per JDTLS home.
- [libraries.rs:CACHE] Caches `LibraryGroup` lists per workspace root and invalidates when watched manifest mtimes/sizes (`Cargo.lock`, `package.json`, `go.mod`, `pyvenv.cfg`) change.
- [lombok.rs:LOMBOK_MIN] Rejects project `lombok.jar` versions older than `1.18.30` for JDTLS `-javaagent` attachment because older versions crash on JDK 21+ javac internals.

## Side Effects
- [discover.rs:detect], [locate.rs:locate_tools] Spawns toolchain binaries with `--version` / `version` flags using bounded timeouts and caches results in memory.
- [apply.rs:configure_java_compiler] Writes Eclipse JDT compiler preferences (`compiler-defaults.prefs`) into the OS temp directory (`openidea-jdtls/`).
- [libraries.rs:project_libraries] Invokes `cargo metadata`, `go env GOMODCACHE`, `go list -m`, and `python -c` subprocesses to resolve external library paths.
- [python.rs:create_venv], [python.rs:install_package], [python.rs:uninstall_package] Spawns `uv` or `python -m venv`/`pip` subprocesses that create virtualenvs and mutate installed packages on disk.

## Verification
- Run: `cargo test -p ide-sdk`
