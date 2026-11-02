# ide-notebook

## Overview
Crate root for `ide-notebook`, providing round-trip-safe `.ipynb` notebook editing and ZeroMQ-based local Jupyter kernel execution.

## Subdirectories & Boundaries
- [src](./src/README.md) - Jupyter `.ipynb` notebook document model (`NotebookDocument`) with undo/redo history and atomic disk persistence, paired with workspace-scoped IPython kernel management (`KernelManager`).
