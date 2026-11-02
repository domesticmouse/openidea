# src

## Overview
Jupyter `.ipynb` notebook document model (`NotebookDocument`) with undo/redo history and atomic disk persistence, paired with workspace-scoped IPython kernel management (`KernelManager`).

## Interface
- [document.rs:NotebookSnapshot], [document.rs:NotebookCell], [document.rs:NotebookEdit], [document.rs:NotebookDocument]: `.ipynb` notebook document model, cell/output structures, and undo/redo edit operations.
- [kernel.rs:KernelOutput], [kernel.rs:KernelState], [kernel.rs:ExecutionResult], [kernel.rs:KernelManager]: IPython kernel lifecycle and execution manager.
- [document](./document/README.md) - Format-preserving JSON AST (`Node`, `Member`, `Layout`) for Jupyter `.ipynb` files that keeps original byte ranges of untouched values so edits only rewrite modified subtrees.
- [kernel](./kernel/README.md) - Jupyter kernel child-process lifecycle (`Process`) and HMAC-SHA256 authenticated ZeroMQ Wire Protocol v5.3 session (`Running`) over Shell, IOPub, Stdin, and Control channels.

## Invariants
- `NotebookDocument::from_str` requires `nbformat == 4` and rejects files exceeding `MAX_FILE_BYTES` (50 MiB), cell counts exceeding `MAX_CELLS` (10,000), or outputs exceeding `MAX_CELL_OUTPUTS` (1,000) (`[document.rs:MAX_FILE_BYTES]`, `[document.rs:NotebookDocument::from_str]`).
- A notebook always contains at least one cell; deleting the last remaining cell replaces it with a fresh empty cell (`[document.rs:NotebookDocument::apply]`).
- Every cell holds a unique, non-empty ID sanitized to `[A-Za-z0-9_-]{1,64}` (`[document.rs:valid_id]`, `[document.rs:unique_ids]`).
- `NotebookDocument::save` refuses to overwrite external disk changes when the on-disk mtime differs from `disk_mtime` (`[document.rs:NotebookDocument::save]`).

## Side Effects
- Reads and atomically writes `.ipynb` files via `ide_vfs::write_atomic` (`[document.rs:NotebookDocument::open]`, `[document.rs:NotebookDocument::save]`).
- Spawns, interrupts, restarts, and shuts down IPython kernel processes and emits `IdeEvent::NotebookKernelState` and `IdeEvent::NotebookCellOutput` events (`[kernel.rs:KernelManager]`).

## Verification
- Run: `cargo test -p ide-notebook`
