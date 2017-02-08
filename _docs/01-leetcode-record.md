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
