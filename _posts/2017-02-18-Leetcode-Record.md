---
title: "LeetCode Record"
categories:
  - LeetCode
tags:
  - Algorithm
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

Reverse the first n - k elements, the last k elements, and then all the n elements. Time complexity: O(n). Space complexity: O(1).

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

If there exists a Majority number, then its count must `> Math.floor(length)`,
which can not reduce to 0. So **res** must store the Majority number.

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

Since input array is sorted, we can take advantage of it. The common way to
solve those kinds of questions is to use HashMap, but this one we can just use
two points.

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
Suppose an array sorted in ascending order is rotated at some pivot unknown to
you beforehand.

(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).

Find the minimum element.

**Solution:** According to the rule, the rightest num must be local largest,
the first left number must be local smallest. If `left < right`, that means the
left one is actually the smallest one.

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

Top solution: Loop through the array, each time remember the max and min value
for the previous product, the most important thing is to update the max and min
value: we have to compare among `max * A[i], min * A[i]` as well as `A[i]`, since this is product, a negative * negative could be positive.

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

我们需要注意，对于res[0],我们要求它与之前的数要不一样。

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
> Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target.
>
> Return the sum of the three integers. You may assume that each input would have exactly one solution.

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
public class Solution {
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

	public void twoSumForFourSum(int[] nums, int target, int low, int high, ArrayList<List<Integer>> fourSumList, int z1, int z2) {

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

>Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length. Do not allocate extra space for another array, you must do this in place with constant memory.
>
>For example, Given input array nums = [1,1,2], Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

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

## 2017-02-13

### 27. Remove Element

Top solution:

```java
public class Solution {
    public int removeElement(int[] nums, int val) {
        int begin = 0;
        for(int i : nums) {
            if (i != val) {
                nums[begin++] = i;
            }
        }
        return begin;
    }
}
```

My solution : the same as

```java
public class Solution {
    public int removeElement(int[] nums, int val) {
        int len = nums.length, res = len, count = 0;
        for (int i = 0; i < len; i++) {
            if (nums[i] == val) {
                count++;
                res--;
            } else if (count > 0) {
                nums[i - count] = nums[i];
            }
        }
        return res;
    }
}
```

### 367. Valid Perfect Square

Top solution: to use __Newton Method__ to calculate the square root or num,
refer to Newton Method for details.

```java
public boolean isPerfectSquare(int num) {
    long x = num;
    while (x * x > num) {
        x = (x + num / x) >> 1;
    }
    return x * x == num;
}
```

### 139. Word Break

> Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words. You may assume the dictionary does not contain duplicate words.

>For example, given s = "leetcode", dict = ["leet", "code"]. Return true because "leetcode" can be segmented as "leet code".

Top solution:

```java
public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> set = new HashSet<String>(wordDict);
        boolean[] list = new boolean[s.length() + 1];
        list[0] = true;

        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                // substring(begin, end)  [begin, end)
                if (list[j] && set.contains(s.substring(j, i))) {
                    list[i] = true;
                    break;
                }
            }
        }

        return list[s.length()];
    }
}
```

第一次没理解对题目，以为是 combine several string ， 但实际上是看是否能靠 wordDict 组成 String。

```java
public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Map<Integer, Integer> map = new HashMap<>();
        int index = 0;

        for (int i = 0; i < wordDict.size(); i++) {
            index = s.indexOf(wordDict.get(i));
            if (index >= 0) {
                map.put(index, index + wordDict.get(i).length());
            }
        }

        index = 0;

        while(map.containsKey(index)) {
            index = map.get(index);
        }

        return index == s.length();
    }
}
```

## 2017-02-21

### 41. First Missing Positive

> Given an unsorted integer array, find the first missing positive integer.
>
> For example, Given [1,2,0] return 3, and [3,4,-1,1] return 2.
>
> Your algorithm should run in O(n) time and uses constant space.

> Tag： 数组
> 运行时间 > 48%

`nums[nums[i]- 1]` 指的是 nums[i] 对应的数对应的数组位置，通过 while 循环，利用数组的特征，保证`nums[nums[i]- 1] == nums[i]`。很多数组题要求 constant space 都可以想到数组可以看成 Map<Integer, Integer> 。


```java
public class Solution {
    public int firstMissingPositive(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            while (nums[i] >= 1 && nums[i] <= nums.length && nums[nums[i]- 1] != nums[i]) {
                swap(nums, i, nums[i] - 1);  
            } 
        }
        
        int i = 0;
        while (i < nums.length && nums[i] == i + 1) {
            i++;
        }
        
        return i + 1;
    }
    
    public void swap(int[] nums, int i, int j) {
        nums[i] ^= nums[j];
        nums[j] ^= nums[i];
        nums[i] ^= nums[j];
    }
}
```

### 7. Reverse Integer

> Reverse digits of an integer.
>
> Example1: x = 123, return 321
> Example2: x = -123, return -321

Top solution: `res`代表位数，`tail`代表用`%`获取的最末尾的数，逐次循环将数字反转，若遇到`(newRes - tail) / 10 != res`时，必然是 newRes overflow，输出0。用 IDE 运行`x=-2147483648`的情况，`newRes = -846384741 * 10  - 2 = 126087180`

```java
public class Solution {
    public int reverse(int x) {
        int res = 0;
        
        while(x !=  0) {
            int tail = x % 10;
            int newRes = res * 10 + tail;
            if ((newRes - tail) / 10 != res) {
                return 0;
            }
            res = newRes;
            x /= 10;
        }
        
        return res;
    }
}
```

脑洞大开版：利用`Integer.toString`将 num 转换为 String，利用`new StringBuffer().reverse().toString()`将 string 翻转，利用`Integer.parseInt()`将 String 转换为 int
缺点：overflow

```java
public class Solution {
    public int reverse(int x) {
        String ori = Integer.toString(x), res = "";
        
        if (ori.charAt(0) == '-') {
            res = new StringBuffer(ori.substring(1)).reverse().toString();
            res = "-" + res;
        } else {
            res = new StringBuffer(ori).reverse().toString();
        }
        
        return Integer.parseInt(res);
    }
}
```

### 14. Longest Common Prefix

```java
public class Solution {
    public String longestCommonPrefix(String[] strs) {
        String res = "";
        if (strs.length == 0) {
            return res;
        }
        for (int i = 0; i < strs[0].length(); i++) {
            char a = strs[0].charAt(i);
            for (int j = 0; j < strs.length; j++) {
                if (i >= strs[j].length() || a != strs[j].charAt(i)) {
                    return res;
                }
            }
            res += a;
        }
        
        return res;
    }
}
```

### 350. Intersection of Two Arrays II

```java
import java.util.*;

public class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        
        if (nums1.length == 0 || nums2.length == 0) {
            return new int[0];
        }
        
        List<Integer> list = new ArrayList<>();
        
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        
        int i = 0, j = 0;
        while (i < nums1.length && j < nums2.length) {
            if (nums1[i] == nums2[j]) {
                list.add(nums1[i]);
                i++;
                j++;
            } else if (nums1[i] > nums2[j]) {
                j++;
            } else {
                i++;
            }
        }
        
        int[] res = new int[list.size()];
        for (int k = 0; k < list.size(); k++) {
            res[k] = list.get(k);
        }
        return res;
    }
}
```

### 174. Dungeon Game

> The demons had captured the princess (P) and imprisoned her in the bottom-right corner of a dungeon. The dungeon consists of M x N rooms laid out in a 2D grid. Our valiant knight (K) was initially positioned in the top-left room and must fight his way through the dungeon to rescue the princess.
>
> The knight has an initial health point represented by a positive integer. If at any point his health point drops to 0 or below, he dies immediately.
>
> Some of the rooms are guarded by demons, so the knight loses health (negative integers) upon entering these rooms; other rooms are either empty (0's) or contain magic orbs that increase the knight's health (positive integers).
>
> In order to reach the princess as quickly as possible, the knight decides to move only rightward or downward in each step.
>
> Write a function to determine the knight's minimum initial health so that he is able to rescue the princess.

一开始想用 uniform cost search 做，但实现过于复杂，代码行数太多。  
看了 solution ，一般用动态规划做。从最右下角的格子开始算起，比较1（最少生命是1）和相邻格子减去本格子的值，取较大者，从右下角扩散到左上角。  
比较好奇感觉能用 UCS 做，不知道哪个快点。

```java
public class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int m = dungeon.length;
        int n = dungeon[0].length;
        
        int[][] cost = new int[m][n];
        cost[m - 1][n - 1] = Math.max(1, 1 - dungeon[m - 1][n - 1]);
        
        for (int i = m - 2; i >= 0; i--) {
            cost[i][n - 1] = Math.max(1, cost[i + 1][n - 1] - dungeon[i][n - 1]);
        }
        
        for (int j = n - 2; j >= 0; j--) {
            cost[m - 1][j] = Math.max(1, cost[m - 1][j + 1] - dungeon[m - 1][j]);
        }
        
        for (int i = m - 2; i >= 0; i--) {
            for (int j = n - 2; j >= 0; j--) {
                cost[i][j] = Math.max(1, Math.min(cost[i][j + 1] - dungeon[i][j], cost[i + 1][j] - dungeon[i][j]));
            }
        }
        
        return cost[0][0];
    }
}
```

### 228. Summary Ranges

比较简单

```java
public class Solution {
    public List<String> summaryRanges(int[] nums) {
        if (nums.length == 0) {
            return new ArrayList<>(); 
        }
        int low = 0, high = 0;
        List<String> list = new ArrayList<>();
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1] + 1) {
                high = i;
            } else {
                String s = low == high ? nums[low] + "" : nums[low] + "->" + nums[high];
                list.add(s);
                low = i;
                high = i;
            }
        }
        
        String s = low == high ? nums[low] + "" : nums[low] + "->" + nums[high];
        list.add(s);
        
        return list;
    }
}
```

### 28. Implement strStr()

默认规则：当`needle is empty`，返回0。

```java
public class Solution {
    public int strStr(String haystack, String needle) {
        int nlen = needle.length(), hlen = haystack.length();
        
        if (nlen == 0) {
            return 0;
        }
        
        if (nlen > hlen) {
            return -1;
        }
        
        for (int i = 0; i <= hlen - nlen; i++) {
            if (haystack.substring(i, i + nlen).equals(needle)) {
                return i;
            }
        }
        
        return -1;
    }
}
```

### 103. Binary Tree Zigzag Level Order Traversal

use DFS 

```java
public class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) 
    {
        List<List<Integer>> sol = new ArrayList<>();
        travel(root, sol, 0);
        return sol;
    }
    
    private void travel(TreeNode curr, List<List<Integer>> sol, int level)
    {
        if(curr == null) return;
        
        if(sol.size() <= level)
        {
            List<Integer> newLevel = new LinkedList<>();
            sol.add(newLevel);
        }
        
        List<Integer> collection  = sol.get(level);
        if(level % 2 == 0) collection.add(curr.val);
        else collection.add(0, curr.val);
        
        travel(curr.left, sol, level + 1);
        travel(curr.right, sol, level + 1);
    }
}
```

