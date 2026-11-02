# ide-clipboard

## Overview
Crate root for `ide-clipboard`, providing native OS file clipboard integration for macOS and Windows.

## Subdirectories & Boundaries
- [src](./src/README.md) - OS file clipboard access (`CF_HDROP` on Windows, `NSPasteboard` file URLs on macOS) so Project View copy/paste interoperates with Finder and Explorer.
