# src

## Overview
Parses JetBrains `.http`/`.rest` files, resolves environment and dynamic `{{variables}}`, executes HTTP requests via `reqwest`, and persists bounded exchange history.

## Interface
- `pub struct RunInput<'a>` and `pub async fn run(in_: RunInput<'_>) -> IdeResult<HttpExchange>` (`[lib.rs:RunInput]`, `[lib.rs:run]`)
- `pub fn environments(project_root: &Path, http_file: &Path) -> Vec<String>` (`[lib.rs:environments]`)
- `pub fn parse(text: &str) -> HttpFile` and `pub fn requests(text: &str) -> Vec<HttpRequestBlock>` (`[parse.rs:parse]`, `[parse.rs:requests]`, `[parse.rs:HttpFile]`, `[parse.rs:HttpRequestBlock]`)
- `pub struct Environments`, `pub struct Resolver`, `PUBLIC_FILE`, `PRIVATE_FILE` (`[env.rs:Environments]`, `[env.rs:Resolver]`, `[env.rs:PUBLIC_FILE]`, `[env.rs:PRIVATE_FILE]`)
- `pub fn prepare(...) -> IdeResult<PreparedRequest>`, `pub async fn execute(...) -> IdeResult<HttpResponse>`, `MAX_BODY` (`[exec.rs:prepare]`, `[exec.rs:execute]`, `[exec.rs:PreparedRequest]`, `[exec.rs:HttpResponse]`, `[exec.rs:MAX_BODY]`)
- `pub struct History`, `pub struct HistoryEntry`, `pub struct HttpExchange`, `MAX_ENTRIES` (`[history.rs:History]`, `[history.rs:HistoryEntry]`, `[history.rs:HttpExchange]`, `[history.rs:MAX_ENTRIES]`)
- `pub fn with_proxy(builder: ClientBuilder, proxy: &ProxySettings) -> IdeResult<ClientBuilder>`, `pub fn build(...) -> IdeResult<Client>`, `pub fn shared() -> &'static Client`, `pub async fn check_connection(proxy: &ProxySettings, url: &str) -> IdeResult<u16>` (`[client.rs:with_proxy]`, `[client.rs:build]`, `[client.rs:shared]`, `[client.rs:check_connection]`)

## Invariants
- `Resolver` resolves `{{name}}` placeholders with precedence: in-file `@name = value` (later definitions win) > private env > public env > private `$shared` > public `$shared` > dynamic variables (`$uuid`, `$timestamp`, `$isoTimestamp`, `$randomInt`, `$env.VAR`), with recursive expansion capped at depth 8 (`[env.rs:Environments::variables]`, `[env.rs:Resolver::lookup]`).
- `prepare` fails with `IdeError::invalid` if any `{{variable}}` remains unresolved (`[exec.rs:prepare]`).
- `execute` streams response chunks and truncates bodies exceeding `MAX_BODY` (16 MiB) with `body_truncated = true` (`[exec.rs:MAX_BODY]`, `[exec.rs:execute]`).
- `History` validates that entry IDs contain only ASCII alphanumeric characters or `-` and prunes stored entries beyond `MAX_ENTRIES` (100) (`[history.rs:MAX_ENTRIES]`, `[history.rs:History::file]`, `[history.rs:History::add]`).

## Side Effects
- Performs outbound HTTP/HTTPS requests using `reqwest` configured with `rustls-platform-verifier` and optional proxy settings (`[client.rs:build]`, `[exec.rs:execute]`).
- Walks ancestor directories up to `project_root` to read `http-client.env.json` and `http-client.private.env.json`, and reads `< path` request body files from disk (`[env.rs:Environments::load]`, `[exec.rs:prepare]`).
- Reads, writes, and deletes `<id>.json` exchange files and `index.json` inside the HTTP history directory (`[history.rs:History::add]`, `[history.rs:History::clear]`).

## Verification
- Run: `cargo test -p ide-http`
