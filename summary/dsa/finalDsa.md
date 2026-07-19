# Introduction
1. [Heap](#hashmap)
2. [2-Pointers](#2-pointers)
3. [Sliding Window](#sliding-window)
4. [Prefix Sum](#prefix-sum)
5. [Kadane's Algorithms](#kadanes-algorithms)
6. [Binary Search](#binary-search)
7. [Priority Queue](#priority-queue)
8. [Greedy Algorithm](#greedy-algorithm)

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

## Template 1: Opposite Direction
```java
int left = 0;
int right = nums.length - 1;

while (left < right) {
    if (condition) {
        // answer
    } else if (needToIncrease) {
        left++;
    } else {
        right--;
    }
}
```
Pair sum in sorted array
Palindrome
Container with most water
Reverse

## Templat 2: Slow-Fast for In-Place Filtering

```java
int slow = 0;

for (int fast = 0; fast < nums.length; fast++) {
    if (isValid(nums[fast])) {
        nums[slow] = nums[fast];
        slow++;
    }
}

return slow;
```
Remove element
Move zeroes
Remove duplicates
Partition

## Template 3: Merging Two Sorted Inputs
```java
int i = 0;
int j = 0;

while (i < arr1.length && j < arr2.length) {
    if (arr1[i] <= arr2[j]) {
        i++;
    } else {
        j++;
    }
}
```
Merge sorted arrays
Intersection
Merge sorted linked lists

## Template 4: Linked List Fast-Slow
```java
ListNode slow = head;
ListNode fast = head;

while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
```
Middle node
Cycle detection
Palindrome linked list

## Template 5: Sliding Window
```java
int left = 0;

for (int right = 0; right < nums.length; right++) {
    // add nums[right]

    while (windowInvalid) {
        // remove nums[left]
        left++;
    }

    // update answer
}
```
Longest substring
Minimum subarray
Max window

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

    if (right >= k - 1) {
        // use window
        windowSum -= nums[left];
        left++;
    }
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
        // Add nums[right] to state

        while (/* window is invalid */) {
            // Remove nums[left] from state
            left++;
        }

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

        while (/* window is valid */) {
            minLength = Math.min(
                    minLength,
                    right - left + 1
            );

            // Remove nums[left]
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