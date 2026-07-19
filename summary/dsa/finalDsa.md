# Introduction
1. [Strategy](#strategy)
2. [WH Framework](#wh-framework-to-use-before-coding)
3. [Heap](#hashmap)
4. [2-Pointers](#2-pointers)
5. [Sliding Window](#sliding-window)
6. [Prefix Sum](#prefix-sum)
7. [Kadane's Algorithms](#kadanes-algorithms)
8. [Binary Search](#binary-search)
9. [Priority Queue](#priority-queue)
10. [Greedy Algorithm](#greedy-algorithm)

# Strategy

## 1. Every Array/String Problem = 5 Questions

Before writing code, ask:

1. **What am I scanning?**
   Array, string, sorted array, or continuous subarray?

2. **What information must I remember?**
   Seen values, frequency, index, sum, maximum, minimum?

3. **What makes the current state valid or invalid?**

4. **What should move/change when invalid?**
   `left`, `right`, map count, heap, or boundary?

5. **When is it safe to update the answer?**

Think:

> **Scan → Maintain state → Check condition → Fix state → Update answer**

---

# 2. Problem Words → Default Pattern

| Question contains                             | First pattern to consider                   |
| --------------------------------------------- | ------------------------------------------- |
| Duplicate / already seen / existence          | `HashSet`                                   |
| Frequency / count / occurrence / anagram      | `HashMap<Value, Count>`                     |
| Pair equals target                            | HashMap if unsorted; Two Pointers if sorted |
| Index / distance between occurrences          | `HashMap<Value, Index>`                     |
| Contiguous subarray or substring              | Sliding Window / Prefix Sum / Kadane        |
| Longest/shortest window with a condition      | Sliding Window                              |
| Exact subarray sum, especially with negatives | Prefix Sum + HashMap                        |
| Maximum/minimum continuous sum                | Kadane                                      |
| Sorted data / first-last occurrence           | Binary Search                               |
| Top K / Kth largest / repeatedly best item    | Heap                                        |
| Intervals / reachability / local selection    | Greedy                                      |

---

# 3. Set vs HashSet

In Java:

```java
Set<Integer> seen = new HashSet<>();
```

* `Set` is the **interface/type**.
* `HashSet` is the **implementation/object**.
* For approximately 80% of DSA problems, use `HashSet`.
* Use `TreeSet` only when sorted order, floor, ceiling, minimum or maximum is required.
* Use `LinkedHashSet` only when insertion order matters.

### Use a Set when only presence matters

```java
if (seen.contains(num)) {
    return true;
}

seen.add(num);
```

An even shorter duplicate check:

```java
if (!seen.add(num)) {
    return true;
}
```

### Use a Map when additional information matters

```java
Map<Integer, Integer> frequency = new HashMap<>();
```

You need a map when storing:

* Frequency
* Index
* List/group
* Prefix-sum count

---

# 4. `for` vs `while` vs `if`

## Use `for` when every element is scanned once

```java
for (int right = 0; right < nums.length; right++) {
}
```

Typical uses:

* Frequency counting
* HashMap lookup
* Moving the right pointer
* Kadane
* Heap processing

## Use `while` when an operation may repeat

```java
while (windowIsInvalid) {
    remove(nums[left]);
    left++;
}
```

Use `while`, not `if`, when one removal may not be enough.

Example:

```text
Window = a b c d e
```

If three elements must be removed to restore validity, an `if` removes only one. A `while` continues until the window becomes valid.

## Use `if` when the action happens at most once per iteration

```java
if (windowSize > k) {
    remove(nums[left]);
    left++;
}
```

## Note: 2-Pointers while inside the for loop whereas Sliding Window if inside the for loop

In a fixed-size window, the size normally increases by only one during each iteration, so one removal is sufficient.

### Important complexity rule

A `while` inside a `for` does **not automatically mean O(n²)**.

```java
for (right ...) {
    while (...) {
        left++;
    }
}
```

If `left` and `right` only move forward, each element enters and leaves once, so it is usually **O(n)**.

---

# 5. The Most Important Add/Remove Rules

Your notes correctly summarize Sliding Window as:

> Right adds, left removes, state describes the window, and the condition decides whether left moves. 

Remember these exact templates.

## A. Frequency Map: Add immediately

```java
for (int num : nums) {
    frequency.put(
        num,
        frequency.getOrDefault(num, 0) + 1
    );
}
```

Here, every element contributes to the count, so update immediately.

---

## B. Complement Map: Check before adding

Example: Two Sum.

```java
for (int i = 0; i < nums.length; i++) {
    int required = target - nums[i];

    if (map.containsKey(required)) {
        return new int[]{map.get(required), i};
    }

    map.put(nums[i], i);
}
```

### Why check before adding?

To avoid using the current element with itself.

Mental rule:

> **Look for the partner among previous elements, then store the current element.**

---

## C. Prefix Sum: Check before storing current prefix

```java
prefixSum += num;

count += prefixFrequency.getOrDefault(prefixSum - k, 0);

prefixFrequency.put(
    prefixSum,
    prefixFrequency.getOrDefault(prefixSum, 0) + 1
);
```

Mental rule:

> **Calculate current prefix → search previous prefix → store current prefix.**

Do not store first, because the current prefix should not incorrectly pair with itself.

---

## D. Sliding Window: Add right, remove left

Universal frequency-map style:

```java
for (int right = 0; right < nums.length; right++) {

    add(nums[right]);

    while (windowIsInvalid()) {
        remove(nums[left]);
        left++;
    }

    updateAnswer();
}
```

Mental rule:

> `right` explores new possibilities.
> `left` repairs the window.

---

# 6. Longest vs Smallest Window

This is one of the most important distinctions.

## Longest valid window

Examples:

* Longest substring without repetition
* At most K distinct elements
* Longest ones after flipping K zeroes

```java
for (int right = 0; right < n; right++) {
    add(right);

    while (windowIsInvalid()) {
        remove(left);
        left++;
    }

    maxLength = Math.max(maxLength, right - left + 1);
}
```

### Rule

> **While invalid → shrink.
> Once valid → update maximum.**

---

## Smallest satisfying window

Examples:

* Minimum size subarray sum
* Minimum window substring

```java
for (int right = 0; right < n; right++) {
    add(right);

    while (windowIsValid()) {
        minLength = Math.min(minLength, right - left + 1);

        remove(left);
        left++;
    }
}
```

### Rule

> **While valid → record answer → shrink further.**

This exact longest-versus-smallest distinction is also captured in your notes. 

---

# 7. Special Sliding Window with HashSet

For “longest substring without repeating characters”:

```java
for (int right = 0; right < s.length(); right++) {
    char current = s.charAt(right);

    while (set.contains(current)) {
        set.remove(s.charAt(left));
        left++;
    }

    set.add(current);

    maxLength = Math.max(maxLength, right - left + 1);
}
```

Here you check before adding because a `Set` cannot store duplicate counts.

Mental rule:

> **Duplicate arriving? Remove from left until duplicate disappears, then add it.**

With a frequency map, you may add first and then shrink while the count is greater than one. Both styles work—do not mix their ordering.

---

# 8. Fixed Sliding Window

Question says:

* Size exactly `K`
* Every `K` consecutive elements
* Maximum average/sum of size `K`

```java
for (int right = 0; right < nums.length; right++) {
    windowSum += nums[right];

    if (right - left + 1 > k) {
        windowSum -= nums[left];
        left++;
    }

    if (right - left + 1 == k) {
        answer = Math.max(answer, windowSum);
    }
}
```

Rule:

> **Add right → if oversized, remove left → when size is K, calculate answer.**

---

# 9. Two-Pointer Movement Rules

## Opposite direction

Usually:

* Sorted array
* Pair target
* Palindrome
* Container problems

```java
while (left < right) {
    int sum = nums[left] + nums[right];

    if (sum == target) {
        return true;
    } else if (sum < target) {
        left++;
    } else {
        right--;
    }
}
```

Mental rule:

* Need larger value → move `left`
* Need smaller value → move `right`
* Used current left value → move `left`
* Used current right value → move `right`
* Palindrome characters equal → move both

Do not move pointers randomly. Move the pointer whose current value can no longer produce the answer.

---

## Same-direction slow/fast

Used for:

* Remove duplicates
* Move zeroes
* Remove element
* In-place filtering

```java
int slow = 0;

for (int fast = 0; fast < nums.length; fast++) {
    if (elementShouldBeKept(nums[fast])) {
        nums[slow] = nums[fast];
        slow++;
    }
}
```

Mental model:

* `fast` = reader
* `slow` = writer
* Accepted element → write and increment `slow`
* Rejected element → only `fast` moves

---

## Merge two sorted inputs

```java
while (i < arr1.length && j < arr2.length) {
    if (arr1[i] <= arr2[j]) {
        take(arr1[i]);
        i++;
    } else {
        take(arr2[j]);
        j++;
    }
}
```

Rule:

> **Move the pointer whose element you consumed.**

---

# 10. Prefix Sum Decision

Use Prefix Sum when the problem says:

* Exact subarray sum
* Count subarrays
* Range sum
* Equal number of two types
* Divisible by K
* Negative values may be present

## Count subarrays

```java
map.put(0L, 1);
```

Map stores:

```text
prefix sum → frequency
```

Every occurrence must be stored.

## Longest subarray

```java
map.put(0L, -1);
```

Map stores:

```text
prefix sum → earliest index
```

Use:

```java
map.putIfAbsent(prefixSum, i);
```

Never overwrite the earliest index because an earlier index creates a longer subarray.

Your notes capture the key difference: count stores frequency, while longest stores the earliest index. 

---

# 11. Sliding Window or Prefix Sum?

This confusion is common.

## Use Sliding Window when

* Subarray/substring is contiguous
* The window condition changes predictably
* For sum-based windows, values are generally non-negative
* Expanding increases the sum and shrinking decreases it

## Use Prefix Sum + HashMap when

* Exact sum is required
* Negative values exist
* You need the number of matching subarrays
* Sliding the left pointer cannot predictably fix the sum

### Golden example

```text
Minimum length with sum >= K, positive values
→ Sliding Window
```

```text
Count subarrays with sum exactly K, negatives possible
→ Prefix Sum + HashMap
```

---

# 12. Kadane

Question says:

* Maximum/minimum continuous subarray sum
* Best continuous gain
* Choose whether to continue or restart

```java
current = Math.max(num, current + num);
answer = Math.max(answer, current);
```

Mental rule:

> At every element:
> **Start a new subarray here, or extend the previous subarray?**

No Set, Map, `left`, or `right` is normally required.

---

# 13. Binary Search

Use when:

* Array is sorted
* First/last occurrence
* Insertion position
* Search space has a monotonic answer

## Normal search

```text
target smaller → high moves left
target larger → low moves right
```

## Binary search on answer

Look for:

```text
Impossible Impossible Impossible Possible Possible
```

Examples:

* Minimum speed
* Minimum capacity
* Maximum minimum distance

Mental rule:

> Guess an answer with `mid`, then ask:
> **Is this candidate feasible?**

---

# 14. Heap

Use when:

* Top K
* Kth largest/smallest
* Repeatedly need the best remaining item
* Priority changes dynamically

## Top K largest

Keep a **min-heap of size K**:

```java
for (int value : nums) {
    minHeap.offer(value);

    if (minHeap.size() > k) {
        minHeap.poll();
    }
}
```

Rule:

> **Add candidate first, then remove the worst extra candidate.**

Remember:

* K largest → min-heap of size K
* K smallest → max-heap of size K

---

# 15. Greedy

Consider Greedy when:

* Intervals are present
* Need maximum non-overlapping selections
* Need minimum resources/steps
* Need farthest reachable position
* Sorting makes the next safe choice obvious

Typical flow:

```java
sort(items);

for (Item item : items) {
    if (compatible(item)) {
        select(item);
        updateBoundary();
    }
}
```

Rule:

> Select only when compatible; after selecting, update the boundary.

Do not update the boundary for rejected items unless the problem specifically requires replacing the previous selection.

---

# 16. Final 30-Second Interview Decision Flow

Use this exact order:

```text
1. Is it contiguous?
   Yes → Sliding Window / Prefix Sum / Kadane

2. Is it sorted or can sorting help?
   Yes → Two Pointers / Binary Search / Greedy

3. Do I need to remember previous values?
   Presence → Set
   Count → Frequency Map
   Index → Index Map
   Partner → Complement Map

4. Is it Top K or repeatedly best?
   Yes → Heap

5. Is it maximum/minimum continuous sum?
   Yes → Kadane

6. Is the answer space monotonic?
   Yes → Binary Search on Answer
```

# The One Line to Remember

> **HashMap remembers, two pointers eliminate, sliding window maintains, prefix sum compares past state, Kadane continues or restarts, binary search removes half, heap keeps the best K, and greedy makes the safest immediate choice.**

# WH Framework to Use Before Coding

For almost every array/string problem, answer these questions aloud:

| WH Question                                               | What you are deciding                                        |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| **What** is the question asking?                          | Existence, count, longest, shortest, index, pair or maximum? |
| **Where** is the answer located?                          | Anywhere in array, sorted pair, or contiguous range?         |
| **What** information must I remember?                     | Presence, frequency, index, sum or window state?             |
| **Which** pattern maintains that information efficiently? | Set, Map, Window, Prefix Sum, Two Pointers, etc.             |
| **When** does the state become valid/invalid?             | Duplicate found, sum reached, size exceeded, etc.            |
| **How** do I repair the state?                            | Move `left`, remove frequency, move pointer?                 |
| **When** should I update the answer?                      | Before shrinking, after shrinking, or immediately?           |
| **Why not** another common pattern?                       | This proves your pattern selection is intentional.           |

In an interview, spend 20–40 seconds answering these before coding.

---

# Example 1: Contains Duplicate

## Problem

Given an integer array, return `true` if any value appears more than once.

## WH Analysis

| Question                      | Thinking                                              |
| ----------------------------- | ----------------------------------------------------- |
| **What is required?**         | Only whether a duplicate exists                       |
| **What should I remember?**   | Values already seen                                   |
| **Do I need frequency?**      | No                                                    |
| **Do I need an index?**       | No                                                    |
| **Which structure fits?**     | `HashSet`                                             |
| **When is the answer found?** | When insertion fails because the value already exists |

Your notes identify duplicate/existence problems as the natural use case for seen-value storage. Frequency maps are only necessary when actual occurrence counts matter. 

## Why Set, Not Map?

A map stores:

```text
value → additional information
```

But this problem has no additional information. We only ask:

```text
Have I seen this value before?
```

Therefore:

```java
Set<Integer> seen = new HashSet<>();

for (int num : nums) {
    if (!seen.add(num)) {
        return true;
    }
}

return false;
```

## Why Only `for` + `if`?

Every number is examined once. There is no window to repair and no operation that must repeat.

* `for` scans every number.
* `if` checks one condition.
* No `while` is required.

## Interview Explanation

> “The problem only asks for duplicate existence, so I do not need frequency or index information. I will maintain a HashSet of previously seen values. If adding the current value returns false, the value already exists and I can return immediately. This gives O(n) average time and O(n) space.”

## Memory Rule

> **Only presence required → HashSet.**

---

# Example 2: Two Sum in an Unsorted Array

## Problem

Find two different indices whose values add up to a target.

## WH Analysis

| Question                      | Thinking                                |
| ----------------------------- | --------------------------------------- |
| **What is required?**         | A pair and their indices                |
| **What should I remember?**   | Previously visited value and its index  |
| **What should I search for?** | `target - currentValue`                 |
| **Which structure fits?**     | `HashMap<Value, Index>`                 |
| **When should I check?**      | Before storing the current element      |
| **Why?**                      | To avoid pairing an element with itself |

The complement-map template in your notes follows exactly this order: calculate the required value, check previously stored values, and then store the current value. 

## Pattern Selection

For the current number:

```text
current + required = target
```

Therefore:

```text
required = target - current
```

We ask:

> Have I already visited `required`?

```java
Map<Integer, Integer> previousIndex = new HashMap<>();

for (int i = 0; i < nums.length; i++) {
    int required = target - nums[i];

    if (previousIndex.containsKey(required)) {
        return new int[]{previousIndex.get(required), i};
    }

    previousIndex.put(nums[i], i);
}
```

## Why Check Before Adding?

Suppose:

```text
current value = 3
target = 6
```

If we store `3` before checking, the current element could find itself as the complement.

The safe order is:

```text
1. Search among previous elements
2. Store the current element for future elements
```

## Why Map, Not Set?

A Set can confirm that the complement exists, but the problem asks for the complement’s **index**.

Therefore we need:

```text
value → index
```

## Why Not Two Pointers?

Two pointers work naturally when the input is sorted. Sorting an unsorted array can destroy the original index relationship unless we create additional objects containing value and index.

For the standard Two Sum problem, HashMap is simpler and O(n).

## Interview Explanation

> “For every current value, I need to know whether its complement has already appeared. Because the answer requires indices, I will store each previous value with its index in a HashMap. I will check for the complement before storing the current value so that the same element cannot be used twice.”

## Memory Rule

> **Find partner → check partner first → store current later.**

---

# Example 3: Longest Substring Without Repeating Characters

## Problem

Find the length of the longest contiguous substring with no repeated characters.

## WH Analysis

| Question                           | Thinking                        |
| ---------------------------------- | ------------------------------- |
| **Is it contiguous?**              | Yes, it asks for a substring    |
| **Is the size fixed?**             | No                              |
| **What is being optimized?**       | Maximum length                  |
| **What makes the window invalid?** | A repeated character            |
| **How do I repair it?**            | Remove characters from the left |
| **When do I update maximum?**      | After the window becomes valid  |

This is a variable Sliding Window: `right` expands the window, `left` removes elements, the Set represents the state, and the duplicate condition controls movement. 

## Pattern Selection

The problem contains three strong clues:

```text
Longest + substring + without repeating
```

That means:

```text
Longest valid contiguous window
```

```java
Set<Character> window = new HashSet<>();
int left = 0;
int maxLength = 0;

for (int right = 0; right < s.length(); right++) {
    char current = s.charAt(right);

    while (window.contains(current)) {
        window.remove(s.charAt(left));
        left++;
    }

    window.add(current);

    maxLength = Math.max(
        maxLength,
        right - left + 1
    );
}
```

## Why `while`, Not `if`?

One removal may not eliminate the duplicate.

For example, the duplicate character may exist several positions away from `left`. We must continue removing until the previous occurrence leaves the window.

Therefore:

```java
while (window.contains(current))
```

not:

```java
if (window.contains(current))
```

## Why Check Before Adding?

A Set cannot represent two copies of the same character.

Therefore:

```text
1. Detect duplicate
2. Remove until duplicate disappears
3. Add current character
4. Update answer
```

A frequency-map solution can add first and then shrink while the count exceeds one. Both styles are valid, but their order should not be mixed.

## Why Update After Shrinking?

Before shrinking, the window contains a duplicate and is invalid.

For a **longest valid window**:

```text
while invalid → shrink
after valid → update maximum
```

This longest-window rule is explicitly reflected in your templates. 

## Interview Explanation

> “Because the question asks for the longest contiguous substring satisfying a condition, I will use a variable Sliding Window. The right pointer expands the window, and a Set stores its characters. Whenever the current character already exists, I will repeatedly remove characters from the left until the window becomes valid again. I update the maximum length only after validity is restored.”

## Memory Rule

> **Longest valid → shrink while invalid → update after shrinking.**

---

# Example 4: Minimum Size Subarray Sum

## Problem

Given an array of positive integers, find the minimum length of a contiguous subarray whose sum is at least the target.

## WH Analysis

| Question                             | Thinking                       |
| ------------------------------------ | ------------------------------ |
| **Is it contiguous?**                | Yes, subarray                  |
| **What is optimized?**               | Minimum length                 |
| **What makes the window valid?**     | `windowSum >= target`          |
| **How do we find a smaller answer?** | Shrink from the left           |
| **When should we update minimum?**   | While the window remains valid |
| **Why does Sliding Window work?**    | All values are positive        |

## Pattern Selection

The important words are:

```text
Minimum length + contiguous + sum at least target
```

That means:

```text
Smallest satisfying window
```

```java
int left = 0;
int windowSum = 0;
int minLength = Integer.MAX_VALUE;

for (int right = 0; right < nums.length; right++) {
    windowSum += nums[right];

    while (windowSum >= target) {
        minLength = Math.min(
            minLength,
            right - left + 1
        );

        windowSum -= nums[left];
        left++;
    }
}
```

## Why Add Right First?

Before checking whether the target has been reached, the new element must become part of the window.

Therefore:

```text
Add current right value → check validity
```

## Why Update Inside the `while`?

Once the sum reaches the target, the window is valid. But it may not be the smallest window ending at this `right`.

Therefore we repeatedly:

```text
1. Record current valid length
2. Remove from left
3. Check whether the smaller window is still valid
```

This differs from longest-window logic:

```text
Longest valid:
while invalid → shrink
update after while
```

```text
Smallest satisfying:
while valid → update
shrink again
```

Your notes make this exact distinction between longest valid and smallest satisfying windows. 

## Why Does Positivity Matter?

With positive numbers:

* Adding from the right cannot decrease the sum.
* Removing from the left cannot increase the sum.

This predictable movement makes Sliding Window valid.

With negative numbers, removing an element may increase or decrease the sum unpredictably, so this simple template may fail.

## Interview Explanation

> “The problem asks for the shortest contiguous window satisfying a sum condition, and all values are positive. I will expand the right pointer until the sum becomes valid. While it remains valid, I will record the current length and shrink from the left to find the smallest possible window ending at the current right position.”

## Memory Rule

> **Smallest satisfying → while valid, update first, then remove.**

---

# Example 5: Count Subarrays With Sum Equal to K

## Problem

Count the number of contiguous subarrays whose sum is exactly `K`. Negative values may be present.

## WH Analysis

| Question                                 | Thinking                   |
| ---------------------------------------- | -------------------------- |
| **Is it contiguous?**                    | Yes                        |
| **Is exact sum required?**               | Yes                        |
| **Can negative values exist?**           | Yes                        |
| **Can Sliding Window reliably move?**    | No                         |
| **What previous information is needed?** | Previous prefix sums       |
| **Do we need existence or count?**       | Count                      |
| **Which structure fits?**                | Prefix Sum + Frequency Map |

The prefix-sum relationship is:

```text
Current prefix − Previous prefix = Subarray sum
```

Your notes define this as the core prefix difference model. 

## Deriving the Required Prefix

We need:

```text
currentPrefix - previousPrefix = K
```

Rearrange:

```text
previousPrefix = currentPrefix - K
```

Therefore, at every position, ask:

> How many times has `currentPrefix - K` appeared before?

```java
Map<Long, Integer> prefixFrequency = new HashMap<>();
prefixFrequency.put(0L, 1);

long prefixSum = 0;
int count = 0;

for (int num : nums) {
    prefixSum += num;

    long requiredPrefix = prefixSum - k;

    count += prefixFrequency.getOrDefault(
        requiredPrefix,
        0
    );

    prefixFrequency.put(
        prefixSum,
        prefixFrequency.getOrDefault(prefixSum, 0) + 1
    );
}
```

## Why Initialize `0 → 1`?

This represents one empty prefix before the array starts.

It allows a subarray beginning at index `0` to be counted.

For example, when:

```text
currentPrefix == K
```

Then:

```text
requiredPrefix = currentPrefix - K = 0
```

The initial zero prefix represents the range starting from index `0`.

## Why Frequency Map, Not Set?

A Set only says whether a required prefix exists.

But several previous positions may have the same prefix sum, and each one creates a different valid subarray.

Therefore we need:

```text
prefix sum → number of occurrences
```

## Why Check Before Storing Current Prefix?

Only previous prefix sums should form a subarray ending at the current position.

The order is:

```text
1. Update current prefix
2. Find required previous prefix
3. Add matches to answer
4. Store current prefix for future positions
```

Storing first can incorrectly allow the current prefix to match itself, especially when `K = 0`.

## Why Not Sliding Window?

Because negative numbers destroy monotonic movement.

When the sum is too large:

* Removing the left value might decrease it.
* But if the left value is negative, removing it increases the sum.

Therefore there is no reliable rule such as “sum too large, move left.”

## Interview Explanation

> “The problem asks for the count of contiguous subarrays with an exact sum, and negative values may be present, so Sliding Window is not reliable. I will use a running prefix sum. A subarray has sum K when a previous prefix equals the current prefix minus K. Because the same prefix can occur multiple times, the HashMap must store frequencies rather than only presence.”

## Memory Rule

> **Exact subarray sum with negatives → current prefix searches for `prefix − K`.**

---

# Final Comparison

| Problem                  | Pattern              | Stored State       | Add/Remove Rule                        | Answer Update                  |
| ------------------------ | -------------------- | ------------------ | -------------------------------------- | ------------------------------ |
| Contains Duplicate       | HashSet              | Seen values        | Check/add current                      | Immediately on duplicate       |
| Two Sum                  | Complement Map       | Value → index      | Check partner, then add current        | Immediately on partner         |
| Longest Unique Substring | Sliding Window + Set | Current characters | While duplicate, remove left; then add | After window becomes valid     |
| Minimum Size Sum         | Sliding Window + Sum | Current sum        | Add right; while valid, remove left    | Inside `while`, before removal |
| Subarray Sum Equals K    | Prefix Sum + Map     | Prefix → frequency | Check old prefix, then store current   | During every iteration         |

# The Interview Speaking Formula

Use this sentence structure:

> “The problem asks for **[output]** over **[contiguous/non-contiguous/sorted]** data. I need to maintain **[state]**. The state becomes invalid or useful when **[condition]** occurs. Therefore, I will use **[pattern]**. I will add/update **[where]**, remove or move **[when]**, and update the final answer **[before/after shrinking]**. I am not using **[alternative pattern]** because **[reason]**.”

That explanation shows the interviewer that you are not recalling random code—you understand **why the template works**.

# Another 5 Mid-Level DSA Problems — WH Approach

These five are completely new and cover five different patterns:

1. Valid Anagram — Frequency Map
2. Remove Duplicates from Sorted Array — Slow/Fast Pointers
3. Maximum Sum Subarray of Size K — Fixed Sliding Window
4. Longest Subarray With Sum K — Prefix Sum + Earliest Index
5. Top K Frequent Elements — Frequency Map + Heap

---

# Example 1: Valid Anagram

## Problem

Given two strings `s` and `t`, determine whether `t` is an anagram of `s`.

An anagram contains exactly the same characters with exactly the same frequencies.

## WH Analysis

| Question                                  | Thinking                               |
| ----------------------------------------- | -------------------------------------- |
| **What is required?**                     | Compare character occurrences          |
| **What should I remember?**               | Frequency of every character           |
| **Which data structure fits?**            | `HashMap<Character, Integer>`          |
| **When can I return false early?**        | Different lengths or missing character |
| **When should I remove a key?**           | When its frequency becomes zero        |
| **What proves the strings are anagrams?** | All frequencies cancel out             |

Frequency and anagram problems are direct Frequency Map use cases in your notes. 

## Pattern Selection

The important clue is:

```text
Same characters with the same number of occurrences
```

Presence alone is insufficient.

For example:

```text
s = "aab"
t = "abb"
```

Both contain `a` and `b`, but the frequencies differ.

Therefore, a `Set` cannot solve this. We need:

```text
character → frequency
```

## Code

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }

    Map<Character, Integer> frequency = new HashMap<>();

    for (char ch : s.toCharArray()) {
        frequency.put(
            ch,
            frequency.getOrDefault(ch, 0) + 1
        );
    }

    for (char ch : t.toCharArray()) {
        if (!frequency.containsKey(ch)) {
            return false;
        }

        frequency.put(ch, frequency.get(ch) - 1);

        if (frequency.get(ch) == 0) {
            frequency.remove(ch);
        }
    }

    return frequency.isEmpty();
}
```

This follows the same anagram frequency-cancellation approach present in your notes. 

## Why Add in the First Loop?

The first string establishes what characters are available:

```text
aab → a:2, b:1
```

## Why Subtract in the Second Loop?

Each character from the second string consumes one available occurrence.

```text
Current count − 1
```

If a character is not available, the strings cannot be anagrams.

## Why Remove When Count Becomes Zero?

Removing zero-frequency entries gives a clean final condition:

```java
return frequency.isEmpty();
```

It is not compulsory—you could check that every value is zero—but removal simplifies the final validation.

## Why `if`, Not `while`?

Each character from the second string reduces exactly one count.

There is no repeated repair operation, so one `if` check is enough.

## Why Not Sorting?

Sorting both strings also works:

```text
O(n log n)
```

The Frequency Map approach is generally:

```text
O(n) time
O(k) space
```

where `k` is the number of distinct characters.

## Interview Explanation

> “The problem asks whether both strings contain identical character frequencies. Therefore, presence alone is insufficient, and I will use a frequency map. I will increment counts using the first string and decrement them using the second. If a character is unavailable, I can return false immediately. When a count becomes zero, I remove it, and an empty map at the end confirms that all frequencies matched.”

## Memory Rule

> **Same elements with same occurrences → Frequency Map.**

---

# Example 2: Remove Duplicates From Sorted Array

## Problem

Given a sorted array, remove duplicates in place and return the number of unique elements.

The first `k` positions should contain the unique values.

## WH Analysis

| Question                            | Thinking                              |
| ----------------------------------- | ------------------------------------- |
| **What is required?**               | In-place filtering                    |
| **Is the array sorted?**            | Yes                                   |
| **What does one pointer do?**       | Scan every element                    |
| **What does the other pointer do?** | Maintain the next writing position    |
| **Which pattern fits?**             | Same-direction slow/fast pointers     |
| **When should slow move?**          | Only when a new unique value is found |

Your notes describe this pattern as: fast scans every element, while slow maintains the correct writing position. 

## Pattern Selection

The important clues are:

```text
Sorted + remove in place + return new length
```

Because the array is sorted, duplicate values appear next to each other.

We do not need a Set to identify duplicates. We only compare the current value with the last accepted value.

Mental model:

```text
fast = reader
slow = writer
```

## Code

```java
public int removeDuplicates(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }

    int slow = 1;

    for (int fast = 1; fast < nums.length; fast++) {
        if (nums[fast] != nums[slow - 1]) {
            nums[slow] = nums[fast];
            slow++;
        }
    }

    return slow;
}
```

## What Does `slow` Represent?

`slow` is not merely a pointer.

It represents:

```text
The next position where a unique value should be written
```

It also represents the number of accepted elements.

## When Do We Write?

Only when:

```java
nums[fast] != nums[slow - 1]
```

That means the current value differs from the last unique value.

Then:

```java
nums[slow] = nums[fast];
slow++;
```

## When Do We Ignore an Element?

When:

```java
nums[fast] == nums[slow - 1]
```

The value is a duplicate.

In that case:

* `fast` continues scanning.
* `slow` does not move.
* Nothing is written.

## Why Start `slow` at 1?

For a non-empty array, the first element is automatically unique.

Therefore:

```text
nums[0] is already accepted
```

The next available writing position is index `1`.

## Why Not Use a HashSet?

A HashSet would:

* Require extra O(n) space.
* Lose the in-place nature.
* Ignore the useful fact that the input is sorted.

The sorted order lets us solve it in:

```text
O(n) time
O(1) extra space
```

## Why `for` + `if`?

* `for` scans every element once.
* `if` decides whether the current element should be accepted.
* No repeated shrinking or correction is required.

## Interview Explanation

> “Because the array is sorted, equal values are adjacent. I will use a slow-fast pointer pattern. The fast pointer reads every value, while the slow pointer marks the next writing position. Whenever the fast value differs from the last accepted value, I copy it to the slow position and increment slow. The final slow value is the number of unique elements.”

## Memory Rule

> **In-place filtering → fast reads, slow writes.**

---

# Example 3: Maximum Sum Subarray of Size K

## Problem

Find the maximum sum among all contiguous subarrays of exactly size `K`.

## WH Analysis

| Question                           | Thinking                        |
| ---------------------------------- | ------------------------------- |
| **Is the answer contiguous?**      | Yes, subarray                   |
| **Is the window size fixed?**      | Yes, exactly `K`                |
| **What state must be maintained?** | Current window sum              |
| **What does right do?**            | Adds a new element              |
| **When does left move?**           | When window size exceeds `K`    |
| **When should the answer update?** | When window size is exactly `K` |

This is the standard fixed-size Sliding Window template from your notes. 

## Pattern Selection

The strongest clue is:

```text
Every contiguous subarray of exactly size K
```

That immediately suggests:

```text
Fixed Sliding Window
```

A brute-force approach would calculate the sum of every `K`-length subarray separately, repeating most additions.

Sliding Window reuses the previous sum.

## Code

```java
public int maxSumSubarrayOfSizeK(int[] nums, int k) {
    if (nums == null || k <= 0 || k > nums.length) {
        throw new IllegalArgumentException("Invalid input");
    }

    int left = 0;
    int windowSum = 0;
    int maxSum = Integer.MIN_VALUE;

    for (int right = 0; right < nums.length; right++) {
        windowSum += nums[right];

        if (right - left + 1 > k) {
            windowSum -= nums[left];
            left++;
        }

        if (right - left + 1 == k) {
            maxSum = Math.max(maxSum, windowSum);
        }
    }

    return maxSum;
}
```

## Why Add `nums[right]` First?

The right pointer represents the new element entering the window.

Therefore:

```text
Expand → add right
```

## Why Remove From the Left?

When the window size becomes greater than `K`, the oldest element must leave.

```java
windowSum -= nums[left];
left++;
```

Mental model:

```text
Right adds the newest element.
Left removes the oldest element.
```

## Why Use `if`, Not `while`?

During one iteration, `right` moves only one position.

Therefore the window grows by at most one element. It can exceed `K` by only one, so one removal restores the correct size.

```java
if (windowSize > k)
```

is sufficient.

Using `while` would also work, but it is unnecessary here.

## Why Update Only When Size Equals K?

A smaller window is not a valid candidate.

```java
if (right - left + 1 == k)
```

Only exact-size windows should be compared.

## Why Initialize `maxSum` to `Integer.MIN_VALUE`?

If all values are negative, initializing to zero would incorrectly return zero even though zero is not a valid window sum.

## Why Not Prefix Sum?

Prefix Sum can also calculate each size-K range in O(1) after preprocessing.

But for a single left-to-right calculation, Sliding Window is simpler and uses O(1) extra space.

## Interview Explanation

> “The question asks for the maximum sum of a contiguous subarray with an exact fixed size, so I will use a fixed Sliding Window. The right pointer adds the incoming element. If the window exceeds K, I remove the leftmost element and advance left. Whenever the window size is exactly K, I update the maximum sum.”

## Memory Rule

> **Exact window size K → add right, remove when oversized, update at size K.**

---

# Example 4: Longest Subarray With Sum K

## Problem

Find the maximum length of a contiguous subarray whose sum equals `K`.

Negative values may exist.

## WH Analysis

| Question                                 | Thinking                          |
| ---------------------------------------- | --------------------------------- |
| **Is it contiguous?**                    | Yes                               |
| **Is the sum exact?**                    | Yes                               |
| **Can negatives exist?**                 | Yes                               |
| **Can Sliding Window move predictably?** | No                                |
| **What previous state is required?**     | Previous prefix sum and its index |
| **What is being optimized?**             | Maximum length                    |
| **Which index should be stored?**        | Earliest index                    |

For the longest prefix-sum variation, your notes store the first occurrence of every prefix and initialize `0 → -1`. 

## Pattern Selection

For a subarray ending at index `i`:

```text
currentPrefix − previousPrefix = K
```

Therefore:

```text
previousPrefix = currentPrefix − K
```

To calculate length, we need the index of that previous prefix:

```text
length = currentIndex − previousIndex
```

So the map should store:

```text
prefix sum → earliest index
```

## Code

```java
public int longestSubarrayWithSumK(int[] nums, long k) {
    Map<Long, Integer> firstIndex = new HashMap<>();
    firstIndex.put(0L, -1);

    long prefixSum = 0;
    int maxLength = 0;

    for (int i = 0; i < nums.length; i++) {
        prefixSum += nums[i];

        long requiredPrefix = prefixSum - k;

        if (firstIndex.containsKey(requiredPrefix)) {
            int length = i - firstIndex.get(requiredPrefix);
            maxLength = Math.max(maxLength, length);
        }

        firstIndex.putIfAbsent(prefixSum, i);
    }

    return maxLength;
}
```

## Why Initialize `0 → -1`?

The index `-1` represents the position before the array begins.

This handles a valid subarray starting from index `0`.

Suppose:

```text
prefixSum at index i = K
```

Then:

```text
requiredPrefix = K − K = 0
length = i − (-1) = i + 1
```

## Why Store the Earliest Index?

To maximize:

```text
currentIndex − previousIndex
```

we want the smallest possible previous index.

For example, if the same prefix appears at indices `1` and `5`, and the current index is `10`:

```text
10 − 1 = 9
10 − 5 = 5
```

The earlier index gives the longer subarray.

Therefore:

```java
firstIndex.putIfAbsent(prefixSum, i);
```

Do not overwrite an earlier index.

## Why Is This Different From Count Subarrays?

For counting:

```text
prefix sum → frequency
```

Every occurrence matters.

For longest length:

```text
prefix sum → earliest index
```

Only the earliest useful occurrence matters.

## Why Check Before Storing?

The required prefix must come from a position before the current index.

Safe order:

```text
1. Update current prefix
2. Search required previous prefix
3. Calculate length
4. Store current prefix if new
```

## Why Not Sliding Window?

With negative numbers, movement is not predictable.

If the sum is too large, removing the left element might:

* Decrease the sum if it is positive.
* Increase the sum if it is negative.

Therefore, a standard Sliding Window cannot reliably decide when to move `left`.

## Interview Explanation

> “The problem asks for the longest contiguous subarray with an exact sum, and negative numbers may be present, so Sliding Window is not reliable. I will use a running prefix sum and store the earliest index of each prefix. At index i, I search for prefixSum minus K. If it exists, the difference between the current index and that earliest index gives a valid subarray length. I use putIfAbsent because the earliest index produces the longest result.”

## Memory Rule

> **Longest exact sum → prefix sum stores earliest index, never overwrite it.**

---

# Example 5: Top K Frequent Elements

## Problem

Given an integer array, return the `K` most frequently occurring elements.

## WH Analysis

| Question                               | Thinking                 |
| -------------------------------------- | ------------------------ |
| **What information is needed first?**  | Frequency of each value  |
| **What is required after counting?**   | Best K values            |
| **Which structure counts?**            | HashMap                  |
| **Which structure maintains best K?**  | Heap                     |
| **What kind of heap?**                 | Min-heap by frequency    |
| **When should an element be removed?** | When heap size exceeds K |
| **Which element should be removed?**   | Least frequent candidate |

Your notes combine a Frequency Map with a fixed-size heap for Top K Frequent problems. 

## Pattern Selection

This problem contains two separate requirements:

```text
1. Calculate frequency
2. Select the best K
```

Therefore it naturally combines two patterns:

```text
Frequency Map + Min-Heap
```

## Code

```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequency = new HashMap<>();

    for (int num : nums) {
        frequency.put(
            num,
            frequency.getOrDefault(num, 0) + 1
        );
    }

    PriorityQueue<Integer> minHeap =
            new PriorityQueue<>(
                (a, b) -> Integer.compare(
                    frequency.get(a),
                    frequency.get(b)
                )
            );

    for (int value : frequency.keySet()) {
        minHeap.offer(value);

        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }

    int[] answer = new int[k];

    for (int i = k - 1; i >= 0; i--) {
        answer[i] = minHeap.poll();
    }

    return answer;
}
```

## Why Build the Frequency Map First?

The heap needs to compare elements using their occurrence counts.

Therefore, frequencies must be available before heap selection begins.

```text
value → frequency
```

## Why Use a Min-Heap?

We want to retain the most frequent `K` elements.

The heap’s root should represent the weakest retained candidate:

```text
least frequent among the current K
```

When a better candidate arrives, the least frequent one can be removed easily.

## Why Add First and Remove Later?

For every distinct value:

```text
1. Consider the candidate
2. Add it to the heap
3. If more than K candidates exist, remove the weakest
```

```java
minHeap.offer(value);

if (minHeap.size() > k) {
    minHeap.poll();
}
```

After processing all distinct values, only the strongest K remain.

## Why Does `poll()` Remove the Correct Element?

The comparator orders values by frequency:

```java
Integer.compare(frequency.get(a), frequency.get(b))
```

Therefore, the smallest frequency stays at the top.

```java
minHeap.poll();
```

removes the least frequent candidate.

## Why Not Use a Max-Heap?

A max-heap could store all distinct elements and remove the maximum `K` times.

That approach works, but its heap may grow to the number of distinct values, `m`.

The fixed-size min-heap keeps only `K` values:

```text
Time: O(n + m log k)
Space: O(m + k)
```

This is especially useful when `K` is much smaller than the number of distinct elements.

## Why Not Sort All Frequencies?

Sorting all distinct elements costs approximately:

```text
O(m log m)
```

A size-K heap costs:

```text
O(m log k)
```

The heap is preferable when `K << m`.

## Interview Explanation

> “The problem has two stages. First, I need a frequency map because the selection depends on occurrence counts. Then I need to retain only the best K candidates, so I will use a min-heap ordered by frequency. I add every distinct value, and whenever the heap size exceeds K, I remove the least frequent candidate. At the end, the heap contains the K most frequent values.”

## Memory Rule

> **Top K → keep K candidates and remove the weakest extra candidate.**

---

# Final Pattern Comparison

| Problem            | Main clue                       | Pattern              | Stored information      | Important update rule                     |
| ------------------ | ------------------------------- | -------------------- | ----------------------- | ----------------------------------------- |
| Valid Anagram      | Same frequencies                | Frequency Map        | Character → count       | Increment first, decrement second         |
| Remove Duplicates  | Sorted + in-place               | Slow/Fast pointers   | No extra structure      | Fast reads; slow moves only when accepted |
| Maximum Size-K Sum | Exactly K contiguous            | Fixed Sliding Window | Current sum             | Add right, remove if oversized            |
| Longest Sum K      | Exact sum + negatives + longest | Prefix Map           | Prefix → earliest index | Use `putIfAbsent`                         |
| Top K Frequent     | Frequencies + best K            | Map + Min-Heap       | Count and K candidates  | Add candidate, remove weakest if size > K |

# The WH Interview Formula

For each new problem, speak in this order:

> **What** is the required output—existence, count, longest, shortest or Top K?
> **Where** can the answer exist—anywhere, sorted pair or contiguous range?
> **What** state must I remember—presence, frequency, index, sum or priority?
> **Which** pattern maintains that state efficiently?
> **When** should I add the current element?
> **When** should I remove, overwrite or move a pointer?
> **When** is the current state valid enough to update the answer?
> **Why** is another common pattern less suitable?

## Five One-Line Recall Rules

> **Frequency comparison → count up and count down.**

> **In-place filtering → fast reads and slow writes.**

> **Fixed K → add right, remove oldest, calculate at size K.**

> **Longest exact sum → preserve the earliest prefix index.**

> **Top K → maintain K winners and remove the weakest.**

# HashMap

## Template 1: Frequency Map

```java
Map<Integer, Integer> freq = new HashMap<>();

for (int num : nums) {
    freq.put(num, freq.getOrDefault(num, 0) + 1);
}
```
Ex: 
Count frequency
Find duplicates
Find majority
Top K frequent
Anagram

## Template 2: Complement Lookup

```java
Map<Integer, Integer> map = new HashMap<>();

for (int i = 0; i < nums.length; i++) {
    int required = target - nums[i];

    if (map.containsKey(required)) {
        // answer found
    }

    map.put(nums[i], i);
}
```
Ex:
Two Sum
Pair with target
Difference equals K

## Template 3: First / Last Index Map

```java
Map<Character, Integer> indexMap = new HashMap<>();

for (int i = 0; i < s.length(); i++) {
    char ch = s.charAt(i);

    if (indexMap.containsKey(ch)) {
        // seen before
    }

    indexMap.put(ch, i);
}
```
Ex:
Longest substring
First repeating
Distance between duplicates
Isomorphic strings

## Template 4: Prefix Sum Map

```java
Map<Integer, Integer> prefixCount = new HashMap<>();
prefixCount.put(0, 1);

int prefixSum = 0;
int count = 0;

for (int num : nums) {
    prefixSum += num;

    if (prefixCount.containsKey(prefixSum - k)) {
        count += prefixCount.get(prefixSum - k);
    }

    prefixCount.put(prefixSum, prefixCount.getOrDefault(prefixSum, 0) + 1);
}
```
Ex:
Subarray sum equals K
Count subarrays
Longest subarray with sum K

## Template 5: Grouping Map

```java
Map<String, List<String>> map = new HashMap<>();

for (String word : words) {
    String key = getKey(word);

    map.computeIfAbsent(key, x -> new ArrayList<>()).add(word);
}
```
Ex:
Group anagrams
Group by department
Group by category


## Valid Anagram

```java
import java.util.*;

public class ValidAnagram {

    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }

        Map<Character, Integer> freq = new HashMap<>();

        for (char ch : s.toCharArray()) {
            freq.put(ch, freq.getOrDefault(ch, 0) + 1);
        }

        for (char ch : t.toCharArray()) {
            if (!freq.containsKey(ch)) {
                return false;
            }

            freq.put(ch, freq.get(ch) - 1);

            if (freq.get(ch) == 0) {
                freq.remove(ch);
            }
        }

        return freq.isEmpty();
    }
}
```

# 2-Pointers

## Type 1: Opposite Direction

Used when:

```text
left starts from beginning
right starts from end
move based on condition
```

Template:

```java
int left = 0;
int right = nums.length - 1;

while (left < right) {
    if (condition) {
        // answer/update
    } else if (need bigger value) {
        left++;
    } else {
        right--;
    }
}
```

Common problems:

```text
Two Sum in sorted array
Valid Palindrome
Reverse String
Container With Most Water
3Sum after sorting
```

---

## Type 2: Same Direction / Slow-Fast

Used when:

```text
fast scans all elements
slow maintains correct position
```

Template:

```java
int slow = 0;

for (int fast = 0; fast < nums.length; fast++) {
    if (condition) {
        nums[slow] = nums[fast];
        slow++;
    }
}
```

Common problems:

```text
Remove duplicates from sorted array
Move zeroes
Remove element
Partition array
```

---

## Type 3: Sliding Window

Sliding window is also a two-pointer technique, but usually treated separately.

Used when:

```text
contiguous subarray/substring
left and right move forward
window valid/invalid condition
```

Template:

```java
int left = 0;

for (int right = 0; right < nums.length; right++) {
    // add nums[right]

    while (window invalid) {
        // remove nums[left]
        left++;
    }

    // update answer
}
```

Common problems:

```text
Longest substring without repeating
Minimum size subarray sum
Max sum subarray of size K
```

---

## Type 4: Merge Two Sorted Inputs

Used when:

```text
two sorted arrays/lists need to be merged or compared
```

Template:

```java
int i = 0;
int j = 0;

while (i < arr1.length && j < arr2.length) {
    if (arr1[i] <= arr2[j]) {
        // take arr1[i]
        i++;
    } else {
        // take arr2[j]
        j++;
    }
}
```

Common problems:

```text
Merge sorted arrays
Intersection of two sorted arrays
Merge two sorted linked lists
```

---

## Type 5: Fast-Slow Pointer in Linked List

Used when:

```text
slow moves 1 step
fast moves 2 steps
```

Template:

```java
ListNode slow = head;
ListNode fast = head;

while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
```

Common problems:

```text
Middle of linked list
Detect cycle
Find start of cycle
Remove nth node from end
```

## Movement Rule
Pair sum:
sum < target → left++
sum > target → right--

Palindrome:
equal → both move
not equal → false

Remove duplicates:
unique found → slow++, copy

Move zeroes:
non-zero found → write/swap at slow

Merge:
smaller value pointer moves

Linked list:
slow 1 step, fast 2 steps

# Sliding Window

## Fixed vs Variable Sliding Window

This is very important.

| Problem                              | Window Type     | Movement                               |
| ------------------------------------ | --------------- | -------------------------------------- |
| Max sum subarray of size K           | Fixed window    | Window size always K                   |
| Minimum size subarray sum            | Variable window | Expand until valid, shrink to optimize |
| Longest substring without repeat     | Variable window | Expand until duplicate, move left      |
| Longest ones after flipping K zeroes | Variable window | Maintain invalid count                 |
| Permutation in string                | Fixed window    | Compare frequency in window            |



## Sliding Window Has Four Components

Every Sliding Window problem contains these four things:

| Component          | Meaning                              | Example                           |
| ------------------ | ------------------------------------ | --------------------------------- |
| `left`             | Start of current window              | Character to remove               |
| `right`            | End of current window                | Character to add                  |
| Window state       | Information about current window     | Sum, frequency, unique characters |
| Validity condition | Whether current window is acceptable | Sum ≥ target, no duplicates       |


> right adds something
> left removes something
> state describes the window
> condition decides whether left should move

## Template 1: Fixed-Size Sliding Window
Window size = K
Subarray of length K
Every K consecutive elements

```java
public void fixedWindow(int[] nums, int k) {
    int left = 0;
    int windowState = 0;

    for (int right = 0; right < nums.length; right++) {
        // 1. Include nums[right]
        windowState += nums[right];

        // 2. Window is too large
        if (right - left + 1 > k) {
            windowState -= nums[left];
            left++;
        }

        // 3. Exact window size reached
        if (right - left + 1 == k) {
            // update answer
        }
    }
}
```
Alternative Style:
```java
for (int right = 0; right < nums.length; right++) {
    windowSum += nums[right];

    int left = 0;
    int windowSum = 0;
    int maxSum = Integer.MIN_VALUE;

    for (int right = 0; right < nums.length; right++) {
        windowSum += nums[right];

        if (right - left + 1 == k) {
            maxSum = Math.max(maxSum, windowSum);

            windowSum -= nums[left];
            left++;
        }
    }

    return maxSum;
}
```

## Template 2: Variable Window - Longest Valid Window
Longest
Maximum length
Largest valid substring/subarray
At most K
Without repeating
```java
public int longestValidWindow(int[] nums) {
    int left = 0;
    int maxLength = 0;

    for (int right = 0; right < nums.length; right++) {
        char current = s.charAt(right);
       
       /* window is invalid */
        while (window.contains(current)) {
            // Remove nums[left] from state
            window.remove(s.charAt(left));
            left++;
        }

         // Add nums[right] to state
         window.add(current);

        maxLength = Math.max(maxLength, right - left + 1);
    }

    return maxLength;
}
```
## Template 3: Smallest Satisfying Window
Minimum length
Smallest substring
Minimum window
Shortest valid subarray
```java
public int smallestValidWindow(int[] nums) {
    int left = 0;
    int minLength = Integer.MAX_VALUE;

    for (int right = 0; right < nums.length; right++) {
        // Add nums[right] to window state
        windowSum += nums[right];

        /* window is valid */
        while (windowSum >= target) {
            minLength = Math.min(
                    minLength,
                    right - left + 1
            );

            // Remove nums[left]
            windowSum -= nums[left];
            left++;
        }
    }

    return minLength == Integer.MAX_VALUE ? 0 : minLength;
}
```
Longest valid:
while invalid → shrink
then update maximum

Smallest satisfying:
while valid → update minimum
then shrink

## Template 4: Count Valid Subarrays
```java
public long countValidSubarrays(int[] nums) {
    int left = 0;
    long count = 0;

    for (int right = 0; right < nums.length; right++) {
        // Add nums[right]

        while (/* window invalid */) {
            // Remove nums[left]
            left++;
        }

        count += right - left + 1;
    }

    return count;
}
```

# Prefix Sum

## Convention 1: Inclusive Prefix
```java
prefix[i] = nums[0] + nums[1] + ... + nums[i];
```
Range Formula
```java
If L == 0:
    sum = prefix[R]
Else:
    sum = prefix[R] - prefix[L - 1]
```
## Convention 2: Exclusive Prefix with Extra Zero
```java
prefix[i + 1] = prefix[i] + nums[i];
```
Range Formula
```java
sum(L...R) = prefix[R + 1] - prefix[L]
```


## Prefix Sum Has Four Core Components

Every Prefix Sum problem contains some combination of these components:

| Component      | Meaning                                     | Example                         |
| -------------- | ------------------------------------------- | ------------------------------- |
| Prefix state   | Accumulated information until current index | Running sum                     |
| Previous state | Accumulated information before a subarray   | `prefixSum - k`                 |
| Difference     | Information inside a range                  | Current prefix minus old prefix |
| Storage        | Array, HashMap, Set or variable             | Prefix array or frequency map   |

```java
Current state
-
Old state
=
State of middle range
```
For Sums
```java
Current prefix sum - Previous prefix sum = Subarray sum
```
For Equal Counts
```java
Current balance - Previous balance = 0
```
For Divisibiltiy
```java
Same remainder at two positions
→ difference is divisible by K
```
## Template 1: Prefix Array for Range Queries
Many queries ask sum from L to R
The array does not change frequently
```java
public class PrefixRangeSum {

    private final long[] prefix;

    public PrefixRangeSum(int[] nums) {
        prefix = new long[nums.length + 1];

        for (int i = 0; i < nums.length; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }

    public long rangeSum(int left, int right) {
        if (left < 0 || right >= prefix.length - 1 || left > right) {
            throw new IllegalArgumentException("Invalid range");
        }

        return prefix[right + 1] - prefix[left];
    }
}
```
## Template 2: Running Prefix Sum + HashMap
Count subarrays with exact sum K
Find longest subarray with exact sum K
Check whether such a subarray exists
Negative values may be present

## Template 2A: Count Subarrays with Sum K
```java
import java.util.HashMap;
import java.util.Map;

public int countSubarraysWithSumK(int[] nums, int k) {
    Map<Long, Integer> prefixFrequency = new HashMap<>();

    prefixFrequency.put(0L, 1);

    long prefixSum = 0;
    int count = 0;

    for (int num : nums) {
        prefixSum += num;

        long requiredPrefix = prefixSum - k;

        count += prefixFrequency.getOrDefault(requiredPrefix, 0);

        prefixFrequency.put(
                prefixSum,
                prefixFrequency.getOrDefault(prefixSum, 0) + 1
        );
    }

    return count;
}
```

## Template 2B: Longest Subarray with Sum K
```java
import java.util.HashMap;
import java.util.Map;

public int longestSubarrayWithSumK(int[] nums, long k) {
    Map<Long, Integer> firstIndex = new HashMap<>();

    firstIndex.put(0L, -1);

    long prefixSum = 0;
    int maxLength = 0;

    for (int i = 0; i < nums.length; i++) {
        prefixSum += nums[i];

        long requiredPrefix = prefixSum - k;

        if (firstIndex.containsKey(requiredPrefix)) {
            int length = i - firstIndex.get(requiredPrefix);
            maxLength = Math.max(maxLength, length);
        }

        firstIndex.putIfAbsent(prefixSum, i);
    }

    return maxLength;
}
```
## Count vs Longest Prefix Template

| Requirement       | Initial Map        | Map Value                   | Update Rule                         |
| ----------------- | ------------------ | --------------------------- | ----------------------------------- |
| Count subarrays   | `0 → 1`            | Frequency                   | Increment every occurrence          |
| Longest subarray  | `0 → -1`           | Earliest index              | Use `putIfAbsent`                   |
| Existence         | Add prefix `0`     | Boolean presence/index      | Any occurrence is enough            |
| Shortest subarray | Depends on problem | Latest/useful ordered index | Usually needs deque or latest index |

## Template 2C: Check Whether a Subarray Sum Exists
```java
import java.util.HashSet;
import java.util.Set;

public boolean hasSubarrayWithSumK(int[] nums, long k) {
    Set<Long> seenPrefixSums = new HashSet<>();
    seenPrefixSums.add(0L);

    long prefixSum = 0;

    for (int num : nums) {
        prefixSum += num;

        if (seenPrefixSums.contains(prefixSum - k)) {
            return true;
        }

        seenPrefixSums.add(prefixSum);
    }

    return false;
}
```
## Template 3: Prefix Transformation / Balance


# Kadane's Algorithms

## Template 1 — Minimum Subarray Sum
```
Maximum subarray sum
Minimum subarray sum
Best continuous gain
Maximum alternating state when only one previous state is needed
```
Shape
```
current = best(startNew, extendPrevious);
answer = best(answer, current);
```

## Template 2: Dual-State Kadane

Track two states simultaneously.

Used for:

```text
Maximum product subarray
Maximum absolute subarray sum
Circular maximum subarray
```

Examples:

```text
Track maximum and minimum ending here.
Track maximum and minimum total.
```

Shape:

```java
newMax = ...
newMin = ...

globalMax = ...
globalMin = ...
```

---

## Template 3: Multi-State Kadane

Track whether a special operation has been used.

Used for:

```text
Maximum subarray with one deletion
Maximum subarray with one replacement
Maximum subarray with one multiplication
Maximum subarray with at most K modifications
```

Shape:

```text
stateWithoutOperation
stateWithOperation
```

Transition:

```text
Continue same state
or
Use the special operation now
```

---

## 1 - Minimum Subarray Sum

For maximum sum:

```java
currentMax = Math.max(num, currentMax + num);
```

For minimum sum, reverse `max` to `min`:

```java
currentMin = Math.min(num, currentMin + num);
```

## Java Code

```java
public static int minSubArray(int[] nums) {
    if (nums == null || nums.length == 0) {
        throw new IllegalArgumentException("Array must not be empty");
    }

    int currentMin = nums[0];
    int globalMin = nums[0];

    for (int i = 1; i < nums.length; i++) {
        currentMin = Math.min(nums[i], currentMin + nums[i]);
        globalMin = Math.min(globalMin, currentMin);
    }

    return globalMin;
}
```
## 2 Maximum Product Subarray Java Code
```java
public static int maxProductSubArray(int[] nums) {
    if (nums == null || nums.length == 0) {
        throw new IllegalArgumentException("Array must not be empty");
    }

    int currentMax = nums[0];
    int currentMin = nums[0];
    int globalMax = nums[0];

    for (int i = 1; i < nums.length; i++) {
        int num = nums[i];

        int previousMax = currentMax;
        int previousMin = currentMin;

        currentMax = Math.max(
                num,
                Math.max(previousMax * num, previousMin * num)
        );

        currentMin = Math.min(
                num,
                Math.min(previousMax * num, previousMin * num)
        );

        globalMax = Math.max(globalMax, currentMax);
    }

    return globalMax;
}
```
## 3 Maximum Subarray with One Deletion Java Code
```java
public static int maximumSumWithOneDeletion(int[] nums) {
    if (nums == null || nums.length == 0) {
        throw new IllegalArgumentException("Array must not be empty");
    }

    int keep = nums[0];
    int drop = Integer.MIN_VALUE;
    int answer = nums[0];

    for (int i = 1; i < nums.length; i++) {
        int num = nums[i];

        int previousKeep = keep;
        int previousDrop = drop;

        keep = Math.max(num, previousKeep + num);

        drop = Math.max(
                previousKeep,
                previousDrop == Integer.MIN_VALUE
                        ? Integer.MIN_VALUE
                        : previousDrop + num
        );

        answer = Math.max(answer, Math.max(keep, drop));
    }

    return answer;
}
```
## 4 Integer Overflow-Safe Version
```java
public static long maxSubArrayLong(int[] nums) {
    if (nums == null || nums.length == 0) {
        throw new IllegalArgumentException("Array must not be empty");
    }

    long currentBest = nums[0];
    long globalBest = nums[0];

    for (int i = 1; i < nums.length; i++) {
        long value = nums[i];

        currentBest = Math.max(
                value,
                currentBest + value
        );

        globalBest = Math.max(
                globalBest,
                currentBest
        );
    }

    return globalBest;
}
```
# Binary Search

## Template 1 — Exact Target Search
The data is sorted
The target may or may not exist
Any matching index is acceptable
```java
public int binarySearch(int[] nums, int target) {
    int low = 0;
    int high = nums.length - 1;

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (nums[mid] == target) {
            return mid;
        }

        if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }

    return -1;
}
```
## Template 2 — Boundary Search
First occurrence
Last occurrence
Insertion position
First value >= target
First value > target
First valid answer
Last valid answer

### Lower Bound
```java
public int lowerBound(int[] nums, int target) {
    int low = 0;
    int high = nums.length;

    while (low < high) {
        int mid = low + (high - low) / 2;

        if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }

    return low;
}
```
### Upper Bound
```java
public int upperBound(int[] nums, int target) {
    int low = 0;
    int high = nums.length;

    while (low < high) {
        int mid = low + (high - low) / 2;

        if (nums[mid] <= target) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }

    return low;
}
```
### First and Last occurance
```java
public int[] searchRange(int[] nums, int target) {
    int first = lowerBound(nums, target);

    if (first == nums.length || nums[first] != target) {
        return new int[]{-1, -1};
    }

    int last = upperBound(nums, target) - 1;

    return new int[]{first, last};
}

private int lowerBound(int[] nums, int target) {
    int low = 0;
    int high = nums.length;

    while (low < high) {
        int mid = low + (high - low) / 2;

        if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }

    return low;
}

private int upperBound(int[] nums, int target) {
    int low = 0;
    int high = nums.length;

    while (low < high) {
        int mid = low + (high - low) / 2;

        if (nums[mid] <= target) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }

    return low;
}
```
## Template 3 — Binary Search on Answer
Minimum eating speed
Minimum ship capacity
Minimum number of days
Minimum maximum workload
Maximum minimum distance
Maximum possible allocation
Integer square root

### Koko Eating Bananas
Find the minimum eating speed that allows all piles
to be completed within h hours.

### Monotonic Pattern

Slow speed → impossible
Fast speed → possible

F F F F T T T

```java
public int minEatingSpeed(int[] piles, int h) {
    int low = 1;
    int high = 0;

    for (int pile : piles) {
        high = Math.max(high, pile);
    }

    while (low < high) {
        int mid = low + (high - low) / 2;

        if (canFinish(piles, h, mid)) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }

    return low;
}

private boolean canFinish(int[] piles, int h, int speed) {
    long hoursRequired = 0;

    for (int pile : piles) {
        hoursRequired += (pile + speed - 1L) / speed;

        if (hoursRequired > h) {
            return false;
        }
    }

    return true;
}
```
# Priority Queue

## Template 1: Repeated Priority Processing
Insert all candidates
Repeatedly remove minimum or maximum
Process until Heap becomes empty

```java
PriorityQueue<Integer> heap = new PriorityQueue<>();

for (int value : nums) {
    heap.offer(value);
}

while (!heap.isEmpty()) {
    int current = heap.poll();
    // Process current highest-priority item
}
```
### For Maximum-first Processing
```java
PriorityQueue<Integer> heap =
        new PriorityQueue<>(Collections.reverseOrder());
```
Last Stone Weight
Connect Ropes With Minimum Cost
Huffman Coding
Reorganize String
Task Scheduler
Minimum Cost to Hire Workers

## Template 2: Fixed-Size Top K Selection
Need Top K
Need Kth largest/smallest
Need best K from large input
Input may arrive as a stream
```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

for (int value : nums) {
    minHeap.offer(value);

    if (minHeap.size() > k) {
        minHeap.poll();
    }
}
```

## Template 3: Frequency Map + Heap
```java
Map<Integer, Integer> frequency = new HashMap<>();

for (int value : nums) {
    frequency.put(value, frequency.getOrDefault(value, 0) + 1);
}

PriorityQueue<Integer> minHeap =
        new PriorityQueue<>(
                (a, b) -> Integer.compare(
                        frequency.get(a),
                        frequency.get(b)
                )
        );

for (int value : frequency.keySet()) {
    minHeap.offer(value);

    if (minHeap.size() > k) {
        minHeap.poll();
    }
}
```
Top K Frequent Elements
Top K Frequent Words
Sort Characters by Frequency
Reorganize String
Task Scheduler

## Java PriorityQueue Code Templates


## Min-Heap

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

---

## Max-Heap

```java
PriorityQueue<Integer> maxHeap =
        new PriorityQueue<>(Collections.reverseOrder());
```

---

## Custom Object: Lowest Priority Number First

```java
PriorityQueue<Task> tasks =
        new PriorityQueue<>(
                (a, b) -> Integer.compare(a.priority, b.priority)
        );
```

---

## Custom Object: Highest Priority Number First

```java
PriorityQueue<Task> tasks =
        new PriorityQueue<>(
                (a, b) -> Integer.compare(b.priority, a.priority)
        );
```

# Greedy Algorithm

## Template 1 — Sort + Select
Candidates must be chosen in a strategic order.
The original order does not help.
The correct order makes a safe decision obvious.
```java
Arrays.sort(items, comparator);

int answer = 0;
int boundary = initialBoundary;

for (Item item : items) {
    if (isCompatible(item, boundary)) {
        answer++;
        boundary = updateBoundary(item);
    }
}
```
Activity Selection
Non-overlapping Intervals
Minimum Number of Arrows
Maximum Length Chain
Meeting Rooms
Job Sequencing
Fractional Knapsack

## Template 2 — Greedy Boundary / Reachability
The problem asks how far we can reach.
Each element expands or restricts a boundary.
We do not need the exact path, only feasibility or minimum steps.
```java
int farthest = 0;

for (int i = 0; i < nums.length; i++) {
    if (i > farthest) {
        return false;
    }

    farthest = Math.max(farthest, i + nums[i]);
}

return true;
```
Jump Game
Jump Game II
Gas Station
Partition Labels
Candy
Can Place Flowers
Minimum Number of Taps

## Template 3 — Greedy Resource Matching
There are requests and resources.
Each resource can satisfy certain requests.
We want to maximize satisfied requests or minimize waste.
```java
Arrays.sort(requirements);
Arrays.sort(resources);

int request = 0;
int resource = 0;

while (request < requirements.length &&
       resource < resources.length) {

    if (resources[resource] >= requirements[request]) {
        request++;
    }

    resource++;
}

return request;
```
Assign Cookies
Boats to Save People
Minimum Platforms
Task Assignment
Matching workers and jobs

## Template 4 — Greedy + Heap
The best candidate changes dynamically.
Several candidates become available over time.
We repeatedly need the smallest/largest valid candidate.
```Java
Arrays.sort(items, comparator);
PriorityQueue<Integer> heap = new PriorityQueue<>();

for (Item item : items) {
    heap.offer(item.value);

    if (invalidState(heap, item)) {
        heap.poll();
    }
}
```
Course Schedule III
Meeting Rooms II
Task Scheduler
Reorganize String
Minimum Cost to Connect Ropes
IPO
Maximum Performance of a Team

### Problem 1 — Activity Selection
```java
import java.util.Arrays;

public class ActivitySelection {

    public static int maxActivities(int[][] activities) {
        if (activities == null || activities.length == 0) {
            return 0;
        }

        Arrays.sort(
            activities,
            (a, b) -> Integer.compare(a[1], b[1])
        );

        int count = 1;
        int previousEnd = activities[0][1];

        for (int i = 1; i < activities.length; i++) {
            if (activities[i][0] >= previousEnd) {
                count++;
                previousEnd = activities[i][1];
            }
        }

        return count;
    }
}
```