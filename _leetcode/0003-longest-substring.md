---
layout: default
title: 3. Longest Substring Without Repeating Characters
date: 2026-02-17
difficulty: Medium
tags:
  - string
  - sliding-window
  - two-pointers
  - hash-map
  - base
---

##  📌Problem

Given a string `s`, find the length of the **longest** **substring** without duplicate characters.

**Example 1:**

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3. Note that "bca" and "cab" are also correct answers.
```

**Example 2:**

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

------

## 0) Quick Identity Card

- **Problem type:** String + Sliding Window + Hash Map
- **Core idea:** Maintain a window of unique characters
- **Difficulty:** Medium
- **Interview frequency:** ⭐⭐⭐⭐⭐
- **Pattern Recognition:**
  - Classic **Sliding Window**
  - First exposure to **Two Pointers on strings**
  - Real understanding of **dynamic window resizing**
  - Hash map used to track last seen index

------

# 1) Problem (Plain English)

Given a string `s`, return the **length** of the longest substring without repeating characters.

Important:

- We care about **substring** (continuous).
- Not subsequence.
- Return the **length**, not the substring itself.

------

## Substring vs Subsequence (VERY IMPORTANT)

Substring:

- Continuous characters.
- `"abc"` in `"abcabcbb"` is valid.

Subsequence:

- Not necessarily continuous.
- `"pwke"` from `"pwwkew"` is subsequence, not substring.
- That is NOT allowed.

------

# 2) First Intuition (Beginner Thinking)

You might think:

> "Try every possible substring and check if it has duplicates."

That works.

But how many substrings exist?

If string length = $n$

Number of substrings ≈ $n^2$

If n = 50,000 → $n^2$ = 2.5 billion → too slow.

So brute force is impossible.

We need something $O(n)$.

------

# 3) The Key Insight

We don't need to restart every time.

We can:

- Maintain a window of characters
- Expand the window to the right
- If duplicate appears → shrink from left

This is called:

# 🔥 Sliding Window Technique

------

# 4) The Mental Model

Imagine two pointers:

```
l (left pointer)
r (right pointer)
```

They form a window:

```
s[l ... r]
```

This window always contains **unique characters only**.

We grow `r` step by step.

If a duplicate appears:

- Move `l` forward
- Remove the duplicate from the window

------

# 5) The Algorithm in Words

1. Start with:
   - `l = 0`
   - `best = 0`
   - dictionary `last = {}` (stores last index of each character)
2. For each index `r`:
   - Let `ch = s[r]`
   - If `ch` appeared before AND its last index ≥ l:
     → Move `l` to `last[ch] + 1`
   - Update `last[ch] = r`
   - Update `best = max(best, r - l + 1)`

------

# 6) Why We Need `last[ch] >= l`

This is CRITICAL.

Consider:

```
s = "abba"
```

Walk through it:

### r = 0 → 'a'

window = "a"
best = 1

### r = 1 → 'b'

window = "ab"
best = 2

### r = 2 → 'b'

duplicate found!
last['b'] = 1

So move l to 1 + 1 = 2

window = "b"
best still 2

### r = 3 → 'a'

last['a'] = 0

BUT 0 < l (l = 2)

That means this 'a' is outside the current window.

So we DO NOT move l.

This is why we check:

```
if last[ch] >= l
```

We only react if duplicate is inside current window.

------

# 7) Full Code (Correct Version)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        last = {}          # char -> last index seen
        l = 0              # left pointer of window
        best = 0           # maximum length found

        for r, ch in enumerate(s):
            # If character seen before and inside current window
            if ch in last and last[ch] >= l:
                l = last[ch] + 1   # jump left pointer

            last[ch] = r
            best = max(best, r - l + 1)

        return best
```

------

# 8) Line-by-Line Explanation

### `last = {}`

Stores the last position of each character.

Example:

```
{'a': 0, 'b': 2}
```

------

### `l = 0`

Left boundary of sliding window.

------

### `for r, ch in enumerate(s):`

Move right pointer step by step.

------

### `if ch in last and last[ch] >= l:`

Two conditions:

1. Character seen before
2. That occurrence is inside current window

------

### `l = last[ch] + 1`

Jump left pointer directly after duplicate.

We do NOT move one by one.
We "teleport" it.

This is optimization.

------

### `last[ch] = r`

Update latest position.

------

### `best = max(best, r - l + 1)`

Current window length = `r - l + 1`

------

# 9) Step-by-Step Example

## Example 1: "abcabcbb"

```
l=0
best=0
```

r=0 → a
window="a"
best=1

r=1 → b
window="ab"
best=2

r=2 → c
window="abc"
best=3

r=3 → a
duplicate!
move l = 0+1 =1
window="bca"
best=3

r=4 → b
duplicate!
move l=1+1=2
window="cab"
best=3

r=5 → c
duplicate!
move l=2+1=3
window="abc"
best=3

r=6 → b
duplicate!
move l=4+1=5
window="cb"
best=3

r=7 → b
duplicate!
move l=6+1=7
window="b"
best=3

Final answer = 3

------

## Example 2: "bbbbb"

Window never grows beyond 1.

Answer = 1

------

## Example 3: "pwwkew"

r=0 → p → best=1
r=1 → w → best=2
r=2 → w duplicate → l=2 → best=2
r=3 → k → best=2
r=4 → e → best=3
r=5 → w duplicate → adjust → best=3

Answer = 3

------

# 10) Why This is O(n)

Each character is:

- Added once (r moves forward)
- l only moves forward

Neither pointer moves backward.

Total operations ≈ 2n

Time complexity: **O(n)**
Space complexity: **O(min(n, charset))**

------

# 11) Common Beginner Mistakes

### ❌ Moving l one step at a time

Too slow.

### ❌ Forgetting `last[ch] >= l`

Causes wrong shrinking.

### ❌ Using set instead of map

You can solve with set, but map is cleaner and faster.

------

# 12) Alternative Version (Set Based — Slower but Educational)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        seen = set()
        l = 0
        best = 0

        for r in range(len(s)):
            while s[r] in seen:
                seen.remove(s[l])
                l += 1

            seen.add(s[r])
            best = max(best, r - l + 1)

        return best
```

This shrinks step-by-step.

Still O(n), but less elegant.

------

# 13) Pattern Recognition

This problem teaches:

### Sliding Window Template

```
for r in range(n):
    add s[r]
    while window invalid:
        remove s[l]
        l += 1
    update answer
```

You will see this in:

- Minimum Window Substring
- Longest Repeating Character Replacement
- Permutation in String
- Subarray problems

------

# 14) Interview Talk Track

Say this:

> "Brute force is O(n²).
> Instead, I maintain a sliding window with unique characters.
> I use a hash map to track last seen index.
> If a duplicate appears inside the window, I move left pointer to avoid duplicates.
> This guarantees O(n) time."

Perfect explanation.

------

# 15) Memory Hooks

- Two pointers: l and r
- Window must always stay VALID (no duplicates)
- Duplicate → jump left pointer
- Map stores last index
- Each pointer only moves forward

------

# 16) Core Template to Memorize

```python
if ch in last and last[ch] >= l:
    l = last[ch] + 1
```

That line is the heart.

------

