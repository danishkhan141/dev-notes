Below are **Top 50 DSA Array Interview Questions** explained in a clean, professional way for Java interview preparation.

For product-based companies, array questions mainly test these patterns:

**Two Pointer, Sliding Window, Prefix Sum, HashMap, Sorting, Binary Search, Kadane’s Algorithm, In-place Manipulation, Matrix Traversal.**

---

# 1. What is an Array?

An **array** is a linear data structure that stores elements of the same type in continuous index positions.

```java
int[] arr = {10, 20, 30, 40};
System.out.println(arr[0]); // 10
```

### Key points

| Point             | Explanation         |
| ----------------- | ------------------- |
| Index starts from | 0                   |
| Access time       | O(1)                |
| Search time       | O(n)                |
| Fixed size        | Yes                 |
| Memory            | Continuous indexing |

---

# 2. Why is array access O(1)?

Because array elements are stored in indexed positions.

For example:

```java
arr[i]
```

Java directly calculates the position of index `i`, so access is constant time.

---

# 3. What is the difference between Array and ArrayList?

| Array                | ArrayList                     |
| -------------------- | ----------------------------- |
| Fixed size           | Dynamic size                  |
| Can store primitives | Stores objects                |
| Faster               | Slightly slower               |
| Example: `int[] arr` | Example: `ArrayList<Integer>` |

```java
int[] arr = new int[5];

ArrayList<Integer> list = new ArrayList<>();
list.add(10);
```

---

# 4. How do you find the largest element in an array?

### Approach

Maintain one variable `max`.

```java
public static int findMax(int[] arr) {
    int max = arr[0];

    for (int num : arr) {
        if (num > max) {
            max = num;
        }
    }

    return max;
}
```

### Example

```java
Input: [10, 5, 30, 20]
Output: 30
```

Time Complexity: **O(n)**
Space Complexity: **O(1)**

---

# 5. How do you find the second largest element?

### Important interview point

Avoid sorting because sorting takes **O(n log n)**. We can do it in **O(n)**.

```java
public static int secondLargest(int[] arr) {
    int largest = Integer.MIN_VALUE;
    int secondLargest = Integer.MIN_VALUE;

    for (int num : arr) {
        if (num > largest) {
            secondLargest = largest;
            largest = num;
        } else if (num > secondLargest && num != largest) {
            secondLargest = num;
        }
    }

    return secondLargest;
}
```

### Example

```java
Input: [10, 20, 4, 45, 99]
Output: 45
```

---

# 6. How do you reverse an array?

Use **two pointer approach**.

```java
public static void reverseArray(int[] arr) {
    int left = 0;
    int right = arr.length - 1;

    while (left < right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;

        left++;
        right--;
    }
}
```

### Example

```java
Input: [1, 2, 3, 4]
Output: [4, 3, 2, 1]
```

Time: **O(n)**
Space: **O(1)**

---

# 7. How do you check if an array is sorted?

```java
public static boolean isSorted(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] < arr[i - 1]) {
            return false;
        }
    }
    return true;
}
```

### Example

```java
[1, 2, 3, 4] -> true
[1, 3, 2, 4] -> false
```

---

# 8. How do you remove duplicates from a sorted array?

This is a classic **two pointer** problem.

```java
public static int removeDuplicates(int[] arr) {
    if (arr.length == 0) return 0;

    int uniqueIndex = 0;

    for (int i = 1; i < arr.length; i++) {
        if (arr[i] != arr[uniqueIndex]) {
            uniqueIndex++;
            arr[uniqueIndex] = arr[i];
        }
    }

    return uniqueIndex + 1;
}
```

### Example

```java
Input: [1, 1, 2, 2, 3]
Output length: 3
Modified array: [1, 2, 3]
```

---

# 9. How do you move all zeroes to the end?

```java
public static void moveZeroes(int[] arr) {
    int insertPosition = 0;

    for (int num : arr) {
        if (num != 0) {
            arr[insertPosition++] = num;
        }
    }

    while (insertPosition < arr.length) {
        arr[insertPosition++] = 0;
    }
}
```

### Example

```java
Input: [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

---

# 10. How do you rotate an array by K positions?

### Example

```java
Input: [1, 2, 3, 4, 5, 6, 7], k = 3
Output: [5, 6, 7, 1, 2, 3, 4]
```

### Approach

Reverse full array, then reverse first `k`, then reverse remaining.

```java
public static void rotate(int[] arr, int k) {
    int n = arr.length;
    k = k % n;

    reverse(arr, 0, n - 1);
    reverse(arr, 0, k - 1);
    reverse(arr, k, n - 1);
}

private static void reverse(int[] arr, int left, int right) {
    while (left < right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
        left++;
        right--;
    }
}
```

Time: **O(n)**
Space: **O(1)**

---

# 11. How do you find the missing number from 1 to N?

### Formula approach

Sum of first N numbers:

```java
N * (N + 1) / 2
```

```java
public static int missingNumber(int[] arr, int n) {
    int expectedSum = n * (n + 1) / 2;
    int actualSum = 0;

    for (int num : arr) {
        actualSum += num;
    }

    return expectedSum - actualSum;
}
```

### Example

```java
Input: [1, 2, 4, 5], n = 5
Output: 3
```

---

# 12. How do you find duplicate elements in an array?

Use `HashSet`.

```java
public static void findDuplicates(int[] arr) {
    Set<Integer> seen = new HashSet<>();

    for (int num : arr) {
        if (seen.contains(num)) {
            System.out.println("Duplicate: " + num);
        } else {
            seen.add(num);
        }
    }
}
```

Time: **O(n)**
Space: **O(n)**

---

# 13. How do you find the frequency of each element?

Use `HashMap`.

```java
public static Map<Integer, Integer> frequency(int[] arr) {
    Map<Integer, Integer> map = new HashMap<>();

    for (int num : arr) {
        map.put(num, map.getOrDefault(num, 0) + 1);
    }

    return map;
}
```

### Example

```java
Input: [1, 2, 2, 3, 3, 3]
Output: {1=1, 2=2, 3=3}
```

---

# 14. What is Two Sum problem?

Given an array and a target, find two numbers whose sum equals target.

### Example

```java
Input: [2, 7, 11, 15], target = 9
Output: [0, 1]
```

### Java solution

```java
public static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {
        int required = target - nums[i];

        if (map.containsKey(required)) {
            return new int[]{map.get(required), i};
        }

        map.put(nums[i], i);
    }

    return new int[]{-1, -1};
}
```

### Logic

For every number, check whether its required partner already exists.

Time: **O(n)**
Space: **O(n)**

---

# 15. What is Kadane’s Algorithm?

Kadane’s Algorithm is used to find the **maximum subarray sum**.

### Example

```java
Input: [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Subarray: [4, -1, 2, 1]
```

### Java code

```java
public static int maxSubArray(int[] arr) {
    int currentSum = arr[0];
    int maxSum = arr[0];

    for (int i = 1; i < arr.length; i++) {
        currentSum = Math.max(arr[i], currentSum + arr[i]);
        maxSum = Math.max(maxSum, currentSum);
    }

    return maxSum;
}
```

### Interview explanation

At every index, decide:

```java
Should I start a new subarray?
Or continue the previous subarray?
```

Time: **O(n)**
Space: **O(1)**

---

# 16. How do you find the minimum subarray sum?

Same concept as Kadane’s Algorithm, but instead of max, use min.

```java
public static int minSubArraySum(int[] arr) {
    int currentSum = arr[0];
    int minSum = arr[0];

    for (int i = 1; i < arr.length; i++) {
        currentSum = Math.min(arr[i], currentSum + arr[i]);
        minSum = Math.min(minSum, currentSum);
    }

    return minSum;
}
```

---

# 17. What is Prefix Sum?

Prefix sum stores cumulative sum.

```java
arr:    [2, 4, 6, 8]
prefix: [2, 6, 12, 20]
```

This helps to find range sum quickly.

### Range sum formula

```java
sum(left, right) = prefix[right] - prefix[left - 1]
```

---

# 18. How do you find subarray sum equals K?

Use prefix sum with HashMap.

```java
public static int subarraySum(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();

    map.put(0, 1);

    int prefixSum = 0;
    int count = 0;

    for (int num : nums) {
        prefixSum += num;

        if (map.containsKey(prefixSum - k)) {
            count += map.get(prefixSum - k);
        }

        map.put(prefixSum, map.getOrDefault(prefixSum, 0) + 1);
    }

    return count;
}
```

### Example

```java
Input: [1, 1, 1], k = 2
Output: 2
```

Subarrays:

```java
[1,1] at index 0-1
[1,1] at index 1-2
```

---

# 19. What is Sliding Window?

Sliding window is used when we deal with **subarrays or substrings**.

Instead of recalculating again and again, we maintain a window.

Used in:

```java
maximum sum subarray of size k
longest subarray
minimum window
consecutive elements
```

---

# 20. Maximum sum subarray of size K

```java
public static int maxSumSubarrayOfSizeK(int[] arr, int k) {
    int windowSum = 0;
    int maxSum = Integer.MIN_VALUE;

    for (int i = 0; i < arr.length; i++) {
        windowSum += arr[i];

        if (i >= k - 1) {
            maxSum = Math.max(maxSum, windowSum);
            windowSum -= arr[i - k + 1];
        }
    }

    return maxSum;
}
```

### Example

```java
Input: [2, 1, 5, 1, 3, 2], k = 3
Output: 9
Subarray: [5, 1, 3]
```

---

# 21. Longest subarray with sum K

For positive numbers, use sliding window.

```java
public static int longestSubarraySumK(int[] arr, int k) {
    int left = 0;
    int sum = 0;
    int maxLength = 0;

    for (int right = 0; right < arr.length; right++) {
        sum += arr[right];

        while (sum > k) {
            sum -= arr[left];
            left++;
        }

        if (sum == k) {
            maxLength = Math.max(maxLength, right - left + 1);
        }
    }

    return maxLength;
}
```

### Note

If array contains negative numbers, use prefix sum + HashMap.

---

# 22. How do you merge two sorted arrays?

```java
public static int[] mergeSortedArrays(int[] a, int[] b) {
    int i = 0, j = 0, k = 0;
    int[] result = new int[a.length + b.length];

    while (i < a.length && j < b.length) {
        if (a[i] <= b[j]) {
            result[k++] = a[i++];
        } else {
            result[k++] = b[j++];
        }
    }

    while (i < a.length) result[k++] = a[i++];
    while (j < b.length) result[k++] = b[j++];

    return result;
}
```

---

# 23. How do you sort an array of 0s, 1s and 2s?

This is called **Dutch National Flag Algorithm**.

```java
public static void sortColors(int[] arr) {
    int low = 0;
    int mid = 0;
    int high = arr.length - 1;

    while (mid <= high) {
        if (arr[mid] == 0) {
            swap(arr, low, mid);
            low++;
            mid++;
        } else if (arr[mid] == 1) {
            mid++;
        } else {
            swap(arr, mid, high);
            high--;
        }
    }
}

private static void swap(int[] arr, int i, int j) {
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```

### Example

```java
Input: [2, 0, 2, 1, 1, 0]
Output: [0, 0, 1, 1, 2, 2]
```

Time: **O(n)**
Space: **O(1)**

---

# 24. What is Binary Search?

Binary Search is used on a **sorted array**.

```java
public static int binarySearch(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1;
}
```

Time: **O(log n)**

---

# 25. Why do we write this?

```java
int mid = left + (right - left) / 2;
```

Instead of:

```java
int mid = (left + right) / 2;
```

Because `left + right` may cause integer overflow for very large indexes.

---

# 26. How do you find first and last occurrence of an element?

Example:

```java
Input: [1, 2, 2, 2, 3, 4], target = 2
Output: first = 1, last = 3
```

Use modified binary search.

```java
public static int firstOccurrence(int[] arr, int target) {
    int left = 0, right = arr.length - 1;
    int answer = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            answer = mid;
            right = mid - 1;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return answer;
}
```

For last occurrence, when target is found:

```java
answer = mid;
left = mid + 1;
```

---

# 27. How do you search in a rotated sorted array?

Example:

```java
Input: [4,5,6,7,0,1,2], target = 0
Output: 4
```

```java
public static int searchRotated(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) return mid;

        if (arr[left] <= arr[mid]) {
            if (target >= arr[left] && target < arr[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            if (target > arr[mid] && target <= arr[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }

    return -1;
}
```

### Logic

At least one half of the array will always be sorted.

---

# 28. How do you find peak element?

A peak element is greater than its neighbours.

```java
public static int findPeakElement(int[] arr) {
    int left = 0;
    int right = arr.length - 1;

    while (left < right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] < arr[mid + 1]) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }

    return left;
}
```

Time: **O(log n)**

---

# 29. What is Majority Element?

An element appearing more than `n/2` times.

Use **Boyer-Moore Voting Algorithm**.

```java
public static int majorityElement(int[] arr) {
    int candidate = 0;
    int count = 0;

    for (int num : arr) {
        if (count == 0) {
            candidate = num;
        }

        if (num == candidate) {
            count++;
        } else {
            count--;
        }
    }

    return candidate;
}
```

### Example

```java
Input: [2, 2, 1, 1, 1, 2, 2]
Output: 2
```

---

# 30. How do you find leaders in an array?

An element is a leader if all elements on its right side are smaller.

### Example

```java
Input: [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]
```

### Approach

Traverse from right to left.

```java
public static List<Integer> leaders(int[] arr) {
    List<Integer> result = new ArrayList<>();
    int maxFromRight = arr[arr.length - 1];

    result.add(maxFromRight);

    for (int i = arr.length - 2; i >= 0; i--) {
        if (arr[i] > maxFromRight) {
            result.add(arr[i]);
            maxFromRight = arr[i];
        }
    }

    Collections.reverse(result);
    return result;
}
```

---

# 31. How do you find equilibrium index?

Equilibrium index means:

```java
left sum == right sum
```

### Example

```java
Input: [-7, 1, 5, 2, -4, 3, 0]
Output: 3
```

At index 3:

```java
Left sum = -7 + 1 + 5 = -1
Right sum = -4 + 3 + 0 = -1
```

```java
public static int equilibriumIndex(int[] arr) {
    int totalSum = 0;

    for (int num : arr) {
        totalSum += num;
    }

    int leftSum = 0;

    for (int i = 0; i < arr.length; i++) {
        int rightSum = totalSum - leftSum - arr[i];

        if (leftSum == rightSum) {
            return i;
        }

        leftSum += arr[i];
    }

    return -1;
}
```

---

# 32. How do you find product of array except self?

Do not use division.

### Example

```java
Input: [1, 2, 3, 4]
Output: [24, 12, 8, 6]
```

```java
public static int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];

    result[0] = 1;

    for (int i = 1; i < n; i++) {
        result[i] = result[i - 1] * nums[i - 1];
    }

    int rightProduct = 1;

    for (int i = n - 1; i >= 0; i--) {
        result[i] = result[i] * rightProduct;
        rightProduct *= nums[i];
    }

    return result;
}
```

Time: **O(n)**
Space: **O(1)** excluding output array.

---

# 33. How do you find maximum profit from stock prices?

Buy once and sell once.

### Example

```java
Input: [7, 1, 5, 3, 6, 4]
Output: 5
```

Buy at `1`, sell at `6`.

```java
public static int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;

    for (int price : prices) {
        minPrice = Math.min(minPrice, price);
        maxProfit = Math.max(maxProfit, price - minPrice);
    }

    return maxProfit;
}
```

---

# 34. How do you find trapped rain water?

### Example

```java
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

### Two pointer solution

```java
public static int trap(int[] height) {
    int left = 0;
    int right = height.length - 1;

    int leftMax = 0;
    int rightMax = 0;
    int water = 0;

    while (left < right) {
        if (height[left] < height[right]) {
            if (height[left] >= leftMax) {
                leftMax = height[left];
            } else {
                water += leftMax - height[left];
            }
            left++;
        } else {
            if (height[right] >= rightMax) {
                rightMax = height[right];
            } else {
                water += rightMax - height[right];
            }
            right--;
        }
    }

    return water;
}
```

Time: **O(n)**
Space: **O(1)**

---

# 35. How do you solve Container With Most Water?

### Example

```java
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```

```java
public static int maxArea(int[] height) {
    int left = 0;
    int right = height.length - 1;
    int maxArea = 0;

    while (left < right) {
        int width = right - left;
        int minHeight = Math.min(height[left], height[right]);

        maxArea = Math.max(maxArea, width * minHeight);

        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }

    return maxArea;
}
```

### Interview logic

Move the pointer having smaller height because area depends on the smaller boundary.

---

# 36. How do you find maximum consecutive ones?

```java
public static int findMaxConsecutiveOnes(int[] nums) {
    int count = 0;
    int maxCount = 0;

    for (int num : nums) {
        if (num == 1) {
            count++;
            maxCount = Math.max(maxCount, count);
        } else {
            count = 0;
        }
    }

    return maxCount;
}
```

### Example

```java
Input: [1,1,0,1,1,1]
Output: 3
```

---

# 37. How do you find longest consecutive sequence?

### Example

```java
Input: [100, 4, 200, 1, 3, 2]
Output: 4
Sequence: [1, 2, 3, 4]
```

```java
public static int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();

    for (int num : nums) {
        set.add(num);
    }

    int longest = 0;

    for (int num : set) {
        if (!set.contains(num - 1)) {
            int current = num;
            int streak = 1;

            while (set.contains(current + 1)) {
                current++;
                streak++;
            }

            longest = Math.max(longest, streak);
        }
    }

    return longest;
}
```

Time: **O(n)** average.

---

# 38. How do you merge overlapping intervals?

### Example

```java
Input: [[1,3], [2,6], [8,10], [15,18]]
Output: [[1,6], [8,10], [15,18]]
```

```java
public static int[][] mergeIntervals(int[][] intervals) {
    Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

    List<int[]> result = new ArrayList<>();

    for (int[] interval : intervals) {
        if (result.isEmpty() || result.get(result.size() - 1)[1] < interval[0]) {
            result.add(interval);
        } else {
            result.get(result.size() - 1)[1] =
                    Math.max(result.get(result.size() - 1)[1], interval[1]);
        }
    }

    return result.toArray(new int[result.size()][]);
}
```

---

# 39. How do you find next permutation?

Next permutation means next lexicographically greater arrangement.

### Example

```java
Input: [1, 2, 3]
Output: [1, 3, 2]
```

```java
public static void nextPermutation(int[] nums) {
    int i = nums.length - 2;

    while (i >= 0 && nums[i] >= nums[i + 1]) {
        i--;
    }

    if (i >= 0) {
        int j = nums.length - 1;

        while (nums[j] <= nums[i]) {
            j--;
        }

        swap(nums, i, j);
    }

    reverse(nums, i + 1, nums.length - 1);
}

private static void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}

private static void reverse(int[] nums, int left, int right) {
    while (left < right) {
        swap(nums, left++, right--);
    }
}
```

---

# 40. How do you find maximum product subarray?

Important because negative numbers can change minimum to maximum.

```java
public static int maxProduct(int[] nums) {
    int maxProduct = nums[0];
    int currentMax = nums[0];
    int currentMin = nums[0];

    for (int i = 1; i < nums.length; i++) {
        int num = nums[i];

        if (num < 0) {
            int temp = currentMax;
            currentMax = currentMin;
            currentMin = temp;
        }

        currentMax = Math.max(num, currentMax * num);
        currentMin = Math.min(num, currentMin * num);

        maxProduct = Math.max(maxProduct, currentMax);
    }

    return maxProduct;
}
```

### Example

```java
Input: [2, 3, -2, 4]
Output: 6
```

---

# 41. How do you find pair with given sum in sorted array?

Use two pointers.

```java
public static boolean pairSumSorted(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;

    while (left < right) {
        int sum = arr[left] + arr[right];

        if (sum == target) return true;
        else if (sum < target) left++;
        else right--;
    }

    return false;
}
```

---

# 42. How do you find triplet sum equal to zero?

This is the famous **3Sum problem**.

### Approach

Sort array, then fix one element and use two pointers.

```java
public static List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> result = new ArrayList<>();

    for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue;

        int left = i + 1;
        int right = nums.length - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];

            if (sum == 0) {
                result.add(Arrays.asList(nums[i], nums[left], nums[right]));

                while (left < right && nums[left] == nums[left + 1]) left++;
                while (left < right && nums[right] == nums[right - 1]) right--;

                left++;
                right--;
            } else if (sum < 0) {
                left++;
            } else {
                right--;
            }
        }
    }

    return result;
}
```

Time: **O(n²)**

---

# 43. How do you find intersection of two arrays?

```java
public static int[] intersection(int[] nums1, int[] nums2) {
    Set<Integer> set1 = new HashSet<>();
    Set<Integer> resultSet = new HashSet<>();

    for (int num : nums1) {
        set1.add(num);
    }

    for (int num : nums2) {
        if (set1.contains(num)) {
            resultSet.add(num);
        }
    }

    int[] result = new int[resultSet.size()];
    int i = 0;

    for (int num : resultSet) {
        result[i++] = num;
    }

    return result;
}
```

---

# 44. How do you find union of two arrays?

Use `HashSet`.

```java
public static Set<Integer> union(int[] a, int[] b) {
    Set<Integer> set = new HashSet<>();

    for (int num : a) set.add(num);
    for (int num : b) set.add(num);

    return set;
}
```

---

# 45. How do you find common elements in three sorted arrays?

Use three pointers.

```java
public static List<Integer> commonElements(int[] a, int[] b, int[] c) {
    int i = 0, j = 0, k = 0;
    List<Integer> result = new ArrayList<>();

    while (i < a.length && j < b.length && k < c.length) {
        if (a[i] == b[j] && b[j] == c[k]) {
            result.add(a[i]);
            i++;
            j++;
            k++;
        } else if (a[i] < b[j]) {
            i++;
        } else if (b[j] < c[k]) {
            j++;
        } else {
            k++;
        }
    }

    return result;
}
```

---

# 46. How do you find minimum number of jumps to reach end?

### Example

```java
Input: [2, 3, 1, 1, 4]
Output: 2
```

Jump from index `0 -> 1 -> 4`.

```java
public static int minJumps(int[] nums) {
    int jumps = 0;
    int currentEnd = 0;
    int farthest = 0;

    for (int i = 0; i < nums.length - 1; i++) {
        farthest = Math.max(farthest, i + nums[i]);

        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
        }
    }

    return jumps;
}
```

---

# 47. How do you check if you can reach the last index?

This is **Jump Game**.

```java
public static boolean canJump(int[] nums) {
    int reachable = 0;

    for (int i = 0; i < nums.length; i++) {
        if (i > reachable) {
            return false;
        }

        reachable = Math.max(reachable, i + nums[i]);
    }

    return true;
}
```

### Example

```java
[2,3,1,1,4] -> true
[3,2,1,0,4] -> false
```

---

# 48. How do you find maximum in every window of size K?

Use deque.

```java
public static int[] maxSlidingWindow(int[] nums, int k) {
    if (nums.length == 0) return new int[0];

    Deque<Integer> deque = new LinkedList<>();
    int[] result = new int[nums.length - k + 1];
    int index = 0;

    for (int i = 0; i < nums.length; i++) {
        while (!deque.isEmpty() && deque.peekFirst() <= i - k) {
            deque.pollFirst();
        }

        while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
            deque.pollLast();
        }

        deque.offerLast(i);

        if (i >= k - 1) {
            result[index++] = nums[deque.peekFirst()];
        }
    }

    return result;
}
```

### Example

```java
Input: [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

---

# 49. How do you rotate a matrix by 90 degrees?

For `n x n` matrix.

### Approach

1. Transpose matrix.
2. Reverse every row.

```java
public static void rotateMatrix(int[][] matrix) {
    int n = matrix.length;

    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }

    for (int i = 0; i < n; i++) {
        reverseRow(matrix[i]);
    }
}

private static void reverseRow(int[] row) {
    int left = 0;
    int right = row.length - 1;

    while (left < right) {
        int temp = row[left];
        row[left] = row[right];
        row[right] = temp;

        left++;
        right--;
    }
}
```

---

# 50. How do you print matrix in spiral order?

```java
public static List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> result = new ArrayList<>();

    int top = 0;
    int bottom = matrix.length - 1;
    int left = 0;
    int right = matrix[0].length - 1;

    while (top <= bottom && left <= right) {
        for (int i = left; i <= right; i++) {
            result.add(matrix[top][i]);
        }
        top++;

        for (int i = top; i <= bottom; i++) {
            result.add(matrix[i][right]);
        }
        right--;

        if (top <= bottom) {
            for (int i = right; i >= left; i--) {
                result.add(matrix[bottom][i]);
            }
            bottom--;
        }

        if (left <= right) {
            for (int i = bottom; i >= top; i--) {
                result.add(matrix[i][left]);
            }
            left++;
        }
    }

    return result;
}
```

---

# Most Important Array Patterns for Interview

| Pattern        | Problems                                               |
| -------------- | ------------------------------------------------------ |
| Two Pointer    | Reverse array, pair sum, container water, 3Sum         |
| Sliding Window | Max sum size K, longest subarray, max consecutive ones |
| Prefix Sum     | Subarray sum K, equilibrium index, range sum           |
| HashMap        | Two Sum, frequency, duplicates, subarray sum K         |
| Binary Search  | Search element, first/last occurrence, rotated array   |
| Sorting        | Merge intervals, 3Sum, sort colors                     |
| Greedy         | Jump game, stock buy sell                              |
| Kadane         | Maximum subarray, maximum product subarray             |
| Matrix         | Rotate matrix, spiral traversal                        |

---

# Interview Answer Template

Whenever interviewer gives an array problem, explain like this:

```text
1. First I will understand whether array is sorted or unsorted.
2. Then I will check if brute force is acceptable.
3. If not, I will look for a better pattern:
   - Two pointers if sorted or pair-based.
   - Sliding window if subarray/window is involved.
   - Prefix sum if range sum/subarray sum is involved.
   - HashMap if frequency or quick lookup is needed.
   - Binary search if sorted/search space is involved.
4. Then I will explain time and space complexity.
```

---

# Must-Practice Array Problems in Priority Order

For interviews, practice these first:

```text
1. Two Sum
2. Best Time to Buy and Sell Stock
3. Maximum Subarray Sum
4. Move Zeroes
5. Rotate Array
6. Remove Duplicates from Sorted Array
7. Sort 0s, 1s and 2s
8. Product of Array Except Self
9. Subarray Sum Equals K
10. Search in Rotated Sorted Array
11. Merge Intervals
12. 3Sum
13. Container With Most Water
14. Trapping Rain Water
15. Sliding Window Maximum
```

Mastering these will make most array problems easier because many problems are just variations of these patterns.
