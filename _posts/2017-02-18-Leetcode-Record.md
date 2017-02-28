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

脑洞大开版：

利用`Integer.toString`将 num 转换为 String，利用`new StringBuffer().reverse().toString()`将 string 翻转，利用`Integer.parseInt()`将 String 转换为 int

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

用两个stack，一个存储奇数层，从左到右，`push right -> push left`，一个存储偶数层，从右到左，`push left -> push right`。

```java
public class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        
        if (root == null) {
            return res;
        }
        
        Stack<TreeNode> oddLayer = new Stack<>();
        Stack<TreeNode> evenLayer = new Stack<>();
        
        oddLayer.push(root);
        
        while (!oddLayer.isEmpty()) {
            List<Integer> temp = new ArrayList<>();
            TreeNode next = null;
            while (!oddLayer.isEmpty()) {
                next = oddLayer.pop();
                if (next.left != null) evenLayer.push(next.left);
                if (next.right != null) evenLayer.push(next.right);
                temp.add(next.val);
            }
            res.add(temp);
            
            temp = new ArrayList<>();
            while (!evenLayer.isEmpty()) {
                next = evenLayer.pop();
                if (next.right != null) oddLayer.push(next.right);
                if (next.left != null) oddLayer.push(next.left);
                temp.add(next.val);
            }
            if (!temp.isEmpty()) res.add(temp);
        }
        
        return res;
    }
}
```

## 2017-02-22

### 111. Minimum Depth of Binary Tree

用BFS做，如果有一层左右子树都没有，就返回该层层数。
Top solution 用 DFS 做，个人感觉不大好，要先遍历左子树才能遍历右子树，取其较小值。最差情况左子树茂盛，右子树稀疏，白白浪费很多时间。

但我的代码需要精简，这是毋庸置疑的。//TODO

```java
public class Solution {
    public static int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }

        int layer = 0;
        Boolean isEmpty = true;
        List<TreeNode> layer1 = new ArrayList<>();
        List<TreeNode> layer2 = new ArrayList<>();
        layer1.add(root);
        TreeNode next = null;

        while (!layer1.isEmpty()) {
            while (!layer1.isEmpty()) {
                next = layer1.remove(0);
                if (next.left == null && next.right == null)
                    return layer + 1;
                if (next.left != null) layer2.add(next.left);
                if (next.right != null) layer2.add(next.right);
            }

            layer++;

            while (!layer2.isEmpty()) {
                isEmpty = false;
                next = layer2.remove(0);
                if (next.left == null && next.right == null)
                    return layer + 1;
                if (next.left != null) layer1.add(next.left);
                if (next.right != null) layer1.add(next.right);
            }

            if (!isEmpty) {
                layer++;
                isEmpty = true;
            }

        }

        return layer;
    }
}
```
Top solution: Use DFS.

```java
public static int minDepth(TreeNode root) {
    if (root == null) return 0;
    if (root.left == null) return minDepth(root.right) + 1;
    if (root.right == null) return minDepth(root.left) + 1;
    return Math.min(minDepth(root.left),minDepth(root.right)) + 1;
}
```

### 220. Contains Duplicate III

> Given an array of integers, find out whether there are two distinct indices i and j in the array such that the absolute difference between nums[i] and nums[j] is at most t and the absolute difference between i and j is at most k.

The idea is like the bucket sort algorithm. Suppose we have consecutive buckets covering the range of nums with each bucket a width of (t+1). If there are two item with difference <= t, one of the two will happen:
[Link](https://discuss.leetcode.com/topic/27608/java-python-one-pass-solution-o-n-time-o-n-space-using-buckets/4)

(1) the two in the same bucket
(2) the two in neighbor buckets


```java
private long getID(long i, long w) {
    return i < 0 ? (i + 1) / w - 1 : i / w;
}

public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
    if (t < 0) return false;
    Map<Long, Long> d = new HashMap<>();
    long w = (long)t + 1;
    for (int i = 0; i < nums.length; ++i) {
        long m = getID(nums[i], w);
        if (d.containsKey(m))
            return true;
        if (d.containsKey(m - 1) && Math.abs(nums[i] - d.get(m - 1)) < w)
            return true;
        if (d.containsKey(m + 1) && Math.abs(nums[i] - d.get(m + 1)) < w)
            return true;
        d.put(m, (long)nums[i]);
        if (i >= k) d.remove(getID(nums[i - k], w));
    }
    return false;
}
```

### 204. Count Primes

>Description: Count the number of prime numbers less than a non-negative number, n.

注意是小于不是 <=

```java
    public int countPrimes(int n) {
        boolean[] notPrime = new boolean[n];
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (!notPrime[i]) {
                count++;
                for (int j = 2; i * j < n; j++) {
                    notPrime[i * j] = true;
                }
            }
        }
        return count;
    }

    // 小改进，从 j = i 开始，为了防止越界，i <= n ^ 0.5
    public int countPrimes(int n) {
        boolean[] notPrime = new boolean[n];
        int count = 0;
        double pow = Math.pow(n, 0.5);
        for (int i = 2; i < n; i++) {
            if (!notPrime[i]) {
                count++;
                for (int j = i; i * j < n && i <= pow; j++) {
                    notPrime[i * j] = true;
                }
            }
        }
        return count;
    }
```

### 108. Convert Sorted Array to Binary Search Tree

比较简单，但要注意分治时左子树从`（low, mid-1）`右子树从 `(mid+1, high)`。

```java
public class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums == null || nums.length == 0) {
            return null;
        }
        return buildTree(nums, 0, nums.length - 1);
    }

    public TreeNode buildTree(int[] nums, int low, int high) {
        int mid = (low + high) / 2 ;
        TreeNode next = new TreeNode(nums[mid]);
        if (mid > low) next.left = buildTree(nums, low, mid - 1);
        if (mid < high) next.right = buildTree(nums, mid + 1, high);
        return next;
    }
}
```

### 229. Majority Element II

很有意思的一道题，用了[Majority Voting Algorithm](https://gregable.com/2013/10/majority-vote-algorithm-find-majority.html)，原先是用来找出数量超过 n/2 的数，修改后寻找 n/3 。**尤其注意循环时 if 之间一定要用 else 连接**。最后还要检查 candidate 是否超过 n/3 。**因为可能如果存在 >n/3 的数，candidate 一定是该数，但如果不存在，candidate 就会给出错误答案，充分不必要条件。**

```java
public class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> res = new ArrayList<>();
        int count1 = 0, count2 = 0, candidate1 = 0, candidate2 = 1;
        for (int val : nums) {
            if (val == candidate1) {
                count1++;
            } else if (val == candidate2) {
                count2++;
            } else if (count1 == 0) {
                candidate1 = val;
                count1++;
            } else if (count2 == 0) {
                candidate2 = val;
                count2++;
            } else {
                count1--;
                count2--;
            }
        }

        count1 = 0;
        count2 = 0;
        for (int val : nums) {
            if (val == candidate1) {
                count1++;
            } else if (val == candidate2) {
                count2++;
            }
        }
        if (count1 > nums.length / 3) {
            res.add(candidate1);
        }
        if (count2 > nums.length / 3) {
            res.add(candidate2);
        }
        return res;
    }
}
```

### 199. Binary Tree Right Side View

> Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.


跟有道遍历的题解法类似，都是用两个数据结构存储不同的层。总结一下，
- 每次从左往右，2个`FIFO`数据结构，先加入左节点。
- 每次从右往左，2个`FIFO`数据结构，先加入右节点。
- 一次从左往右，下一层先push左节点，一次从右往左，下一层先push右节点。2个`FILO`数据结构（Stack）。
- 一次从右往左，下一层先push右节点，一次从左往右，下一层先push左节点。2个`FILO`数据结构（Stack）。  

```java
public class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) {
            return list;
        }
        
        List<TreeNode> layer1 = new ArrayList<>();
        List<TreeNode> layer2 = new ArrayList<>();
        
        layer1.add(root);
        while (!layer1.isEmpty()) {
            int count = 0;
            while (!layer1.isEmpty()) {
                count++;
                TreeNode next = layer1.remove(0);
                if (count == 1) {
                    list.add(next.val);
                }
                if (next.right != null) layer2.add(next.right);
                if (next.left != null) layer2.add(next.left);
            } 
            count = 0;
            while (!layer2.isEmpty()) {
                count++;
                TreeNode next = layer2.remove(0);
                if (count == 1) {
                    list.add(next.val);
                }
                if (next.right != null) layer1.add(next.right);
                if (next.left != null) layer1.add(next.left);
            } 
        }
        return list;
    }

}
```

### 2017-02-23

#### 60. Permutation Sequence

>The set [1,2,3,…,n] contains a total of n! unique permutations.
>
>By listing and labeling all of the permutations in order, We get the following sequence (ie, for n = 3):
>
>"123" "132" "213" "231" "312" "321"
>
>Given n and k, return the kth permutation sequence.

这道题看了 [discussion](https://discuss.leetcode.com/topic/17348/explain-like-i-m-five-java-solution-in-o-n/2) 才会，
- 先建立 factorial 数组，注意 factorial[0] = 1
- 再建立长度为 n 的列表，建立列表方便增加删除
- k--，因为 k = 1 就是原始数字
- index = k / factorial[n - i] 加入 list.remove(index)


```java
public class Solution {
    public String getPermutation(int n, int k) {
        int[] factorial = new int[n + 1];
        factorial[0] = 1;
        int sum = 1;
        for (int i = 1; i <= n; i++) {
            sum *= i;
            factorial[i] = sum;
        }
        
        List<Integer> list = new ArrayList<>();
        for (int i = 1; i <= n; i++) {
            list.add(i);
        }
        
        k--;
        StringBuilder s = new StringBuilder();
        for (int i = 1; i <= n; i++) {
            int index = k / factorial[n - i];
            s.append(String.valueOf(list.remove(index)));
            k -= index * factorial[n - i];
        }
        
        return String.valueOf(s);
    }
}
```

### 40. Combination Sum II

> Given a collection of candidate numbers (C) and a target number (T), find all unique combinations in C where the candidate numbers sums to T.
>
> Each number in C may only be used once in the combination.

跟我的方法如出一辙，也是用 DFS

```java
public List<List<Integer>> combinationSum2(int[] cand, int target) {
    Arrays.sort(cand);
    List<List<Integer>> res = new ArrayList<List<Integer>>();
    List<Integer> path = new ArrayList<Integer>();
    dfs_com(cand, 0, target, path, res);
    return res;
}
void dfs_com(int[] cand, int cur, int target, List<Integer> path, List<List<Integer>> res) {
    if (target == 0) {
        res.add(new ArrayList(path));
        return ;
    }
    if (target < 0) return;
    for (int i = cur; i < cand.length; i++){
        if (i > cur && cand[i] == cand[i-1]) continue;
        path.add(path.size(), cand[i]);
        dfs_com(cand, i+1, target - cand[i], path, res);
        path.remove(path.size()-1);
    }
}
```

我的方法比较朴素，用 set 防止重复加入，用 DFS 搜索所有可能的解。不推荐使用

```java
public class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        Set<List<Integer>> res = new HashSet<>();
        List<Integer> temp = new ArrayList<>();
        for (int i = 0; i < candidates.length; i++) {
            find(res, temp, candidates, target, i);
        }
        List<List<Integer>> list = new ArrayList<>(res);
        return list;
    }

    public static void find(Set<List<Integer>> res, List<Integer> temp, int[] candidates, int target, int i) {
        if (candidates[i] == target) {
            temp.add(target);
            res.add(new ArrayList(temp));
            temp.remove(temp.size() - 1);
            return;
        } else if (candidates[i] > target) {
            return;
        } else {
            temp.add(candidates[i]);
            for (int j = i + 1; j < candidates.length; j++) {
                find(res, temp, candidates, target - candidates[i], j);
            }
            temp.remove(temp.size() - 1);
        }
    }
}
```

### 9. (Easy) Palindrome Number

> Determine whether an integer is a palindrome. Do this without extra space.

使用了之前的方法将数字反转，再判断是否相等

```java
public class Solution {
    public boolean isPalindrome(int x) {
        if (x < 0) {
            return false;
        }
        int res = x, tail = 0, newres = 0;
        while (res > 0) {
            tail = res % 10;
            newres = 10 * newres + tail;
            res /= 10;
        }
        return newres == x;
    }
}
```

### 17. Letter Combinations of a Phone Number

[Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/?tab=Description)

> Given a digit string, return all possible letter combinations that the number could represent.

TOP solution:使用 FIFO 数据结构，每次将字母加入list，读取下字母时取出队列中所有字符串，加入新字母在重新加入队列中。

- 获取 char 所代表的数字，`Character.getNumericValue()`
- 用 String[] 代替 char[][]

```java
public class Solution {
    public List<String> letterCombinations(String digits) {
        LinkedList<String> ls = new LinkedList<>();
        if (digits == null || digits.length() == 0) {
            return ls;
        }

        ls.add("");
        String[] mapping = new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        for (int i = 0; i < digits.length(); i++) {
            int x = Character.getNumericValue(digits.charAt(i));
            while (ls.peek().length() == i) {
                String before = ls.remove();
                for (int j = 0; j < mapping[x].length(); j++) {
                    String after = before + mapping[x].charAt(j);
                    ls.add(after);
                }
            }
        }
        return ls;
    }
}
```

用 DFS 做

```java
public class Solution {
    public List<String> letterCombinations(String digits) {
        List<String> ls = new ArrayList<>();
        if (digits == null || digits.length() == 0) {
            return ls;
        }
        StringBuilder sb = new StringBuilder();
        // 用 char build 页面有问题，先改成这个
        String[] mapping = new String[] {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        addletter(ls, sb, num, digits, 0);
        return ls;
    }

    public void addletter(List<String> ls, StringBuilder sb, char[][] num, String s, int i) {
        if (i == s.length()) {
            String temp = sb.toString();
            ls.add(temp);
            return;
        } else {
            int index = Integer.valueOf(s.substring(i, i + 1)) - 2;
            for (int j = 0; j < num[index].length; j++) {
                sb.append(num[index][j]);
                addletter(ls, sb, num, s, i + 1);
                sb.deleteCharAt(sb.length() - 1);
            }
        }
    }
}
```

### 6. ZigZag Conversion

[ZigZag Conversion](https://leetcode.com/problems/zigzag-conversion/?tab=Description)

我的方法就是找规律 // TODO

```java
public class Solution {
    public String convert(String s, int numRows) {
        if (s == null || s.length() == 0 || numRows <= 0) {
            return "";
        }
        
        if (numRows == 1) {
            return s;
        }
        
        int part = (numRows - 1) * 2;
        int partnum = s.length() / part;
        String res = "";
        for (int j = 1; j <= numRows; j++) {
            for (int i = 0; i <= partnum; i++) {
                int index = part * i + j;
                if (index <= s.length()) {
                    res += s.charAt(index - 1);
                    if (j > 1 && j < numRows) {
                        if (part * i + 2 + part - j <= s.length())
                            res += s.charAt(part * i + 2 + part - j - 1);
                    }
                }
            }
        }
        
        return res;
    }
}
```

### 20. (Easy) Valid Parentheses

> Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.
>
> The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.

用 stack 来做

```java
public class Solution {
    public boolean isValid(String s) {
        if (s.length() % 2 != 0 || s.length() == 0) {
            return false;
        }
        Stack<Character> cs = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') cs.push(')');
            else if (s.charAt(i) == '[') cs.push(']');
            else if (s.charAt(i) == '{') cs.push('}');
            else if (cs.isEmpty() || cs.pop() != s.charAt(i)) return false;
        }
        return cs.isEmpty();
    }
}
```

### 21. (Easy) Merge Two Sorted Lists

>Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

被一道 easy 的题给虐了，一开始没想到用迭代的方法做，结果。。。

```java
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

### 22. Generate Parentheses

> Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

Recusive:

- 终止条件：`str.length() == 2 * max`。
- 如果`(`的数量小于`n`，加上`(`
- 如果`)`数量小于`(`，加上`)`
- 先尝试加`(`，再尝试加`)`
- `((()))` -> `((` -> `(()`

```java
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        String str = "";
        backtracking(res, str, 0, 0, n);
        return res;
    }
    void backtracking(List<String> res, String str, int open, int close, int max) {
        if (str.length() == 2 * max) {
            res.add(str);
            return;
        }
        if (open < max) {
            backtracking(res, str + "(", open + 1, close, max);
        }
        if (close < open) {
            backtracking(res, str + ")", open, close + 1, max);
        }
    }
```

### 520. Detect Capital

>Given a word, you need to judge whether the usage of capitals in it is right or not. We define the usage of capitals in a word to be right when one of the following cases holds:  
>All letters in this word are capitals, like "USA".  
>All letters in this word are not capitals, like "leetcode".  
>Only the first letter in this word is capital if it has more than one letter, like "Google".  
>Otherwise, we define that this word doesn't use capitals in a right way.

`(int)'a', a=97,z=122,A=65,Z=90`

`Character.compare(char, 'a') >= 0` 小写字母

```java
public class Solution {
    public boolean detectCapitalUse(String word) {
        if (word.length() <= 1) {
            return true;
        }

        // upper case
        if (Character.compare(word.charAt(0), 'a') < 0) {
            if (word.length() == 2) return true;
            else {
                int x = Character.compare(word.charAt(1), 'a');
                for (int i = 2; i < word.length(); i++) {
                    int y = Character.compare(word.charAt(i), 'a');
                    if ((x >= 0 && y < 0) || (x < 0 && y >= 0)) {
                        return false;
                    }
                }
                return true;
            }
        } else {
            for (int i = 1; i < word.length(); i++) {
                int y = Character.compare(word.charAt(i), 'a');
                if (y < 0) {
                    return false;
                }

            }
            return true;
        }
    }
}
```

### 32. (Hard) Longest Valid Parentheses

[Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/?tab=Description)

>Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.  
>For "(()", the longest valid parentheses substring is "()", which has length = 2.  
>Another example is ")()())", where the longest valid parentheses substring is "()()", which has length = 4.

这道题花了很多长时间结果超出时间限制，吐血。。
Top solution:

```java
public class Solution {
    public int longestValidParentheses(String s) {
        Stack<Integer> stack = new Stack<Integer>();
        int max = 0;
        int left = -1;
        for (int j = 0; j < s.length(); j++) {
            if (s.charAt(j) == '(') stack.push(j);
            else {
                if (stack.isEmpty()) left = j;
                else {
                    stack.pop();
                    if (stack.isEmpty()) max = Math.max(max, j - left);
                    else max = Math.max(max, j - stack.peek());
                }
            }
        }
        return max;
    }
}
```

想法对，但超出时间限制，跟 top solution 区别就在于我多用了数组，算是巧妙的方法。不过不管是时间复杂度还是空间复杂度都比推荐算法差。ORZ

```java
public class Solution {
    public int longestValidParentheses(String s) {
        if (s.length() <= 1) {
            return 0;
        }
        int i = 0;
        while (i < s.length() && s.charAt(i) == ')') {
            i++;
        }
        
        Stack<Integer> open = new Stack<>();
        int[] nums = new int[s.length()];

        int max = 0, count = 0;
        for (int j = i; j < s.length(); j++) {
            if (s.charAt(j) == '(') {
                open.push(j);
            } else if (!open.isEmpty()) {
                int x = open.pop();
                nums[x] = 1;
                nums[j] = 1;
            }
        }
        
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] == 1) {
                count++;
                max = Math.max(max, count);
            } else {
                count = 0;
            }
        }
        
        return max;
    }
}
```

## 2017-02-27

### 53. Maximum Subarray

> Find the contiguous subarray within an array (containing at least one number) which has the largest sum.
>
> For example, given the array [-2,1,-3,4,-1,2,1,-5,4], the contiguous subarray [4,-1,2,1] has the largest sum = 6.

用 dp 做，`d[i] = Math.max(dp[i-1] + nums[i], nums[i])`，注意这次 result 并不在 dp 数组中取得，dp 数组只是记录 the maximum subarray ending with A[i]。

```java
public class Solution {
    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int[] dp = new int[len];
        dp[0] = nums[0];
        int max = dp[0];
        for (int i = 1; i < len; i++) {
            // d[i] = Math.max(dp[i-1] + nums[i], nums[i]); the maximum subarray ending with A[i]
            dp[i] = nums[i] + (dp[i - 1] <= 0 ? 0 : dp[i - 1]);
            max = Math.max(max, dp[i]);
        }
        return max;
    }
}
```

### 62. Unique Paths

> A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).
>
> The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
>
> How many possible unique paths are there?

典型的 dp 问题，首先声明从 start 到 end 的路径和从 end 到 start 的路径数量是一样的。我们反转图形 G 得到 Grev。（其实并不需要反转。。。。。。）

- subproblem: 找到从 `end` 到 `(i,j)` 有多少条 unique paths。
- `dp[i][j] = dp[i - 1][j] + dp[i][j - 1];`
- 边界条件：因为反转后我们只能向上走或向下走，所以`dp[i][0] = 1 && dp[0][j] = 1`

```java
public class Solution {
    public int uniquePaths(int m, int n) {
        // the unique path from finish (0, 0) to (m-1, n-1)
        int[][] dp = new int[m][n];
        dp[0][0] = 1;
        for (int i = 1; i < m; i++) {
            dp[i][0] = 1;
        }
        for (int j = 1; j < n; j++) {
            dp[0][j] = 1;
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

### 63. Unique Paths II

> Follow up for "Unique Paths":
>
> Now consider if some obstacles are added to the grids. How many unique paths would there be?
>
> An obstacle and empty space is marked as 1 and 0 respectively in the grid.
>
> [Link](https://leetcode.com/problems/unique-paths-ii/?tab=Description)

就是边界条件需要注意：

- 如果 start 就有阻碍，直接返回 0。
- 如果第一行或第一列某个位置有阻碍，该位置之后的所有格子都填 0。
- `dp[x][y] = dp[x - 1][y] + dp[x][y - 1]`

```java
public class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = obstacleGrid[0][0] == 1 ? 0 : 1;
        if (dp[0][0] == 0) {
            return 0;
        }
        for (int j = 1; j < m; j++) {
            if (obstacleGrid[j][0] == 1 || dp[j - 1][0] == 0) dp[j][0] = 0;
            else dp[j][0] = 1;
        }
        for (int j = 1; j < n; j++) {
            if (obstacleGrid[0][j] == 1 || dp[0][j - 1] == 0) dp[0][j] = 0;
            else dp[0][j] = 1;
        }
        for (int x = 1; x < m; x++) {
            for (int y = 1; y < n; y++) {
                if (obstacleGrid[x][y] == 1) {
                    dp[x][y] = 0;
                } else {
                    dp[x][y] = dp[x - 1][y] + dp[x][y - 1];
                }
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

### 64. Minimum Path Sum

> Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.
>
> [Link](https://leetcode.com/problems/minimum-path-sum/?tab=Description)

```java
public class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        for (int i = 1; i < m; i++) {
            grid[i][0] = grid[i - 1][0] + grid[i][0];
        }
        for (int i = 1; i < n; i++) {
            grid[0][i] = grid[0][i - 1] + grid[0][i];
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                grid[i][j] = Math.min(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];
            }
        }
        return grid[m - 1][n - 1];
    }
}
```

### 70. Climbing Stairs

> You are climbing a stair case. It takes n steps to reach to the top.
>
> Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
> [Link](https://leetcode.com/problems/climbing-stairs/?tab=Description)

基本思路就是用 dp 做，不过传统 dp 需要建立数组，比较费时间，这道题可以不通过数组，只需要几个变量就行了。

dp solution without an array:

```java
public class Solution {
    public int climbStairs(int n) {
        if (n <= 0) return 0;
        if (n == 1) return 1;
        if (n == 2) return 2;

        int before2 = 1, before1 = 2, res = 0;
        for (int i = 3; i <= n; i++) {
            res = before1 + before2;
            before2 = before1;
            before1 = res;
        }

        return res;
    }
}
```

dp solution with an array:

```java
public class Solution {
    public int climbStairs(int n) {
        if (n <= 0) return 0;
        if (n == 1) return 1;
        if (n == 2) return 2;
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

### 72. Edit Distance

> Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)
>
> You have the following 3 operations permitted on a word:
>
>a) Insert a character  
>b) Delete a character  
>c) Replace a character

`dp[i][j]` 代表 `word1 (1...i)` 和 `word2 (1...j)` 之间的差别：

- if `c == d`, then : `f[i][j] = f[i-1][j-1]`
- if we replaced c with d: `f[i][j] = f[i-1][j-1] + 1`
- if we added d after c: `f[i][j] = f[i][j-1] + 1`
- if we deleted c: `f[i][j] = f[i-1][j] + 1`

```java
public class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i <= m; i++) {
            dp[i][0] = i;
        }
        for (int i = 0; i <= n; i++) {
            dp[0][i] = i;
        }
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.min(Math.min(dp[i][j - 1] + 1, dp[i - 1][j] + 1), dp[i - 1][j - 1] + 1);
                }
            }
        }
        return dp[m][n];
    }
}
```

### 91. Decode Ways

[Link](https://leetcode.com/problems/decode-ways/?tab=Description)

很复杂的一道题，尽管思路简单用 dp 做，但是边界条件**复杂**，需要好好考虑。

考虑前两位字符：

- 以`0`开始的字符串都不符合要求
- `10, 20` 有且只有一种可能
- `30, 40, 50...` 等不符合要求
- 其余数字 `11, 12, 13, ...` 有两种可能

考虑接下来的数字:

- 当前数字为`0`
    - if 前一个数字为`0`或者`>3`，不符合要求，`return 0`
    - else 有且只有一种可能，`dp[i] == dp[i - 2]`
- 当前数字不为`0`
    - if 前一个数字为`0`或者`两者加起来>=27`，有且只有一种可能，`dp[i] == dp[i - 1]`
    - else 有两种可能，`dp[i] == dp[i - 2] + dp[i - 1]`

字符比较大小：`Character.compare(x, y)`，如果`x > y`，返回1
字符串比较大小：`string1.compareTo(string2)`，如果`string1 > string2`，返回1.

```java
public class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0) return 0;
        if (s.charAt(0) == '0') return 0;
        int len = s.length();
        if (len == 1) return 1;
        if (len == 2) {
            return deter2(s);
        }
        int before2 = 1;
        int before1 = deter2(s);
        if (before1 == 0) {
            return 0;
        }
        int res = 0;
        for (int i = 2; i < s.length(); i++) {
            if (s.charAt(i) == '0') {
                if (s.charAt(i - 1) == '0' || Character.compare(s.charAt(i - 1), '3') >= 0) {
                    return 0;
                } else {
                    res = before2;
                }
            } else {
                if (s.charAt(i - 1) == '0' || s.substring(i - 1, i + 1).compareTo("26") > 0) {
                    res = before1;
                } else {
                    res = before1 + before2;
                }
            }
            before2 = before1;
            before1 = res;
        }
        return res;
    }

    public int deter2(String s) {
        if (s.charAt(1) == '0') {
            if (Character.compare(s.charAt(0), '3') >= 0) {
                return 0;
            } else {
                return 1;
            }
        } else if (s.substring(0, 2).compareTo("11") >= 0 && s.substring(0, 2).compareTo("26") <= 0) return 2;
        else return 1;
    }
}
```

### 121. Best Time to Buy and Sell Stock

> Say you have an array for which the ith element is the price of a given stock on day i.
>
> If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.

简单的题，每次循环记录`(0, i)`上最小的值，再把当前值和最小值相减，与最大值作比较，保留较大者。

```java
public class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) {
            return 0;
        }
        int min = Math.min(prices[0], prices[1]);
        int max = Math.max(0, prices[1] - prices[0]);
        for (int i = 2; i < prices.length; i++) {
            max = Math.max(max, prices[i] - min);
            min = Math.min(min, prices[i]);
        }
        return max;
    }
}
```

### 123. Best Time to Buy and Sell Stock III

> Say you have an array for which the ith element is the price of a given stock on day i.
>
> Design an algorithm to find the maximum profit. You may complete at most two transactions.

这道题个人思路是先找出最大的减区间`(i, j)`，`result = maxSum(0, i) + maxSum(j, end)`，思路应该对的，但实现起来一如既往的复杂。

看了看 Top solution 又是一行行不知道怎么想出来的代码，给个思路啊！

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int hold1 = Integer.MIN_VALUE, hold2 = Integer.MIN_VALUE;
        int release1 = 0, release2 = 0;
        for (int i : prices) {                         // Assume we only have 0 money at first
            release2 = Math.max(release2, hold2 + i);  // The maximum if we've just sold 2nd stock so far.
            hold2 = Math.max(hold2, release1 - i);     // The maximum if we've just buy  2nd stock so far.
            release1 = Math.max(release1, hold1 + i);  // The maximum if we've just sold 1nd stock so far.
            hold1 = Math.max(hold1, -i);               // The maximum if we've just buy  1st stock so far. 
        }
        return release2; // Since release1 is initiated as 0, so release2 will always higher than release1.
    }
}
```

自己方案，没跑出来，先列着。//TODO

```java
public class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;
        int[] gap = new int[prices.length];
        gap[0] = 0;
        int begin = 0, end = 0, maxGap = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] - prices[i - 1] < 0) 
                gap[i] = gap[i - 1] + prices[i] - prices[i - 1];
            else 
                gap[i] = gap[i - 1];
            maxGap = Math.min(maxGap, gap[i]);
        }
        for (int i = 1; i < gap.length; i++) {
            if (gap[i] < 0 && gap[i - 1] >= 0) 
                begin = i - 1;
            if (gap[i] == maxGap) {
                end = i;
                break;
            }
        }
        
        return findSum(begin, end, prices, gap);
    }
    
    public int findSum(int begin, int end, int[] prices, int[] gap) {
        int max1 = 0, max2 = 0;
        if (begin == 0 && end == 0) {
            max1 = 0;
        } else if (begin == 0 && end != 0) {
            max1 = prices[0];
        } else {
            gap[0] = 0;
            for (int i = 1; i <= begin; i++) {
                if (prices[i] - prices[i - 1] > 0) 
                    gap[i] = gap[i - 1] + prices[i] - prices[i - 1];
                else 
                    gap[i] = gap[i - 1];
                max1 = Math.max(max1, gap[i]); 
            }
        }
        if (end == gap.length - 1) {
            max2 = prices[end];
        } else {
            gap[end] = 0;
        for (int i = end + 1; i < gap.length; i++) {
            if (prices[i] - prices[i - 1] > 0) 
                gap[i] = gap[i - 1] + prices[i] - prices[i - 1];
            else 
                gap[i] = gap[i - 1];
            max2 = Math.max(max2, gap[i]); 
        }
        }
        return max1 + max2;
    }
}
```