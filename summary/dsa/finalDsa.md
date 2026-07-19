# Introduction
1. [Strategy](#strategy)
2. [Heap](#hashmap)
3. [2-Pointers](#2-pointers)
4. [Sliding Window](#sliding-window)
5. [Prefix Sum](#prefix-sum)
6. [Kadane's Algorithms](#kadanes-algorithms)
7. [Binary Search](#binary-search)
8. [Priority Queue](#priority-queue)
9. [Greedy Algorithm](#greedy-algorithm)

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