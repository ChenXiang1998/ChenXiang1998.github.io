---
layout: default
title: "1. Two Sum"
date: 2026-02-17
difficulty: Easy
tags:
  - array
  - hash-table
  - two-pointers
  - base
---

## 📌Problem

Given an array of integers `nums` and an integer `target`, return *indices of the two numbers such that they add up to `target`*.

**Example 1:**

```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

**Example 2:**

```
Input: nums = [3,2,4], target = 6
Output: [1,2]
```

**Example 3:**

```
Input: nums = [3,3], target = 6
Output: [0,1]
```

## 0) Quick identity card

- **Problem type:** Array + Hash Table (dictionary / map)
- **Core idea:** “Have I seen the complement before?”
- **Difficulty:** Easy (but *fundamental*)
- **Why it’s famous:** It’s the textbook example of **trading space for time**
- **Interview frequency:** ⭐⭐⭐⭐⭐ (extremely common)
- **Pattern Recognition:**
  - Classic **hash-table lookup**
  - First exposure to **O(n²) → O(n)** optimization
  - “Complement trick” used in many other problems

------

## 1) Problem (plain English)

You are given:

- an integer array `nums`
- an integer `target`

You must find **two different indices** `i` and `j` such that:

- `nums[i] + nums[j] == target`
- return **[i, j]** (order usually doesn’t matter)

Important details:

- You **cannot** use the **same element twice**.
- The problem usually guarantees exactly **one solution** (on LeetCode).
- Indices matter, not the values.

------

## 2) Example walkthrough (to anchor memory)

Example:

- `nums = [2, 7, 11, 15]`
- `target = 9`

We need two numbers that sum to 9.

- 2 needs a partner `9 - 2 = 7`
- 7 is in the array
- Indices: 2 is at 0, 7 is at 1
  Answer: `[0, 1]`

That “partner” number has a name:

> **complement = target - current_value**

This word “complement” is the key that unlocks the hash table solution.

------

## 3) The “beginner mindset”: How should you think?

### 3.1 First thought (brute force)

A beginner will naturally try:

- “Try all pairs”
- For each `i`, try every `j > i`
- Check if `nums[i] + nums[j] == target`

This always works, but it can be slow.

**Pseudo:**

```
for i in range(n):
  for j in range(i+1, n):
    if nums[i] + nums[j] == target:
      return [i, j]
```

**Time complexity:** $O(n²)$
If `n = 10,000`, then pairs ≈ 50 million. That’s too big.

So we ask:

> Can we avoid checking all pairs?

------

### 3.2 The “upgrade” idea

Instead of searching pairs explicitly, we can do:

When I look at a number `x`, I know exactly what I need:

- complement = `target - x`

So the whole problem becomes:

> For each element `x`, **check quickly** whether its complement has appeared earlier.

The phrase **“check quickly”** suggests a data structure:

- List search is O(n)
- Hash table (dictionary / map) lookup is **O(1) average**

So:

- store numbers we’ve seen in a dictionary
- map: `value -> index`

------

## 4) Why the hash map solution works (conceptual proof)

Let’s say we are scanning the array from left to right.

At index `i`, we see value `x = nums[i]`.

We want to know if there exists some earlier index `j` such that:

```
nums[j] + nums[i] = target
```

Rearrange:

```
nums[j] = target - nums[i]
```

So the earlier value we need is:

```
complement = target - x
```

If we have a dictionary of all earlier numbers, we can check:

- Is `complement` already in the dictionary?
  - If yes, we found `j` (the stored index), and current `i` gives the pair.
  - If no, store `x` with its index and continue.

### Why “earlier” matters

This guarantees we never use the same element twice.

Because when we’re at index `i`, the dictionary only contains indices `< i`.
So `j` is always different from `i`.

------

## 5) The most important mental picture

### “Asking the past”

When you’re at position `i`, you ask:

> “Have I already seen the number that would complete my target?”

That’s the entire algorithm.

------

## 6) Step-by-step trace (so you can *see* it happen)

Example:

- `nums = [2, 7, 11, 15]`
- `target = 9`

Initialize:

- `seen = {}` (empty dictionary)

#### i = 0

- x = 2
- complement = 9 - 2 = 7
- Is 7 in `seen`? ❌ No
- Store: `seen[2] = 0`
- Now `seen = {2: 0}`

#### i = 1

- x = 7
- complement = 9 - 7 = 2
- Is 2 in `seen`? ✅ Yes
- Found pair:
  - j = `seen[2] = 0`
  - i = 1
- Return `[0, 1]`

Done.

------

## 7) Edge cases beginners often mess up

### 7.1 Duplicate values (critical)

Example:

- `nums = [3, 3]`
- `target = 6`

Correct answer is `[0, 1]`.

Trace:

- i=0: x=3, complement=3 not in seen, store (3->0)
- i=1: x=3, complement=3 IS in seen, return [0,1]

Works perfectly.

### 7.2 Negative numbers

Example:

- `nums = [-1, -2, -3, -4, -5]`
- `target = -8`

Pick -3 + -5 = -8
Hash map handles negatives naturally.

### 7.3 Complement equals current value

This is basically the duplicate case:

- If complement == x, you need **two occurrences** of x
- Using “check first, store after” ensures you only match with earlier indices.

------

## 8) Common wrong approaches and why they fail

### Wrong idea A: “Store first, then check”

If you do:

1. store `seen[x] = i`
2. check complement

You might accidentally match the same element when `target = 2*x`.

Example:

- `nums = [3]`, target=6
- store 3 at index 0
- check complement 3 → found itself
  That would be illegal.

✅ Fix: **check first, then store**.

------

### Wrong idea B: Use sorting (without care)

People think:

- sort array
- two pointers

But sorting changes indices, and you must return original indices.
You can still do it, but it becomes more complex (need to keep original indices).
Hash map is the intended clean solution.

------

## 9) Final solution (Python) — recommended interview version

### 9.1 Clean code (the standard)

```python
from typing import List

class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # seen maps: number -> index where we first saw it
        seen = {}

        for i, x in enumerate(nums):
            complement = target - x

            # If complement already seen, we found the pair
            if complement in seen:
                return [seen[complement], i]

            # Otherwise remember x for future elements
            seen[x] = i

        # LeetCode guarantees exactly one solution, so we usually never reach here.
        return []
```

------

## 10) Explain every line (beginner explanation)

- `seen = {}`
  Create an empty dictionary that will remember numbers we have encountered.
- `for i, x in enumerate(nums):`
  Go through the list, where:
  - `i` is the index
  - `x` is the value at that index
- `complement = target - x`
  Compute the number needed to pair with `x` to reach `target`.
- `if complement in seen:`
  Check if we have already encountered the needed partner number.
- `return [seen[complement], i]`
  If yes, return:
  - the index where we saw the complement earlier
  - the current index `i`
- `seen[x] = i`
  If complement doesn’t exist yet, store current number and its index,
  so future elements can pair with it.

------

## 11) Complexity (must memorize)

- **Time complexity:** **O(n)**
  Because you loop once, and dictionary operations are O(1) average.
- **Space complexity:** **O(n)**
  In the worst case, you store almost every element in the dictionary.

### The key trade-off

> We use extra memory (dictionary) to avoid the expensive nested loop.

------

## 12) Interview “talk track” (what to say out loud)

If the interviewer asks “Explain your reasoning”:

1. “Brute force checks all pairs, O(n²).”
2. “We can do better by noticing: for each value `x`, we need `target - x`.”
3. “If I can check whether the complement exists in O(1), total becomes O(n).”
4. “Use a hash map storing seen numbers to their indices.”
5. “For each element: check complement first, then store current.”

That is basically the perfect explanation.

------

## 13) Variations you should recognize (Two Sum family)

Once you understand this, you can recognize many cousin problems:

### Two Sum II — sorted array

- uses two pointers, O(n) time, O(1) space

### 3Sum / 4Sum

- often sort + two pointers + careful duplicate handling

### Subarray Sum Equals K

- same spirit: prefix sums + hash map

### Pair with given difference

- also uses hash set/map

The pattern is:

> Transform a “pair condition” into a “lookup condition”.

------

## 14) Small “memory hooks” (so you won’t forget)

- **Complement** is the password: `target - x`
- **Ask the past**: “Have I already seen my complement?”
- **Check before store**: avoids using same element twice
- **Dictionary = instant search**: O(1) average
- **Brute force pairs**: O(n²) and painful

---

