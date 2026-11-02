# src

## Overview
Provides system web browser discovery and launching alongside a lazily-started, token-authenticated localhost HTTP server for previewing project files.

## Interface
- [lib.rs:Browser], [lib.rs:installed], [lib.rs:open], [lib.rs:WebServer]: Re-exported browser discovery/launch API and built-in static web server.
- [browsers.rs:Browser], [browsers.rs:installed], [browsers.rs:open]: Discovers installed desktop browsers (`chrome`, `firefox`, `safari`, `edge`, `brave`, `chromium`) and opens URLs in the system default or a specific browser.
- [server.rs:WebServer], [server.rs:WebServer::new], [server.rs:WebServer::url_for]: Lazily binds a localhost HTTP server and builds signed preview URLs (`http://127.0.0.1:<port>/<token>/<project>/<rel-path>`) for files inside registered project roots.

## Invariants
- [browsers.rs:open]: Rejects URLs that do not start with `http://`, `https://`, or `file://`, and rejects unknown browser identifiers.
- [server.rs:serve], [server.rs:host_allowed], [server.rs:resolve]: Only `GET` and `HEAD` requests are accepted; requests without a `Host` header matching `127.0.0.1:<port>` or `localhost:<port>` are rejected with `403 Forbidden` (DNS rebinding protection); the first URL segment must match the server's random UUID v4 token; and relative path segments containing `.`, `..`, `\`, or `/` are rejected before canonicalizing and verifying the target path stays within the canonical project root.

## Side Effects
- [server.rs:WebServer::port]: Binds a TCP listener on `127.0.0.1:63342` (or an OS-assigned ephemeral port on `127.0.0.1:0` if `63342` is busy) and spawns Tokio tasks serving project files from disk.
- [browsers.rs:open]: Spawns detached OS processes (`open`, `cmd /C start`, `xdg-open`, or browser binaries) with nulled stdio handles.

## Verification
- Run: `cargo test -p ide-web`
