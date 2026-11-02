# ide-plugin

## Overview
Crate root for `ide-plugin`, delivering manifest validation, filesystem-sandboxed asset loading, declarative language/theme contributions, and permission-gated WASI WebAssembly plugin execution.

## Subdirectories & Boundaries
- [src](./src/README.md) - Sandboxed plugin host (`PluginHost`), `plugin.toml` manifest validator, root-confined asset loader, theme contribution loader, and WASI preview 2 WebAssembly (`wasmtime`) runtime with fuel/memory/epoch limits.
