# src-tauri

## Overview
Root package for the `openidea` Tauri desktop application, containing the Cargo manifest, Tauri configuration, build-time environment and Windows manifest injection (`build.rs`), and the `src/` desktop shell implementation.

## Interface
- [build.rs:main]: Cargo build script that injects `OPENIDEA_WORKTREE_ROOT`, links the Windows Common-Controls v6 manifest on MSVC targets, and runs `tauri_build::try_build`.
- [src](./src/README.md) - Implements the Tauri desktop shell for OpenIDEA, wiring `ide_host::Host`, project trust IPC authorization, remote SSH gateway multiplexing, multi-window lifecycle management, native OS menus, and platform-specific window framing (macOS AppKit tabbing/fullscreen, Windows DWM titlebar, Linux GTK geometry persistence).

## Invariants
- [build.rs:main]: Resolves `OPENIDEA_WORKTREE_ROOT` from the canonicalized parent of `CARGO_MANIFEST_DIR`, and on `CARGO_CFG_TARGET_ENV == "msvc"` embeds `windows-app-manifest.xml` via `/MANIFEST:EMBED` and `/MANIFESTINPUT:` across all binary targets (including unit test executables) so `comctl32.dll` v6 (`TaskDialogIndirect`) is always available.

## Side Effects
- [build.rs:main]: Emits `cargo:rustc-env=OPENIDEA_WORKTREE_ROOT=...`, `cargo:rerun-if-changed=...`, and `cargo:rustc-link-arg=...` directives to Cargo, and generates Tauri build artifacts via `tauri_build::try_build`.

## Verification
- Run: `cargo test -p openidea`
