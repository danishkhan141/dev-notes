# Key Points:
1. currentSum = Math.max(nums[i], currentSum + nums[i]) =
    if (nums[i] > currentSum + nums[i]) {
    currentSum = nums[i];
} else {
    currentSum = currentSum + nums[i];
}
2. 

# Techniques:
Greedy, prefix+suffix, HashMap, 2-Pointer, Floyd's, Kadane's, Binary Search, Sliding Window, Sorting Algorithms, Searching Algorithms
---
1. HashMap, 2 sum
2. Prefix Minimum + Greedy
3. Floyd's Tortoise and Hari Algorithm
4. prefix product + suffix product
5. Kadane's Algorithm
6. Modified Kadane’s Algorithm (Tracking Max & Min Product) --Greedy
7. Binary Search on Rotated Sorted Array (also called Pivot-based Binary Search)
8. Modified Binary Search on Rotated Sorted Array
9. 3 Sum Sorting + 2 Pointer Technique --Two Pointer / Hashing pattern extension
10. 2 Pointer Technique
11. Factor Counting / Mathematical Observation (Counting Factors of 5)
12. 2 Pointer Technique with LeftMax & RightMax (Alternate: Prefix Max / Suffix Max)
13. Sorting + Sliding Window (Greedy)
14. Interval Merging (Greedy / Linear Scan)
15. Sorting + Greedy Interval Merging
16. Greedy Interval Scheduling (sort by end time)

# Issues:
1. Constaint change karne changes.
2. Related problems.
3. Loop change kar skte hai kya.
4. Understand the concept of time and space complexity.
5. Key point note karna array created etc.
6. Different techniques ki understanding and their TC and SC if variations happen and if 2 techniques merge then TC and SC.
7. Discussion should be done on study and learn mode.

# Greedy : We select Something Permanently
1. Best Time to Buy and Sell Stock(2)
2. Maximum Product Subarray(6)
3. Chocolate Distribution Problem(13)
4. Insert Interval(14)
5. Merge Interval(15)
6. Non-overlapping Interval(16)

# 2 Pointer : We eliminate Possibility
1. Container With Most Water(10)
2. 3 Sum(9)
3. Trapping Rain Water(12)

# Index:
1. [LC1: 2 Sum Unsorted Array](#lc1-unsorted)  
1.1 [LC1: 2 Sum Sorted Array](#lc1sorted)  
1.2 [LC1: 2 Sum Count pairs whose sum is target](#lc1-count)  
1.3 [LC1: 3 Sum](#lc1-3-sum)  
1.4 [Note](#lc1-note)
2. [LC2: Best Time to Buy and Sell Stock](#lc2)
3. [LC3: Find the Duplicate Number](#lc3)  
3.1 [LC3: Find Duplicate Number special case](#lc3-new-version)
4. [LC4: Product of Array Except Self](#lc4)
5. [LC5: Given an integer array nums, find the subarray with the largest sum, and return its sum](#lc5)
6. [LC6: Maximum Product Subarray](#lc6)
7. [LC7: Find Minimum in Rotated Sorted Array](#lc7)
8. [LC8: Search in Rotated Sorted Array](#lc8)
10. [LC10: Container With Most Water](#lc10)
11. [LC11: Factorial Trailing Zeroes](#lc11)
12. [LC12: Trapping Rain Water](#lc12)
13. [LC13: Chocolate Distribution Problem](#lc13)
14. [LC14: Insert Interval](#lc14)
15. [LC15: Merge Interval](#lc15)
16. [LC16: Non-overlapping Intervals](#lc16)

# Doubts:
1. [Merge vs Insert vs Non-Overlapping](#doubt1)


# LC1 (Unsorted)
```
2 Sum
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.
You may assume that each input would have exactly one solution, and you may not use the same element twice.
You can return the answer in any order.
Example 1:
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
Example 2:
Input: nums = [3,2,4], target = 6
Output: [1,2]
Example 3:
Input: nums = [3,3], target = 6
Output: [0,1]

- two sum (unsorted)
- HashMap
- Only one valid answer exists
- TC:O(n), SC:O(n)
```
---
```java
public int[] twoSum(int[] nums, target){
	Map(Integer, Integer) map = new HashMap<>();
	
	for(int i=0; i<nums.size; i++)
	{
		int need = target - nums[i];
		
		if(map.containsKey(need))
		{
			int[] result = new int[]{map.get(need), i};
                System.out.println(Arrays.toString(result));
                return result;
		}
		map.put(nums[i],i);
	}
	return new int[] {};
}
// new int[]{...} → creates array with values
// int[] result = new int[]{map.get(need), i};==map.get(need) → index of the first number, i=index of current number
// new int[] {} → creates empty array===returns empty int[] bc method returns int[] 

```

# LC1(Sorted)
```Java
public static int[] twoSum(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            return new int[] { left, right };
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    throw new IllegalArgumentException("No two sum solution");
}
```
```
1. Initialize pointers: left starts at index 0, right at the last index.
2. Calculate sum: Check the sum of elements at left and right.
3. Adjust pointers:
- If sum equals target, return the indices.
- If sum is less than target, increment left (need a larger number).
- If sum is greater than target, decrement right (need a smaller number).
4. Continue until pointers meet: If no solution is found, throw an exception.
This approach is O(n) time and O(1) space, efficient for sorted arrays. The test output confirms it works: for array [2, 7, 11, 15] and target 13, it returns indices [0, 2] (2 + 11 = 13).
```

# LC1 (Count)
Count all pairs whose sum is target
```java
public static int countTwoSumPairs(int[] nums, int target) {
    java.util.Map<Integer, Integer> frequency = new java.util.HashMap<>();
    int count = 0;
    for (int num : nums) {
        int complement = target - num;
        if (frequency.containsKey(complement)) {
            count += frequency.get(complement);
        }
        frequency.put(num, frequency.getOrDefault(num, 0) + 1);
    }
    return count;
}
```
```
// frequency.put(num, frequency.getOrDefault(num, 0) + 1);
frequency.getOrDefault(num, 0) reads the current count for num
If num is not yet in the map, it returns 0
Then it adds 1
Finally frequency.put(num, ...) stores the new count back into the map
```

# LC1 (All Pairs-values based)
```Java
public static List<int[]> twoSumAllPairs(int[] nums, int target) {
    Set<String> seen = new HashSet<>();
    List<int[]> result = new ArrayList<>();
    Map<Integer, Integer> map = new HashMap<>();
    
    for (int num : nums) {
        int complement = target - num;
        if (map.containsKey(complement)) {
            int[] pair = {complement, num};
            String pairStr = Arrays.toString(pair);
            if (!seen.contains(pairStr)) {
                result.add(pair);
                seen.add(pairStr);
            }
        }
        map.put(num, map.getOrDefault(num, 0) + 1);
    }
    return result;
}
```
```
KeyPoints:
Uses a HashSet to track seen pairs (avoids duplicates)
Returns List<int[]> - list of all valid pairs
Uses HashMap for O(1) complement lookup
Time: O(n), Space: O(n)
```

# LC1 (All Pairs-Index Based)
```Java
public static List<int[]> twoSumAllPairs(int[] nums, int target) {
    Set<String> seen = new HashSet<>();
    List<int[]> result = new ArrayList<>();
    Map<Integer, Integer> map = new HashMap<>();
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            int[] pair = {map.get(complement), i};
            String pairStr = Arrays.toString(pair);
            if (!seen.contains(pairStr)) {
                result.add(pair);
                seen.add(pairStr);
            }
        }
        map.put(nums[i], i);
    }
    return result;
}
```
```
//map.put(nums[i], i);
Stores the index of each value
Overwrites if same value appears multiple times (keeps last index)
```

# LC1 (3 Sum) 
```
3Sum
Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.
Notice that the solution set must not contain duplicate triplets.
Example 1:
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation: 
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.
The distinct triplets are [-1,0,1] and [-1,-1,2].
Notice that the order of the output and the order of the triplets does not matter.
Example 2:
Input: nums = [0,1,1]
Output: []
Explanation: The only possible triplet does not sum up to 0.
Example 3:
Input: nums = [0,0,0]
Output: [[0,0,0]]
Explanation: The only possible triplet sums up to 0.

- 3Sum Sorting + Two Pointer Technique 
- Category: Two Pointers / Hashing pattern extension
- solution set must not contain duplicate triplets
- TC:?
```
```java
public List<List<Integer>> threeSum(int[] nums) {

        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);

        for (int i = 0; i < nums.length - 2; i++) {

            // skip duplicate first element
            if (i > 0 && nums[i] == nums[i - 1]) continue;

            int left = i + 1;
            int right = nums.length - 1;

            while (left < right) {

                int sum = nums[i] + nums[left] + nums[right];

                if (sum == 0) {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));

                    // skip duplicates
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;

                    left++;
                    right--;
                }
                else if (sum < 0) {
                    left++;
                }
                else {
                    right--;
                }
            }
        }
        return result;
    }
}
```
# LC1 Note
```
1. In case of 3 Sum we are adding index in Arrays.asList because it could be multiple subarray and if it is only 1 then we can use as new int[] {} just like in 2 sum we done.
2. 2 Sum multiple array index based and value based we have only difference when we are adding into the map.
3. getOrDefault is used for count scenarios.
```
```
Here are the key points and logic to remember from our discussion:

## 1. **Two Sum Variations**
- **Unsorted**: HashMap (O(n) time, O(n) space) - lookup complement in O(1)
- **Sorted**: Two pointers (O(n) time, O(1) space) - move from ends based on sum
- **Count pairs**: Frequency map - accumulate counts, don't return pairs
- **All pairs**: HashMap + Set for deduplication - return all unique pairs

## 2. **HashMap Storage Logic**
- **For indices**: `map.put(nums[i], i)` - overwrites, keeps last index
- **For frequencies**: `map.put(num, map.getOrDefault(num, 0) + 1)` - accumulates counts
- **Critical**: This single line change switches between counting and indexing

## 3. **Three Sum Logic**
- Sort first (O(n log n))
- Fix one element, use two pointers for remaining
- Skip duplicates: `if (i > 0 && nums[i] == nums[i-1]) continue`
- Inner loop: skip adjacent duplicates after finding triplet

## 4. **Return Types**
- **Single pair**: `int[]` (indices or values)
- **Multiple pairs**: `List<int[]>` or `List<List<Integer>>`
- **Consistency**: Match problem expectations (LeetCode usually wants Lists)

## 5. **Deduplication**
- Use `Set<String>` with `Arrays.toString(pair)` for pair uniqueness
- For sorted approaches: skip adjacent duplicates in loops
- Prevents returning same pair multiple times

## 6. **Code Style**
- Imports at top: `import java.util.*;`
- Avoid inline fully-qualified names
- Makes code cleaner and more readable

## 7. **Edge Cases**
- Empty arrays, single elements
- No solution: throw exception or return empty
- Duplicates: handle carefully based on problem requirements

## 8. **Time/Space Tradeoffs**
- HashMap: O(n) space, O(1) lookups
- Two pointers: O(1) space, O(n) time
- Sorting: O(n log n) time, enables two pointers

These patterns cover most array sum problems. Practice switching between them based on sorted/unsorted input and required output format.
```

# LC2 
```
Best Time to Buy and Sell Stock
You are given an array prices where prices[i] is the price of a given stock on the ith day.
You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.
Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0. 
Example 1:
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
Example 2:
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.

- Best Time to Buy and Sell Stock
- (Single Pass – O(n)) : Greedy
- Prefix Minimum + Greedy
- TC:O(n) SC:O(1)
```
```java
public int maxProfit(int[] prices)
{
	int minPrice = Integer.MAX_VALUE;
	int MaxProfit = 0;
	
	for(int price : prices)
	{
		if(price < minPrice)
		{
			minPrice = price;
		} else 
			{
				maxProfit = Math.max(MaxProfit, price - minPrice);
			}
	}
	return maxProfit;
}
```

# LC3 
```
Find the Duplicate Number
Given an array of integers nums containing n + 1 integers where each integer is in the range [1, n] inclusive.
There is only one repeated number in nums, return this repeated number.
You must solve the problem without modifying the array nums and using only constant extra space.
Example 1:
Input: nums = [1,3,4,2,2]
Output: 2
Example 2:
Input: nums = [3,1,3,4,2]
Output: 3
Example 3:
Input: nums = [3,3,3,3,3]
Output: 3 

- Find the Duplicate Number
- Cycle Detection Algorithm
- Floyd's Tortoise and Hari Algorithm
- Array modify nahi karna
- Extra space nhi
- TC:O(n) SC:O(1)
```
```java
public int findDuplicate(int[] nums)
{
	int slow = nums[0];
	int fast = nums[0];
	
	do
	{
		slow = nums[slow];
		fast = nums[nums[fast]];
	}while(slow != fast;
	
	slow = nums[0];
	while(slow != fast)
	{
		slow = nums[slow];
		fast = nums[fast];
	}
	return slow;
}
```
# LC3 (New Version)
```
If extra space constraint not there in find duplicates
```
```Java
public int findDuplicate(int[] nums) {
    Map<Integer, Boolean> seen = new HashMap<>();
    for (int num : nums) {
        if (seen.containsKey(num)) {
            return num;
        }
        seen.put(num, true);
    }
    return -1;
}
```

# LC4 
```
Product of Array Except Self
Given an integer array nums, return an array answer such that answer[i] is equal to the product of all the elements of nums except nums[i].
The product of any prefix or suffix of nums is guaranteed to fit in a 32-bit integer.
You must write an algorithm that runs in O(n) time and without using the division operation.
Example 1:
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
Example 2:
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]

- Product of Array Except Self
- prefix product + suffix product
- Division allowed nahi
- TC:O(n) SC:O(1)
```
```java
public int[] productExceptSelf(int[] nums)
{
	int n = nums.length;
	int[] answer = new int[n];
	
	answer[0]=1;
	for(int i=1; i<n; i++)
	{
		answer[i] = answer[i-1] * nums[i-1]; //answer is product of index ke left se nums ki values ka multiple
	}
	
	int rightProduct = 1;
	for(i=n-1; i>=0; i--)
	{
		answer[i] = answer[i] * rightProduct; // answer ke index right side nums ki values ka multiple-nothing but rightProduct
		rightProduct *= nums[i]; // rightProduct nums ki values ka multiple hai in reverse order
        // till here ultimately first nums ke left side se multiply karte hai aur answer[i] ban gaya fir uske right side ke multiple karke rightproduct ban gaya 
        // aur fir usko answer[i]-First loop wale left side products se multiple kar diya
	}
	return answer;
}
```         		   
# LC5 
```
Given an integer array nums, find the subarray with the largest sum, and return its sum.
Example 1:
Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: The subarray [4,-1,2,1] has the largest sum 6.
Example 2:
Input: nums = [1]
Output: 1
Explanation: The subarray [1] has the largest sum 1.
Example 3:
Input: nums = [5,4,-1,7,8]
Output: 23
Explanation: The subarray [5,4,-1,7,8] has the largest sum 23.

- Given an integer array nums, find the subarray with the largest sum, and return its sum
- Maximum SubArray
- Kadane's Algorithm
- Greedy + Dynamic Programming
- TC:O(n) SC:O(1)
```
```java
public int maxSubArray(int[] nums){
        int currSum = nums[0];
        int maxSum = nums[0];

        for (int i = 1; i < nums.length; i++) {
            currSum = Math.max(nums[i], currSum + nums[i]);
            maxSum = Math.max(maxSum, currSum);
        }
        return maxSum;
    }
}
```
# maxSum && respective subArray
```
- (Sum + Subarray)
```
```java
public int maxSubArray(int[] nums) {

        int currSum = nums[0];
        int maxSum = nums[0];

        int start = 0, end = 0;
        int tempStart = 0;

        for (int i = 1; i < nums.length; i++) {

            if (nums[i] > currSum + nums[i]) { //means currSum become negative so reset it
                currSum = nums[i];
                tempStart = i;   // new subarray starts
            } else {
                currSum += nums[i];
            }

            if (currSum > maxSum) {
                maxSum = currSum;
                start = tempStart;
                end = i;
            }
        }

        // Printing subarray (optional)
        System.out.println("Max Sum = " + maxSum);
        System.out.print("Subarray = ");
        for (int i = start; i <= end; i++) {
            System.out.print(nums[i] + " ");
        }

        return maxSum;
    }
}
```

# LC6 
```
Maximum Product Subarray
Given an integer array nums, find a subarray that has the largest product, and return the product.
The test cases are generated so that the answer will fit in a 32-bit integer.
Note that the product of an array with a single element is the value of that element.
Example 1:
Input: nums = [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
Example 2:
Input: nums = [-2,0,-1]
Output: 0
Explanation: The result cannot be 2, because [-2,-1] is not a subarray. 

- Maximum Product Subarray
- Modified Kadane’s Algorithm (Tracking Max & Min Product) 
- Category: Dynamic Programming / Greedy hybrid]
```
```java
public int maxProduct(int[] nums){

        int max = nums[0];
        int min = nums[0];
        int result = nums[0];

        for (int i = 1; i < nums.length; i++) {

            int curr = nums[i];

            // tempMax ko min ke pahle nikalna hai aur 
            int tempMax = Math.max(curr,
                            Math.max(max * curr, min * curr));

            // Negative * Negative = Positive
            // Kabhi kabhi negative maximum ban jata hai so needs to track it
            min = Math.min(curr,
                    Math.min(max * curr, min * curr));

            max = tempMax;

            result = Math.max(result, max);
        }

        return result;
    }
}
```

# LC7 
Find Minimum in Rotated Sorted Array 
Suppose an array of length n sorted in ascending order is rotated between 1 and n times. For example, the array nums = [0,1,2,4,5,6,7] might become:
[4,5,6,7,0,1,2] if it was rotated 4 times.
[0,1,2,4,5,6,7] if it was rotated 7 times.
Notice that rotating an array [a[0], a[1], a[2], ..., a[n-1]] 1 time results in the array [a[n-1], a[0], a[1], a[2], ..., a[n-2]].
Given the sorted rotated array nums of unique elements, return the minimum element of this array.
You must write an algorithm that runs in O(log n) time.

Example 1:
Input: nums = [3,4,5,1,2]
Output: 1
Explanation: The original array was [1,2,3,4,5] rotated 3 times.
Example 2:
Input: nums = [4,5,6,7,0,1,2]
Output: 0
Explanation: The original array was [0,1,2,4,5,6,7] and it was rotated 4 times.
Example 3:
Input: nums = [11,13,15,17]
Output: 11
Explanation: The original array was [11,13,15,17] and it was rotated 4 times.

- Find Minimum in Rotated Sorted Array
- Binary Search on Rotated Sorted Array (also called Pivot-based Binary Search)
- TC:O(nlogn)
```java
public int findMin(int[] nums) {

        int left = 0, right = nums.length - 1;

        while (left < right) {

            int mid = left + (right - left) / 2;

            if (nums[mid] > nums[right]) {
                // minimum is in right half
                left = mid + 1;
            } else {
                // minimum is at mid or in left half
                right = mid;
            }
        }

        return nums[left];
    }
}
```

# LC8 
```
Search in Rotated Sorted Array
There is an integer array nums sorted in ascending order (with distinct values).
Prior to being passed to your function, nums is possibly left rotated at an unknown index k (1 <= k < nums.length) such that the resulting array is [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]] (0-indexed). For example, [0,1,2,4,5,6,7] might be left rotated by 3 indices and become [4,5,6,7,0,1,2].
Given the array nums after the possible rotation and an integer target, return the index of target if it is in nums, or -1 if it is not in nums.
You must write an algorithm with O(log n) runtime complexity.
Example 1:
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
Example 2:
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
Example 3:
Input: nums = [1], target = 0
Output: -1

- Search in Rotated Sorted Array
- Modified Binary Search on Rotated Sorted Array
- write an algorithm with O(log n) runtime complexity
- TC:O(nlogn)
```
```java
public int search(int[] nums, int target){
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            }
            // Left half is sorted
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } 
            // Right half is sorted
            else {
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```


# LC10
![Not Available](images/Q10.png) 
```
Container With Most Water
You are given an integer array height of length n. There are n vertical lines drawn such that the two endpoints of the ith line are (i, 0) and (i, height[i]).
Find two lines that together with the x-axis form a container, such that the container contains the most water.
Return the maximum amount of water a container can store.
Notice that you may not slant the container.
Examle 1: Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
Example 2:
Input: height = [1,1]
Output: 1

- Container With Most Water
- Two Pointer Technique
- intuition-based pointer movement
- TC:?
```
```java
public int maxArea(int[] height) {

        int left = 0, right = height.length - 1;
        int maxArea = 0;

        while (left < right) {

            int area = Math.min(height[left], height[right]) * (right - left);
            maxArea = Math.max(maxArea, area);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return maxArea;
    }
}
```

# LC11 
```
Factorial Trailing Zeroes
Given an integer n, return the number of trailing zeroes in n!.
Note that n! = n * (n - 1) * (n - 2) * ... * 3 * 2 * 1.
Example 1:
Input: n = 3
Output: 0
Explanation: 3! = 6, no trailing zero.
Example 2:
Input: n = 5
Output: 1
Explanation: 5! = 120, one trailing zero.
Example 3:
Input: n = 0
Output: 0

- Factorial Trailing Zeros
- Factor Counting / Mathematical Observation (Counting Factors of 5)
- TC:O(logs n) SC:O(1)
```
```java
    public int trailingZeroes(int n) {

        int count = 0;

        while (n > 0) {
            n /= 5;
            count += n;
        }

        return count;
    }
}
```

# LC12 
```
Trapping Rain Water
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.
Example 1:
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
Example 2:
Input: height = [4,2,0,3,2,5]
Output: 9

- Trapping Rain Water
- 2 Pointer Technique with LeftMax & RightMax (Alternate: Prefix Max / Suffix Max)
```
```java
public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        int water = 0;

        while (left < right) {

            if (height[left] <= height[right]) {

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
}
```

# LC13 
```
Chocolate Distribution Problem
* `N` chocolate packets diye gaye hain
* Har packet me kuch chocolates hain (`arr[i]`)
* `M` students hain

Har student ko **exactly 1 packet** dena hai
Objective:
(max chocolates - min chocolates) ko minimum karna

- Chocolate Distribution Problem
- Sorting + Sliding Window (Greedy)
- TC:O(n log n) (Sorting) SC:O(1)
```
```java
public static int findMinDiff(int[] arr, int n, int m) {

        if (m == 0 || n == 0) return 0;
        if (m > n) return -1;

        Arrays.sort(arr);

        int minDiff = Integer.MAX_VALUE;

        for (int i = 0; i + m - 1 < n; i++) {
            int diff = arr[i + m - 1] - arr[i];
            minDiff = Math.min(minDiff, diff);
        }
        return minDiff;
    }
}
```

# LC14 
Insert Interval
You are given an array of non-overlapping intervals intervals where intervals[i] = [starti, endi] represent the start and the end of the ith interval and intervals is sorted in ascending order by starti. You are also given an interval newInterval = [start, end] that represents the start and end of another interval.
Insert newInterval into intervals such that intervals is still sorted in ascending order by starti and intervals still does not have any overlapping intervals (merge overlapping intervals if necessary).
Return intervals after the insertion.
Note that you don't need to modify intervals in-place. You can make a new array and return it.
Example 1:
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
Example 2:
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

- Insert Interval
- Interval Merging (Greedy / Linear Scan)
- No need to modify intervals in-place. You can make a new array and return it.
- intervals is sorted by starti in ascending order.
- TC:O(n) SC:O(n)
```java
public int[][] insert(int[][] intervals, int[] newInterval) {

        List<int[]> result = new ArrayList<>();

        for (int[] interval : intervals) {

            // Case 1: interval completely before newInterval
            if (interval[1] < newInterval[0]) {
                result.add(interval);
            }

            // Case 2: interval completely after newInterval
            else if (interval[0] > newInterval[1]) {
                result.add(newInterval);
                newInterval = interval;
            }

            // Case 3: overlapping intervals
            else {
                newInterval[0] = Math.min(newInterval[0], interval[0]);
                newInterval[1] = Math.max(newInterval[1], interval[1]);
            }
        }

        // add the last newInterval
        result.add(newInterval);

        return result.toArray(new int[result.size()][]);
    }
}
```

# LC15 
Given an array of intervals where intervals[i] = [starti, endi], merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.
Example 1:
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].
Example 2:
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
Example 3:
Input: intervals = [[4,7],[1,4]]
Output: [[1,7]]
Explanation: Intervals [1,4] and [4,7] are considered overlapping.

- Merge Intervals
- Sorting + Greedy Interval Merging
- TC:O(n log n) (Sorting) SC:O(n)
```java
public int[][] merge(int[][] intervals) {

        // Step 1: sort by start time
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);

        List<int[]> result = new ArrayList<>();

        // Step 2: add first interval
        result.add(intervals[0]);

        // Step 3: process remaining intervals
        for (int i = 1; i < intervals.length; i++) {

            int[] last = result.get(result.size() - 1);
            int[] current = intervals[i];

            // overlap
            if (current[0] <= last[1]) {
                last[1] = Math.max(last[1], current[1]);
            }
            // no overlap
            else {
                result.add(current);
            }
        }

        return result.toArray(new int[result.size()][]);
    }
}
```

# LC16
``` 
Non-overlapping Intervals
Given an array of intervals where intervals[i] = [starti, endi], return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.
Note that intervals which only touch at a point are non-overlapping. For example, [1, 2] and [2, 3] are non-overlapping.
Example 1:
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.
Example 2:
Input: intervals = [[1,2],[1,2],[1,2]]
Output: 2
Explanation: You need to remove two [1,2] to make the rest of the intervals non-overlapping.
Example 3:
Input: intervals = [[1,2],[2,3]]
Output: 0
Explanation: You don't need to remove any of the intervals since they're already non-overlapping.

- Non-overlapping Intervals
- Greedy Interval Scheduling (sort by end time)
- TC:O(n log n) SC:O(1) Extra
```
```java
public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        // sort by end time
        Arrays.sort(intervals, (a, b) -> a[1] - b[1]);
        int remove = 0;
        int lastEnd = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {

            if (intervals[i][0] < lastEnd) {
                // overlap → remove current
                remove++;
            } else {
                // no overlap → keep
                lastEnd = intervals[i][1];
            }
        }
        return remove;
    }
}
```

# Doubt1
```
Q: here 3 Q Insert(14), merge(15) intervals we are sorting based on start value but Non Overlapping(16) intervals we are sorting based on last value why?
```
💥 Exactly.
Non-overlapping problem = **selection problem**.

Now we unlock the confusion.


# 🔎 Why Different Sorting?

## 🟢 Merge / Insert

Goal:
👉 Combine overlapping intervals
👉 Maintain structure

Yahan hum **decision nahi le rahe ki kaunsa interval rakhen**.

Hum bas:

1. Sort by **start**
2. Compare current interval with previous
3. Overlap ho to merge

Why sort by start?

Think logically:

If intervals start ke hisaab se sorted nahi honge,
to overlap check karna mushkil ho jayega.

Example:

[4,7]
[1,4]

Agar start se sort nahi kiya,
to merging logic break ho jayega.

👉 So sorting by start ensures:
Intervals natural timeline order me aa jaye.

Yahan goal = **structure clean karna**.

---

## 🔴 Non Overlapping Intervals

Goal:
👉 Maximum intervals rakhna
👉 Minimum remove karna

This is a **selection problem**.

Ab question:

Agar do intervals overlap karte hain,
to kaunsa rakhen?

Example:

[1,4]
[2,3]

Agar dono overlap karte hain,
to kaunsa better hai rakhna?

Think.

---

Which one future ke liye zyada jagah chhodega?

[1,4]
or
[2,3] ?

---

Clearly:

[2,3] finishes earlier.

To future me aur intervals fit ho sakte hain.

Yahi reason hai:

> Non-overlapping me sort by END time.

Because:
Earliest finish = maximum future space.

---

# 🎯 Final Comparison (Very Important)

| Problem Type    | What are we doing? | Sort by |
| --------------- | ------------------ | ------- |
| Merge           | Combine structure  | Start   |
| Insert          | Maintain structure | Start   |
| Non-overlapping | Select intervals   | End     |

---

# 🧠 Core Insight

If problem is:

* Structure-based → sort by start
* Selection-based → sort by end

---

Now I want you to think one level deeper.

Suppose Non-overlapping problem me hum start ke basis pe sort karein.

Kya issue aa sakta hai?

Think using example:

[1,4]
[2,3]

Agar start sort kiya, kaunsa pehle aayega?

Aur kya galat decision ho sakta hai?

Explain in your words. 💪

YESSS 🔥

Exactly.

Agar hum start ke basis pe sort karte:

Order hota:

[1,4]
[2,3]

Agar tum pehla interval blindly le lete ([1,4])
to tum:

* [2,3] lose kar dete
* Aur future me aur bhi intervals lose kar sakte ho

Matlab:

> Tumne bada interval leke future ki space block kar di.

Isliye selection problem me:

Earliest finish choose karte hain.

Because:

Chhota end → zyada future space → maximum intervals fit.

---

# 🧠 Ab Full Mental Model Clear Kar Lo

## 1️⃣ Merge / Insert

Hum decision nahi le rahe ki kaunsa rakhen.

Hum bas timeline ko clean kar rahe hain.

Isliye:
Sort by START
Phir merge.

---

## 2️⃣ Non-Overlapping

Hum choose kar rahe hain kaunsa interval rakhen.

Isliye:
Sort by END
Earliest finish choose karo
Future maximize karo.

---

# 🎯 Golden Rule (Notes me likho)

If problem asks:

* “Merge”
* “Insert”
* “Combine”

→ Sort by START

If problem asks:

* “Maximum non-overlapping”
* “Minimum removals”
* “Maximum meetings”

→ Sort by END

---

Ab final check.

Suppose problem:

“You have meetings. Find maximum number you can attend.”

Without overthinking —

Start se sort karoge
ya end se?

Aur kyun?

One line answer. 💪
```
