# ide-jsonrpc

## Overview
Bidirectional JSON-RPC 2.0 transport, framing, process supervision, and console output decoding crate (`ide-jsonrpc`) used by LSP, DAP, and ACP clients.

## Subdirectories & Boundaries
- [src](./src/README.md) - Implements bidirectional JSON-RPC 2.0 framing (`Content-Length` and newline-delimited), async `Peer` multiplexing over child processes or custom streams, and Windows console code-page output decoding.
