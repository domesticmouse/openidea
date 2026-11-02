# ide-host

## Overview
Transport-agnostic IDE backend host crate (`ide-host`) shared by the Tauri desktop app and the headless `openidea-server` binary.

## Subdirectories & Boundaries
- [src](./src/README.md) - Core `Host` and `ProjectState` orchestration layer that binds workspace state, trust enforcement, environment probing, notebooks, Kotlin compiler bridge, HTTP client state, and JSON-RPC dispatch.
