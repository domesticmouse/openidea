# theme

## Overview
WCAG 2.x color contrast calculation (parseHex, relativeLuminance, contrastRatio) and CSS custom property token extraction (readTokens, isColorValue) for theme verification.

## Interface
- [contrast.ts:Rgb] [contrast.ts:parseHex] [contrast.ts:relativeLuminance] [contrast.ts:contrastRatio] [contrast.ts:readTokens] [contrast.ts:isColorValue] Hex color parser, WCAG relative luminance and contrast ratio calculators, and CSS custom property reader.

## Invariants
- [contrast.ts:contrastRatio] Throws an Error if either input is not a 3-digit or 6-digit # hex color (parseHex returns null), and returns a WCAG 2.x luminance ratio in [1, 21].
- [contrast.ts:readTokens] Strips block comments and collects only --* custom properties from CSS rules whose comma-separated selector list includes the exact selector.

## Side Effects
- None

## Verification
- Run: `pnpm test`
