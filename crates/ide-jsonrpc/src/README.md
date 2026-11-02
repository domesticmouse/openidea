# src

## Overview
Implements bidirectional JSON-RPC 2.0 framing (`Content-Length` and newline-delimited), async `Peer` multiplexing over child processes or custom streams, and Windows console code-page output decoding.

## Interface
- `pub mod framing` with `MAX_MESSAGE_LEN`, `read_message`, `read_message_or_noise`, `write_message`, `read_line_message`, `write_line_message` (`[lib.rs:framing::MAX_MESSAGE_LEN]`, `[lib.rs:framing::read_message]`, `[lib.rs:framing::read_message_or_noise]`, `[lib.rs:framing::write_message]`, `[lib.rs:framing::read_line_message]`, `[lib.rs:framing::write_line_message]`)
- `pub struct RpcError`, `pub mod codes`, `pub trait Handler`, `pub struct NoopHandler`, `pub enum Framing { ContentLength, Line }` (`[lib.rs:RpcError]`, `[lib.rs:codes]`, `[lib.rs:Handler]`, `[lib.rs:NoopHandler]`, `[lib.rs:Framing]`)
- `pub struct Peer` with `Peer::spawn`, `Peer::spawn_lines`, `Peer::spawn_framed`, `Peer::connect`, `Peer::connect_framed`, `Peer::request`, `Peer::request_cancellable`, `Peer::request_with_timeout`, `Peer::notify`, `Peer::is_alive`, `Peer::kill` (`[lib.rs:Peer]`)
- `pub mod output` with `decode`, `decode_with`, `encoding_for_code_page`, `trim_line_end`, `read_lines` (`[output.rs:decode]`, `[output.rs:decode_with]`, `[output.rs:encoding_for_code_page]`, `[output.rs:trim_line_end]`, `[output.rs:read_lines]`)

## Invariants
- `framing` rejects payloads exceeding `MAX_MESSAGE_LEN` (256 MiB) and header lines reaching `MAX_HEADER_LINE` (8 KiB) with `io::ErrorKind::InvalidData` (`[lib.rs:framing::MAX_MESSAGE_LEN]`, `[lib.rs:framing::MAX_HEADER_LINE]`).
- Incoming notifications are delivered sequentially in arrival order on a dedicated `"jsonrpc-notify"` OS thread, and incoming requests are dispatched onto `tokio::task::spawn_blocking` only after all preceding notifications have been enqueued (`[lib.rs:Peer::start]`).
- Dropping an unresolved `Peer::request` future or cancelling its `CancellationToken` removes its pending response slot and sends a `$/cancelRequest` notification (`[lib.rs:PendingGuard::drop]`, `[lib.rs:Peer::request_inner]`).
- `output::decode_with` decodes valid UTF-8 directly (stripping a UTF-8 BOM) and falls back to the detected console encoding only for non-UTF-8 byte sequences (`[output.rs:decode_with]`).

## Side Effects
- Spawns child processes with piped `stdin`/`stdout`/`stderr`, Tokio reader/writer/supervision tasks, and a `"jsonrpc-notify"` thread (`[lib.rs:Peer::spawn_framed]`, `[lib.rs:Peer::start]`).
- Terminates the child process when `Peer::kill` is called or when the last `Peer` clone is dropped via `KillOnDrop` (`[lib.rs:Peer::kill]`, `[lib.rs:KillOnDrop::drop]`).

## Verification
- Run: `cargo test -p ide-jsonrpc`
