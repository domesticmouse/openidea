# ide-lsp

## Overview
Crate root for `ide-lsp`, providing Language Server Protocol client management, capability negotiation, multi-server request merging, and language-specific adapters (JDTLS, TypeScript/Angular).

## Subdirectories & Boundaries
- [src](./src/README.md) - LSP client lifecycle and multi-server orchestration (`LspManager`), JSON-RPC stdio/TCP transport, UTF-16 position and workspace-edit conversion, JDTLS and TypeScript/Angular bridges, virtual decompiled documents, and batch file inspections.
