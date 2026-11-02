<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="src-tauri/icons/source/openidea-wordmark-dark.svg">
    <img src="src-tauri/icons/source/openidea-wordmark.svg" alt="OpenIDEA" height="56">
  </picture>
</p>

<p align="center">
  The IDE you know, rebuilt in Rust.
</p>

<p align="center">
  <a href="https://github.com/l0ng-ai/openidea/releases/latest"><b>Download</b></a>
  &nbsp;·&nbsp;
  <a href="#benchmark">Benchmark</a>
  &nbsp;·&nbsp;
  <a href="https://discord.gg/2Zy4yn7gP">Discord</a>
</p>

https://github.com/user-attachments/assets/234dd54c-1255-4b3a-b35c-533d78d737c0

OpenIDEA brings the IntelliJ IDEA way of working — its keymap, its tool windows,
its search-everything habits — to a native app with a Rust core.

Editor, code insight, debugger, Git, terminal, database and HTTP clients,
for 60+ languages. Builds for macOS, Windows and Linux.

## Benchmark

Opening [freqtrade](https://github.com/freqtrade/freqtrade) (Python, ~770 files)
with warm caches, median of 5 runs:

|                             | OpenIDEA          | IntelliJ IDEA 2026.2.3 |
| --------------------------- | ----------------- | ---------------------- |
| Window with the project     | **0.9 s**         | 3.1 s                  |
| Ready (CPU settled)         | **1.1 s**         | 13.2 s                 |
| Memory (USS, all processes) | **169 MB**        | 2011 MB                |
| CPU time until ready        | **2.4 s**         | 47.9 s                 |
| Idle CPU                    | **4 %** of a core | 7 % of a core          |
| Quit                        | **0.3 s**         | 5.9 s                  |

A release build of OpenIDEA from `main` and IDEA with default settings and plugins, on
Windows 11, 32-core AMD, 62 GB RAM.
IDEA analyses Python more deeply, so "ready" doesn't mean the same work on
both sides. Both are measured from the outside, the same way: see
[`perf/compare`](perf/compare) to run it on your machine and projects.

## License

[Apache 2.0](LICENSE). Bundled third-party assets are listed in [`THIRD_PARTY_LICENSES.md`](THIRD_PARTY_LICENSES.md).

<sub>Not affiliated with JetBrains. IntelliJ IDEA is a trademark of JetBrains s.r.o.</sub>

---

## Overview
OpenIDEA repository root: a native cross-platform IDE with a Rust backend (crates/*, src-tauri) and React 19 + TypeScript + CodeMirror 6 frontend (src/*), bundled plugins (plugins/*), build/release scripts (scripts/*), and performance benchmarks (perf/*).

## Interface
- [vite.config.ts] Vite and Vitest configuration defining the @ -> ./src alias, routing @tauri-apps/api/core through ./src/platform/tauriCore.ts for transparent remote development, and configuring jsdom unit test discovery.
- [playwright.config.ts] Playwright end-to-end test runner configuration serving the frontend in plain-browser in-memory mode on port 1450 with macOS keymap user-agent defaults.
- [knip.ts] Dead-code and unused-export analyzer configuration across src/, scripts/, perf/, and e2e/.
- [crates](./crates/README.md) - Rust workspace crates implementing OpenIDEA's platform-independent IDE core, virtual file system, Rope text buffer, tree-sitter syntax & trigram index, LSP/DAP/ACP clients, Git/database/HTTP/Docker/SSH/terminal services, WASM plugin host, and central IdeHost coordinator.
- [perf](./perf/README.md) - UI and Rust backend performance harness (`pnpm perf`, `pnpm perf:rust`), interaction scenario suite (`scenarios.ts`), and performance budget manifest (`budgets.json`).
- [plugins](./plugins/README.md) - OpenIDEA plugins subsystem containing the WebAssembly component build script (`build.sh`), sample WASI preview 2 plugins (`hello`), and app-owned JDT LS / Maven bridge bundles (`java-usages`, `kotlin-bridge`).
- [public](./public/README.md) - Static web assets served at the root URL, including the synchronous pre-CSS theme restoration script (`boot.js`) and startup splash stylesheet (`splash.css`).
- [scripts](./scripts/README.md) - Worktree-isolated development session manager (`dev-session.ts`), Tauri/Vite dev runner (`tauri.ts`), MCP bridge status inspector (`dev-status.ts`), and theme tooling (`scripts/theme`).
- [src](./src/README.md) - React 19 + TypeScript + CodeMirror 6 frontend root containing the application entry point (main.tsx), auto-generated Tauri IPC bindings (bindings.ts), and the editor, features, platform, ui, and workbench layers.
- [src-tauri](./src-tauri/README.md) - Root package for the `openidea` Tauri desktop application, containing the Cargo manifest, Tauri configuration, build-time environment and Windows manifest injection (`build.rs`), and the `src/` desktop shell implementation.

## Invariants
- [vite.config.ts] Aliases /^@tauri-apps\/api\/core$/ to src/platform/tauriCore.ts so every IPC invoke() call transparently routes to either the local Tauri host or a connected remote openidea-server over SSH.
- [playwright.config.ts] Forces a Macintosh User-Agent across all host OSes in e2e tests so specs deterministically exercise the macOS (⌘) keymap.

## Side Effects
- [playwright.config.ts] Spawns pnpm exec vite --port 1450 --strictPort when running pnpm e2e.

## Verification
- Run: `pnpm test`
