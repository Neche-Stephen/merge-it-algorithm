
# Merge Contents

## Overview

The `mergeContents` function is designed to merge blocks of text from two files: a base file (`baseFileContents`) and a modified file (`modifiedFileContents`). The function respects the following rules: 

1. the block from modifiedFileContents should be used in the merged result for any differences between corresponding blocks in the two files.
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

- I assume that the blocks are separated by two or more newlines.
- If the number of blocks differs between the base and modified files, any extra blocks from the base or modified file will be preserved and joined.
- The keyword `"IGNORED"` is treated case-insensitively and is treated as a special keyword only in the modified file.

---


## Test Cases and Results

### Test 1: Basic Block Merge (No "IGNORED")
**Purpose**: Test basic merging when both the base and modified files contain corresponding blocks, but no `"IGNORED"` keyword is used. The modified blocks should overwrite the base blocks.

- **Input**:
  ```text
  Base: Line 1\nLine 2\n\nLine 3\nLine 4
  Modified: Line 1 Modified\nLine 2\n\nLine 3\nLine 4 Modified
  ```
- **Expected Output**: `Line 1 Modified\nLine 2\n\nLine 3\nLine 4 Modified`
- **Explanation**: All blocks are replaced by their modified versions since there is no `"IGNORED"` keyword in the modified file.
- **Result**: Passed

---

### Test 2: Merging with "IGNORED" Blocks
**Purpose**: Ensure that when a block in the modified file contains the `"IGNORED"` keyword, the corresponding block from the base file is used.

- **Input**:
  ```text
  Base: Line 1\nLine 2\n\nLine 3\nLine 4
  Modified: IGNORED\n\nLine 3\nLine 4 Modified
  ```
- **Expected Output**: `Line 1\nLine 2\n\nLine 3\nLine 4 Modified`
- **Explanation**: Since the first block of the modified file contains `"IGNORED"`, the base content (`Line 1\nLine 2`) is kept, while other blocks are replaced by their modified versions.
- **Result**: Passed

---

### Test 3: Merging with Different Number of Blocks
**Purpose**: Test the function’s behavior when the number of blocks in the base and modified files are different.

- **Input**:
  ```text
  Base: Block A\n\nBlock B\n\nBlock C
  Modified: IGNORED\n\nBlock B Modified
  ```
- **Expected Output**: `Block A\n\nBlock B Modified\n\nBlock C`
- **Explanation**: The base file has an extra block (`Block C`) at the end. The modified file's second block is used, but the extra block from the base file is retained at the end.
- **Result**: Passed

---

### Test 4: All Blocks as "IGNORED"
**Purpose**: Ensure that when all blocks in the modified file are marked as `"IGNORED"`, the base file's content is used entirely.

- **Input**:
  ```text
  Base: Block 1\n\nBlock 2
  Modified: IGNORED\n\nIGNORED
  ```
- **Expected Output**: `Block 1\n\nBlock 2`
- **Explanation**: Since both blocks in the modified file are `"IGNORED"`, the base content is not changed.
- **Result**: Passed

---

### Test 5: Merging with Empty Blocks in the Modified File
**Purpose**: Test the function's behavior when the modified file contains empty blocks. The function should retain the corresponding base blocks in this case.

- **Input**:
  ```text
  Base: Block 1\n\nBlock 2
  Modified: \n\n
  ```
- **Expected Output**: `Block 1\n\nBlock 2`
- **Explanation**: The modified file has empty blocks, so the function retains the corresponding base content.
- **Result**: Passed

---

### Test 6: Empty Base File
**Purpose**: Verify that when the base file is empty, the modified file's content is used entirely.

- **Input**:
  ```text
  Base: 
  Modified: Block 1 Modified\n\nBlock 2 Modified
  ```
- **Expected Output**: `Block 1 Modified\n\nBlock 2 Modified`
- **Explanation**: Since the base file is empty, the modified content is used completely.
- **Result**: Passed

---

### Test 7: Empty Modified File
**Purpose**: Ensure that when the modified file is empty, the base file's content is retained unchanged. (Note that, this test case is different from test 5 in that it is completely empty without any blocks)

- **Input**:
  ```text
  Base: Block 1\n\nBlock 2
  Modified: 
  ```
- **Expected Output**: `Block 1\n\nBlock 2`
- **Explanation**: With the modified file being empty, the function keeps the base content as it is.
- **Result**: Passed

---

### Test 8: "IGNORED" in the Base File
**Purpose**: Verify that the `"IGNORED"` keyword is treated only as a special case in the modified file, and it is not treated specially in the base file.

- **Input**:
  ```text
  Base: IGNORED\n\nBlock 2
  Modified: Block 1 Modified\n\nBlock 2 Modified
  ```
- **Expected Output**: `Block 1 Modified\n\nBlock 2 Modified`
- **Explanation**: The `"IGNORED"` keyword in the base file is not treated as special. The modified file's content is used completely.
- **Result**: Passed

---

### Conclusion

All tests have passed, showing that the function works well with various inputs, including edge cases like empty blocks, different numbers of block, and handling the `"IGNORED"` keyword in modified.

---





