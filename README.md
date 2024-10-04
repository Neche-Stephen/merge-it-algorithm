
# Merge Contents

## Overview

The `mergeContents` function is designed to merge blocks of text from two files: a base file (`baseFileContents`) and a modified file (`modifiedFileContents`). The function respects the following rules: 

1. the block from modifiedFileContents should be used in the merged result for any differences between corresponding blocks.
2. if a block in the modified file contains the keyword `IGNORED`, the corresponding block from the base file is used in the final output.
3. The merge is performed at the block or semantic level, A block is a group of consecutive lines of text that are separated by a block separator (empty lines, string sequence, opening and closing sequence)

### Approach

1. **Splitting into Blocks**:
   I assumed the blocks of text are separated by two or more newlines (with possible spaces in between). The content of both files is split into blocks using this separator. However, my logic will also work for another kind of separator such as a string sequence like ====

2. **Iterating and Merging**:
   I iterate over the blocks from both the base and modified files, using the following rules:
   - If the modified block contains the keyword `IGNORED`, the corresponding base block is used.
   - If the modified block is not empty and doesn't contain `IGNORED`, the modified block is used.
   - If the modified block is empty, the base block is used

3. **Rejoining the Blocks**:
   After processing, the blocks are joined together with two (or more) newlines separating them, forming the final merged content.

### Tools/Libraries Used

- **JavaScript/Node.js**: The function and tests are written in JavaScript.
- **Jest**: Used as the testing framework.

### Runtime Performance

The function operates in **O(n)** time complexity, where **n** is the maximum number of blocks between the base and modified files. This is because the function processes each block exactly once and performs constant-time operations (such as comparisons and joins) on each block.

### Assumptions

- I assume that the blocks are separated by two or more newlines (with optional spaces between them).
- If the number of blocks differs between the base and modified files, any extra blocks from the base or modified file will be preserved and joined.
- The keyword `"IGNORED"` is treated case-insensitively and should be trimmed before comparison.

---

## Test Cases and Results

### Test 1: Basic Block Merge
- **Input**:
  ```text
  Base: Line 1\nLine 2\n\nLine 3\nLine 4
  Modified: Line 1 Modified\nLine 2\n\nLine 3\nLine 4 Modified
  ```
- **Expected Output**: `Line 1 Modified\nLine 2\n\nLine 3\nLine 4 Modified`
- **Result**: Passed

### Test 2: Handling "IGNORED" Blocks
- **Input**:
  ```text
  Base: [\nLine 1\n\nLine 2\n]\n[\nLine 3\n\nLine 4\n]
  Modified: IGNORED\n\n[\nLine 3 Modified\n\nLine 4\n]
  ```
- **Expected Output**: `[\nLine 1\n\nLine 2]\n\n[\nLine 3 Modified\n\nLine 4]`
- **Result**: Passed

### Test 3: Different Number of Blocks
- **Input**:
  ```text
  Base: Block A\n\nBlock B\n\nBlock C
  Modified: IGNORED\n\nBlock B Modified
  ```
- **Expected Output**: `Block A\n\nBlock B Modified\n\nBlock C`
- **Result**: Passed

### Test 4: All Blocks as "IGNORED"
- **Input**:
  ```text
  Base: Block 1\n\nBlock 2
  Modified: IGNORED\n\nIGNORED
  ```
- **Expected Output**: `Block 1\n\nBlock 2`
- **Result**: Passed

### Test 5: Empty Blocks
- **Input**:
  ```text
  Base: Block 1\n\nBlock 2
  Modified: \n\n
  ```
- **Expected Output**: `Block 1\n\nBlock 2`
- **Result**: Passed

### Test 6: Empty Base File
- **Input**:
  ```text
  Base: 
  Modified: Block 1 Modified\n\nBlock 2 Modified
  ```
- **Expected Output**: `Block 1 Modified\n\nBlock 2 Modified`
- **Result**: Passed

### Test 7: Empty Modified File
- **Input**:
  ```text
  Base: Block 1\n\nBlock 2
  Modified: 
  ```
- **Expected Output**: `Block 1\n\nBlock 2`
- **Result**: Passed

### Test 8: Single Block with "IGNORED" in Base
- **Input**:
  ```text
  Base: IGNORED\n\nBlock 2
  Modified: Block 1 Modified\n\nBlock 2 Modified
  ```
- **Expected Output**: `Block 1 Modified\n\nBlock 2 Modified`
- **Result**: Passed

### Conclusion

All tests have passed, demonstrating that the function performs correctly with various inputs, including edge cases such as empty blocks, different block counts, and handling the `"IGNORED"` keyword.

---
