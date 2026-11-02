# ide-http

## Overview
HTTP Client `.http`/`.rest` parser, environment variable resolver, request executor, and exchange history crate (`ide-http`).

## Subdirectories & Boundaries
- [src](./src/README.md) - Parses JetBrains `.http`/`.rest` files, resolves environment and dynamic `{{variables}}`, executes HTTP requests via `reqwest`, and persists bounded exchange history.
