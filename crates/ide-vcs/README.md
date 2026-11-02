# ide-vcs

## Overview
Crate root for `ide-vcs`, providing Git version control queries, CLI-backed repository mutations, changelists, and shelving for OpenIDEA.

## Subdirectories & Boundaries
- [src](./src/README.md) - Implements Git version control operations for single and multi-root workspaces, combining pure-Rust `gix` read queries with subprocess `git` CLI execution for index mutations, network operations, changelists, and shelved patches.
