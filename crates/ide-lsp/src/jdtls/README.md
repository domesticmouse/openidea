# jdtls

## Overview
Eclipse JDTLS code-style formatter configuration, bundling the IntelliJ-compatible Eclipse formatter XML profile and wiring profile settings into JDTLS launch configurations.

## Interface
- [formatter.rs:configure_formatter]: Mutates a JDTLS `LanguageServerConfig` with formatter profile settings based on `CodeStyleSettings`.

## Invariants
- `configure_formatter` is a no-op whenever `cfg.name != "jdtls"` (`[formatter.rs:configure_formatter]`).
- Explicit user overrides for `java.format.settings.url` or `java.format.settings.profile` in `LanguageServerSettings` take precedence over generated formatter profile settings (`[formatter.rs:formatter_overrides]`).
- Missing or unreadable custom formatter XML files return an `Err` without mutating `cfg.launch_error`, allowing JDTLS to start with default formatting (`[formatter.rs:configure_formatter]`).

## Side Effects
- When the `"intellij"` profile is active, creates `<data_dir>/lsp/jdtls/` and atomically writes the embedded `intellij.xml` profile via `ide_vfs::write_atomic` (`[formatter.rs:configure_formatter]`).
- When the `"custom"` profile is active, opens `<project_root>/<formatter_path>` on disk to validate accessibility (`[formatter.rs:configure_formatter]`).

## Verification
- Run: `cargo test -p ide-lsp`
