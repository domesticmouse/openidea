# comparison

## Overview
Two-level line, word, and character diff engine (ported from IntelliJ ComparisonManagerImpl) supporting DEFAULT, TRIM_WHITESPACES, and IGNORE_WHITESPACES policies, block splitting, and chunk optimization.

## Interface
- [index.ts:LineFragment] [index.ts:CompareOptions] [index.ts:compareTexts] [index.ts:compareLineFragments] [index.ts:compareLinesInner] [index.ts:processBlocks] Top-level text comparison pipeline computing line fragments and optional inner word ranges.
- [byLine.ts:compareLines] [byWord.ts:InlineChunk] [byWord.ts:getInlineChunks] [byWord.ts:compareWords] [byWord.ts:LineBlock] [byWord.ts:compareAndSplit] [byWord.ts:matchWhitespaces] [byChar.ts:comparePunctuation] Line-level, word-level, and punctuation-level diff algorithms.
- [chunkOptimizer.ts:WordChunk] [chunkOptimizer.ts:optimizeWordChunks] [chunkOptimizer.ts:UNIMPORTANT_LINE_CHAR_COUNT] [chunkOptimizer.ts:LineInfo] [chunkOptimizer.ts:optimizeLineChunks] Post-passes merging or trimming short/unimportant unchanged gaps between changed chunks.
- [iterables.ts:DiffIterable] [iterables.ts:create] [iterables.ts:unchanged] [iterables.ts:invert] [iterables.ts:createUnchanged] [iterables.ts:diffSequences] [iterables.ts:subIterable] [iterables.ts:ChangeBuilder] [iterables.ts:ExpandChangeBuilder] [iterables.ts:expandEqual] [iterables.ts:expandForward] [iterables.ts:expandBackward] [lcs.ts:Range] [lcs.ts:range] [lcs.ts:isEmptyRange] [lcs.ts:DiffTooBigError] [lcs.ts:diffInts] [lcs.ts:diffKeys] Myers LCS implementation and DiffIterable combinators.
- [text.ts:ComparisonPolicy] [text.ts:isSpace] [text.ts:isPunctuation] [text.ts:isWhiteSpaceCodePoint] [text.ts:isAlpha] [text.ts:isContinuousScript] [text.ts:charCount] [text.ts:trimStart] [text.ts:trimEnd] [text.ts:trimRange] [text.ts:expandWhitespaces] [text.ts:expandWhitespacesForward] [text.ts:expandWhitespacesBackward] [text.ts:stripSpaces] [text.ts:trimSpaces] [text.ts:lineKey] [text.ts:isEqualTexts] [text.ts:isEqualsRange] [text.ts:isEqualsIgnoreWhitespacesRange] Policy-aware character classification, hashing, and range comparison utilities.

## Invariants
- [index.ts:innerFragments] Stops computing word-level inner diffs after MAX_BAD_LINES (3) blocks throw DiffTooBigError, falling back to line-only fragments for remaining large blocks.
- [lcs.ts:diffInts] Throws DiffTooBigError when sequence complexity exceeds the Myers threshold rather than blocking the UI thread.

## Side Effects
- None

## Verification
- Run: `pnpm test`
