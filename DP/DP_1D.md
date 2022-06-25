# 70. Climbing Stairs
## Problem (Easy):
You are climbing a staircase. It takes n steps to reach the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
```
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```
## Solution:
### Brute Force:
Use recursion, we go through all possible combinations, which means 1 and 2 at each step.

Time Complexity: $\Omicron(2^n)$

Space Complexity: $\Omicron(n)$ The depth of recursion tree can go to n.

```
class Solution {
    public int climbStairs(int n) {
        // brute force
        return climb(0, n);
    }
    
    private int climb(int length, int target) {
        if (length==target) return 1;
        
        if (length>target) return 0;
        
        return climb(length+1, target) + climb(length+2, target);
    }
}
```
### Recursion with memoization:
In brute force solution, current result at many points are caculated multiple times. We save the result in one array to save time.

Time Complexity: $\Omicron(n)$ The depth of recursion tree can go to n.
Space Complexity: $\Omicron(n)$ The depth of recursion tree can go to n.

```
class Solution {
    public int climbStairs(int n) {
        // recursion with memoization
        int[] memo = new int[n+1];
        return climb(0, n, memo);
    }
    
    private int climb(int length, int target, int[] memo) {    
        if (length==target) return 1;
        
        if (length>target) return 0;
        
        if (memo[length]>0) {
            return memo[length]; 
        }
        memo[length] = climb(length+1, target, memo) + climb(length+2, target, memo);
        return memo[length];
    }
}
```

### DP:
State Transition Equation: dp(i) = dp(i-1) + dp(i-2)

```
class Solution {
    public int climbStairs(int n) {
        // DP
        // dp(i) = dp(i-1) + dp(i-2)
        if (n==1) {
            return 1;
        }
        int[] dp = new int[n+1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i=3; i<=n; i++) {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
}
```

# 198. House Robber
## Problem (Medium):
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.
```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```
## Solution:
### DP:
Note: the key point is finding state transition function. We can solve the current state problem based on results of prior state. In addition, start state can be got as base. 

Recusion is one method to implement DP.

Time Complexity: $\Omicron(n)$

Space Complexity: $\Omicron(n)$
```
class Solution {
    public int rob(int[] nums) {
        // edge case
        if (nums.length==1) {
            return nums[0];
        }
        // DP 
        // max[i] = max(max[i-1], max[i-2]+nums[i])
        int[] max = new int[nums.length];
        max[0] = nums[0];
        max[1] = Math.max(nums[0], nums[1]);
        
        for (int i=2; i<nums.length; i++) {
            max[i] = Math.max(max[i-1], max[i-2]+nums[i]);
        }
        return max[nums.length-1];
    }
}
```
### improved DP:

Note: To calculate the current value, we just need to rely on the next two consecutive values/recursive calls.

Time Complexity: $\Omicron(n)$

Space Complexity: $\Omicron(1)$

```
class Solution {
    public int rob(int[] nums) {
        // edge case
        if (nums.length==1) {
            return nums[0];
        }
        // DP 
        // max[i] = max(max[i-1], max[i-2]+nums[i])
        
        int max_pre_pre = nums[0];
        int max_pre = Math.max(nums[0], nums[1]);
        
        for (int i=2; i<nums.length; i++) {
            int tmp = max_pre;
            max_pre = Math.max(max_pre, max_pre_pre+nums[i]);
            max_pre_pre = tmp;
        }
        return max_pre;
    }
}
```

# 413. Arithmetic Slices
## Problem (Medium):
An integer array is called arithmetic if it consists of at least three elements and if the difference between any two consecutive elements is the same.

For example, [1,3,5,7,9], [7,7,7,7], and [3,-1,-5,-9] are arithmetic sequences.
Given an integer array nums, return the number of arithmetic subarrays of nums.

A subarray is a contiguous subsequence of the array.

```
Input: nums = [1,2,3,4]
Output: 3
Explanation: We have 3 arithmetic slices in nums: [1, 2, 3], [2, 3, 4] and [1,2,3,4] itself.
```
## Solution:
### Brute Force:
Note: find all slices, which contain more than 3 elements. Check them if any two consecutive elements in the slice have same difference. 
Subarray (slice) must contain more than 3 elements.

Time Complexity: $\Omicron(n^2)$
Space Complexity: $\Omicron(1)$ Only several constant are used.

```
class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        int count = 0;
        // edge case
        if (nums.length<3) {
            return count;
        }
        // Brute Force
        // nums[i] - nums[i-1] = nums[i+1] - nums[i]
        for (int start=0; start<nums.length-2; start++) {
            int distance = nums[start+1] - nums[start];
            for (int end=start+2; end<nums.length; end++) {
                int nextDistance = nums[end] - nums[end-1];
                if (distance==nextDistance) {
                    count++;
                } else {
                    break;
                }
            }
        }
        return count;
    }
}
```

### DP:
Note: by adding one element in existing valid slide, we are adding n+1 possibilities of slides more in the total count. 
Time Complexity: $\Omicron(n)$

Space Complexity: $\Omicron(1)$

```
class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        int count = 0;
        // edge case
        if (nums.length<3) {
            return count;
        }
        // DP
        // nums[i] - nums[i-1] = nums[i+1] - nums[i]
        int d_pre = nums[1] - nums[0];
        int currCount = 0;
        for (int start=2; start<nums.length; start++) {
            int d_curr = nums[start] - nums[start-1];
            if (d_curr==d_pre) {
                // everytime by adding one element more, currCount+1 of slices are added. 
                // 1, 2, 3...i: by adding i+1,
                // original: (1, i), (2, i), (3. i) ... : n slides
                // now: (2, i+1), (3, i+1), (4, i+1).... + (1, i+1): n+1 slides 
                currCount++;
                count = count + currCount;
            } 
            else {
                currCount = 0;
            }
            d_pre = d_curr;
        }
        return count;
    }
}
```
