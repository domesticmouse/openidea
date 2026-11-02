# src

## Overview
Sandboxed plugin host (`PluginHost`), `plugin.toml` manifest validator, root-confined asset loader, theme contribution loader, and WASI preview 2 WebAssembly (`wasmtime`) runtime with fuel/memory/epoch limits.

## Interface
- [lib.rs:DEV_PLUGINS_DIR], [lib.rs:PluginHost], [lib.rs:scan_catalog]: Plugin host manager and directory scanner.
- [manifest.rs:API_VERSION], [manifest.rs:MANIFEST_FILE], [manifest.rs:Permission], [manifest.rs:PluginManifest], [manifest.rs:Contributions], [manifest.rs:CommandDecl], [manifest.rs:FileDecl], [manifest.rs:ThemeDecl], [manifest.rs:validate_plugin_id], [manifest.rs:validate_command_id]: `plugin.toml` manifest schema and validators.
- [sandbox.rs:MAX_READ_BYTES], [sandbox.rs:resolve_in_root], [sandbox.rs:read_text]: Canonical path confinement and bounded UTF-8 file reader.
- [theme.rs:ThemeKind], [theme.rs:ThemeDef]: Theme JSON parser and CSS token validator.
- [types.rs:NotificationLevel], [types.rs:PluginSource], [types.rs:PluginStatus], [types.rs:PluginLanguage], [types.rs:PluginTheme], [types.rs:PluginInfo], [types.rs:CatalogPlugin], [types.rs:ActiveTheme], [types.rs:PluginsSnapshot], [types.rs:PluginEvent], [types.rs:PluginEventSink], [types.rs:BufferText], [types.rs:Invocation], [types.rs:Limits]: Public plugin host types and execution limits.

## Invariants
- Plugin manifests must declare a supported `openidea-api` version (`API_VERSION`), valid plugin/command identifiers, and relative contribution paths free of `..` or root prefixes (`[manifest.rs:API_VERSION]`, `[manifest.rs:PluginManifest::parse]`, `[manifest.rs:validate_rel_path]`).
- `sandbox::resolve_in_root` canonicalizes both `root` and `root.join(rel)` and rejects any path escaping `root` (including via symlinks) or any file larger than `MAX_READ_BYTES` (`[sandbox.rs:resolve_in_root]`, `[sandbox.rs:read_text]`).
- Each plugin executes in an isolated `wasmtime::Store` bounded by `Limits` (fuel budget, 5s epoch timeout, and 256 MiB linear memory) and host calls check declared `Permission`s (`ProjectRead`, `EditorRead`, `EditorWrite`) (`[runtime.rs]`, `[types.rs:Limits]`).

## Side Effects
- Scans installed and workspace `.openidea/plugins` directories, copies/removes plugin directories on install/uninstall, and persists `plugins-state.json` (`[lib.rs:PluginHost::reload]`, `[lib.rs:PluginHost::install_from_dir]`, `[lib.rs:PluginHost::save_state]`).
- Executes WASI preview 2 plugin components and forwards `PluginEvent` notifications and commands to the host sink (`[lib.rs:PluginHost]`).

## Verification
- Run: `cargo test -p ide-plugin`
