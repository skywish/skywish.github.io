---
title: "LeetCode Record"
permalink: /docs/leetcode-record/
excerpt: "Daily Record of LeetCode"
modified: 2017-02-06T14:33:41 
---
Daily Record of LeetCode

{% include toc %}

## 2017-02-06

### 287 Find the Duplicate Number

### 283 Move Zeroes

### 268 Missing Number
The basic idea is to use XOR operation. We all know that a^b^b =a

### 219 Contains Duplicate II
**My first solution**: find two Duplicate nums and see if less than k

```java
public class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        int len = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < len; i++) {
            if (map.containsKey(nums[i]) && Math.abs(i - map.get(nums[i])) <= k) {
                return true;
            } else {
                map.put(nums[i], i);
            }
        }
        return false;
    }
}
```

**Top solution**: maintain a set, whose size is k, and see if nums[i] is in this set.

```java
public class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        int len = nums.length;
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < len; i++) {
            if (i > k) {
                set.remove(nums[i - k - 1]);
            } 
            if (!set.add(nums[i])) {
                return true;
            }
        }
        return false;
    }
}
``` 

### 217 Contains Duplicate

## 2017-02-07

### 216 Combination Sum III

Use DFS to search all possible combinations. For 1, search 2 to 9; for 2, search 3 to 9; and etc. We need to notice that A.add(B), if you change B in Java, A also changes. So we need to create new ArrayList and deep copy temp, like `ans.add(new ArrayList<>(temp))`

```java
public class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> temp = new ArrayList<>();
        DFS(res, temp, 0, k, n);
        return res;
    }
    
    private void DFS(List<List<Integer>> ans, List<Integer> temp, int i, int k, int n) {
        if (i > 0) {
            temp.add(i);
        }
        n -= i;
        if (temp.size() == k) {
            if (n == 0) {
                ans.add(new ArrayList<>(temp));
            }
            temp.remove(temp.size() - 1);
            n += i;
            return;
        }
        for (int j = i + 1; j <= 9; j++) {
            DFS(ans, temp, j, k, n);
        }
        if (i > 0) {
            temp.remove(temp.size() - 1);
            n += i;
        }
    }
}
```

The top solution uses backtracking algorithm. 回溯算法

```java
 public List<List<Integer>> combinationSum3(int k, int n) {
    List<List<Integer>> ans = new ArrayList<>();
    combination(ans, new ArrayList<Integer>(), k, 1, n);
    return ans;
}

private void combination(List<List<Integer>> ans, List<Integer> comb, int k,  int start, int n) {
	if (comb.size() == k && n == 0) {
		List<Integer> li = new ArrayList<Integer>(comb);
		ans.add(li);
		return;
	}
	for (int i = start; i <= 9; i++) {
		comb.add(i);
		combination(ans, comb, k, i+1, n-i);
		comb.remove(comb.size() - 1);
	}
}
```

### 189. Rotate Array

Reverse the first n - k elements, the last k elements, and then all the n elements.
Time complexity: O(n). Space complexity: O(1).

```java
public class Solution {
    public void rotate(int[] nums, int k) {
        int len = nums.length;
        k %= len;
        reverse(nums, 0, len - k - 1);
        reverse(nums, len - k, len - 1);
        reverse(nums, 0, len - 1);
    }
    
    public void reverse(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
```

### 169. Majority Element

If there exists a Majority number, then its count must `> Math.floor(length)`, which can not reduce to 0. So **res** must store the Majority number.

```java
public class Solution {
    public int majorityElement(int[] nums) {
        int res = nums[0], count = 1;
        for (int i = 1; i < nums.length; i++) {
            if (count == 0) {
                res = nums[i];
                count++;
            } else if (res == nums[i]) {
                count++;
            } else {
                count--;
            }
        }
        return res;
    }
}
```

### 167. Two Sum II - Input array is sorted

Since input array is sorted, we can take advatange of it. The common way to solve those kinds of questions is to use HashMap, but this one we can just use two points.

```java
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int low = 0, high = numbers.length - 1, sum = 0;
        int[] res = new int[2];
        while(true) {
            sum = numbers[low] + numbers[high];
            if (sum == target) {
                res[0] = low + 1;
                res[1] = high + 1;
                return res;
            } else if (sum < target) {
                low++;
            } else {
                high--;
            }
        }
    }
}
```

### 153. Find Minimum in Rotated Sorted Array

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).

Find the minimum element.

**Solution:** According to the rule, the rightest num must be local largest, the leftest number must be local smallest. 
If `left < right`, that means the left one is actually the smallest one.
The key thought is to find an element that `A[i-1] < A[i] > A[i+1]`. If the middle number is larger than the rightest one, the minimum number is on the right. Else, it must be on the left.
`A[mid] > A[right] > A[mid+i]`. We can see if we want to find minimum number, it must locate at the place which is lower than other place, like a **canyon**.

```java
public class Solution {
    public int findMin(int[] nums) {
        int len = nums.length, left = 0, right = len - 1;
        
        while (left < right) {
            if (nums[left] < nums[right]) {
                return nums[left];
            }
            
            int mid = (left + right) / 2;
            
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return nums[left];
    }
}
```

### 152. Maximum Product Subarray

Top solution: Loop through the array, each time remember the max and min value for the previous product, the most important thing is to update the max and min value: we have to compare among max * A[i], min * A[i] as well as A[i], since this is product, a negative * negative could be positive.

```java
public class Solution {
    public int maxProduct(int[] A) {
        if (A == null || A.length == 0) {
            return 0;
        }
        int max = A[0], min = A[0], result = A[0];
        for (int i = 1; i < A.length; i++) {
            int temp = max;
            max = Math.max(Math.max(max * A[i], min * A[i]), A[i]);
            min = Math.min(Math.min(temp * A[i], min * A[i]), A[i]);
            if (max > result) {
                result = max;
            }
        }
        return result;
    }
}

```

## 2017-02-10
### 15. 3Sum
> Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

我们需要注意，对于res[0],我们要求它与之前的数要不一样。对于res[1]，我们要求其与

Top solution:

```java
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < nums.length - 2; i++) {
            // skip same result
            if (i == 0 || (i > 0 && nums[i] != nums[i - 1])) {
                int low = i + 1, high = nums.length - 1, sum = 0 - nums[i];
                while (low < high) {
                    if (nums[low] + nums[high] == sum) {
                        res.add(Arrays.asList(nums[i], nums[low], nums[high]));
                        // skip same result
                        while (low < high && nums[low] == nums[low + 1]) {
                            low++;
                        } 
                        // skip same result
                        while (low < high && nums[high] == nums[high - 1]) {
                            high--;
                        }
                        low++;
                        high--;
                    } else if (nums[low] + nums[high] > sum) {
                        high--;
                    } else {
                        low++;
                    }
                } 
            }
        }
        return res;
    }
}
```

Time limit!

```java
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> list = new ArrayList<>();
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 2; i++) {
            resSum(nums, res, list, i, 0, 0);
        }
        return res;
    }
    
    public void resSum(int[] nums, List<List<Integer>> res, List<Integer> list, int i, int sum, int count) {
        if (count == 2) {
            if (nums[i] + sum == 0) {
                list.add(nums[i]);
                res.add(new ArrayList(list));
                count--;
                sum -= nums[i];
                list.remove(list.size() - 1);
                return;
            } else if (nums[i] + sum > 0) {
                return;
            }
        } else {
            if (count == 0 && i > 0 && nums[i] == nums[i - 1]) {
                return;
            }
            list.add(nums[i]);
            count++;
            sum += nums[i];
            for (int j = i + 1; j < nums.length; j++) {
                if (j != i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                } else {
                    resSum(nums, res, list, j, sum, count);
                }
            }
            count--;
            sum -= nums[i];
            list.remove(list.size() - 1);
        }
    }
}
```

### 16. 3Sum Closest
> Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

```java
public class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int res = nums[0] + nums[1] + nums[2], sum = 0;
        for (int i = 0; i < nums.length - 2; i++) {
            int low = i + 1, high = nums.length - 1;
            while (low < high) {
                sum = nums[low] + nums[high] + nums[i];
                if (sum == target) {
                    return target;
                } else if (sum < target) {
                    low++;
                } else {
                    high--;
                }
                if (Math.abs(sum - target) < Math.abs(res - target)) {
                    res = sum;
                }
            }
        }
        return res;
    }
}
```

### 18. 4Sum

Top solution: 基本思想不变，加了很多判断语句来减少循环次数。

```java
public List<List<Integer>> fourSum(int[] nums, int target) {
		ArrayList<List<Integer>> res = new ArrayList<List<Integer>>();
		int len = nums.length;
		if (nums == null || len < 4)
			return res;

		Arrays.sort(nums);

		int max = nums[len - 1];
		if (4 * nums[0] > target || 4 * max < target)
			return res;

		int i, z;
		for (i = 0; i < len; i++) {
			z = nums[i];
			if (i > 0 && z == nums[i - 1])// avoid duplicate
				continue;
			if (z + 3 * max < target) // z is too small
				continue;
			if (4 * z > target) // z is too large
				break;
			if (4 * z == target) { // z is the boundary
				if (i + 3 < len && nums[i + 3] == z)
					res.add(Arrays.asList(z, z, z, z));
				break;
			}

			threeSumForFourSum(nums, target - z, i + 1, len - 1, res, z);
		}

		return res;
	}

	/*
	 * Find all possible distinguished three numbers adding up to the target
	 * in sorted array nums[] between indices low and high. If there are,
	 * add all of them into the ArrayList fourSumList, using
	 * fourSumList.add(Arrays.asList(z1, the three numbers))
	 */
	public void threeSumForFourSum(int[] nums, int target, int low, int high, ArrayList<List<Integer>> fourSumList,
			int z1) {
		if (low + 1 >= high)
			return;

		int max = nums[high];
		if (3 * nums[low] > target || 3 * max < target)
			return;

		int i, z;
		for (i = low; i < high - 1; i++) {
			z = nums[i];
			if (i > low && z == nums[i - 1]) // avoid duplicate
				continue;
			if (z + 2 * max < target) // z is too small
				continue;

			if (3 * z > target) // z is too large
				break;

			if (3 * z == target) { // z is the boundary
				if (i + 1 < high && nums[i + 2] == z)
					fourSumList.add(Arrays.asList(z1, z, z, z));
				break;
			}

			twoSumForFourSum(nums, target - z, i + 1, high, fourSumList, z1, z);
		}

	}

	/*
	 * Find all possible distinguished two numbers adding up to the target
	 * in sorted array nums[] between indices low and high. If there are,
	 * add all of them into the ArrayList fourSumList, using
	 * fourSumList.add(Arrays.asList(z1, z2, the two numbers))
	 */
	public void twoSumForFourSum(int[] nums, int target, int low, int high, ArrayList<List<Integer>> fourSumList,
			int z1, int z2) {

		if (low >= high)
			return;

		if (2 * nums[low] > target || 2 * nums[high] < target)
			return;

		int i = low, j = high, sum, x;
		while (i < j) {
			sum = nums[i] + nums[j];
			if (sum == target) {
				fourSumList.add(Arrays.asList(z1, z2, nums[i], nums[j]));

				x = nums[i];
				while (++i < j && x == nums[i]) // avoid duplicate
					;
				x = nums[j];
				while (i < --j && x == nums[j]) // avoid duplicate
					;
			}
			if (sum < target)
				i++;
			if (sum > target)
				j--;
		}
		return;
	}
```

My solution: The same as 3sum

```java
public class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        int length = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < length - 3; i++) {
            if (i == 0 || (i > 0 && nums[i] != nums[i - 1])) {
                for (int j = i + 1; j < length - 2; j++) {
                    if (j == i + 1 || (j > i + 1 && nums[j] != nums[j - 1])) {
                        int low = j + 1, high = length - 1;
                        while (low < high) {
                            if (nums[low] + nums[high] == target - nums[i] - nums[j]) {
                                res.add(Arrays.asList(nums[i], nums[j], nums[low], nums[high]));
                                while (low < high && nums[low] == nums[low + 1]) low++;
                                while (low < high && nums[high] == nums[high - 1]) high--;
                                low++; high--;
                            } else if (nums[low] + nums[high] < target - nums[i] - nums[j]) {
                                low++;
                            } else {
                                high--;
                            }
                        }
                    }
                }
            }
        }
        return res;
    }
}
```

### 26. Remove Duplicates from Sorted Array
>Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length. 
Do not allocate extra space for another array, you must do this in place with constant memory.
For example, Given input array nums = [1,1,2], Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

```java
public class Solution {
    public int removeDuplicates(int[] nums) {
        int res = 0, count = 0;
        if (nums != null && nums.length > 0) {
            res++;
        }
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1 - count]) {
                count++;
            } else {
                nums[i - count] = nums[i];
                res++;
            }
        }
        return res;
    }
}
```