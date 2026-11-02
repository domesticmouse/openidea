# term

## Overview
Securely stores pasted terminal clipboard images under `<data_dir>/terminal-images` with strict permissions and 24-hour TTL cleanup.

## Interface
- `pub(super) fn store(data_dir: &Path, bytes: &[u8], ext: &str) -> IdeResult<PathBuf>` (`[images.rs:store]`)

## Invariants
- Restricts extensions to `png`, `jpg`, `jpeg`, `gif`, `webp`, and `bmp` (`[images.rs:sanitize_ext]`).
- On Unix, requires an absolute `data_dir`, opens `terminal-images` with `OFlags::NOFOLLOW`, verifies current-EUID ownership and `0o700` permissions, writes `0o600` files via `.partial-` staging and `linkat`, and only unlinks owned regular `image-<32-hex>.<ext>` files older than 24 hours (`[images.rs:unix::open_directory]`, `[images.rs:unix::cleanup]`, `[images.rs:unix::store_with]`).

## Side Effects
- Creates `<data_dir>/terminal-images`, deletes expired image files, and writes image payloads to disk (`[images.rs:store]`, `[images.rs:unix::store_with]`).

## Verification
- Run: `cargo test -p ide-host`
