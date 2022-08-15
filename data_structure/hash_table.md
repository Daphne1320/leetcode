# Easy
## 1. Two Sum
### Problem:
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.
### Solution:
#### Brute Force:
Use two for loops
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i=0; i<nums.length; i++) {
            for (int j=i+1; j<nums.length; j++){
                if((nums[i] + nums[j]) == target) {
                    return new int[] {i, j};
                }
            } 
        }
      return null;      
    }
}
```
#### Use Hash Table (Time complexity n)
Time Complexity:n => tranverse array only once, each loopup in hashmap take 1.
Space Complexity:n=> save at most n elements in hashmap.
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> numbers = new HashMap<Integer, Integer>();
        
        // put values in nums into hashtable
        for (int idx=0; idx<nums.length; idx++) {
            int rest = target - nums[idx];
            int value = nums[idx];
            
            if (numbers.get(value)!=null) return new int[]{numbers.get(value),idx};
            
            numbers.put(rest, idx);
        }
        // if no solution
        return new int[] {-1,-1};
    }
}
```

# 560. Subarray Sum Equals K
## Problem (Medium):
Given an array of integers nums and an integer k, return the total number of subarrays whose sum equals to k.

A subarray is a contiguous non-empty sequence of elements within an array.

```
Input: nums = [1,1,1], k = 2
Output: 2
```
## Solution:
### Brute Force + Cumulative Sum
calculate the sum for every subarray.
```
class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
    
        for (int i=0; i<nums.length; i++) {
            int sum = 0;
            for (int j=i; j<nums.length; j++) {
                sum = sum + nums[j];
                if (sum==k) {
                    count ++;
                }
            }
        }
        
        return count;
    }
}
```

Time Complexity: $\Omicron(n^2)$ <br />
Space Complexity: $\Omicron(1)$ <br />

### Hashmap (Tricky!!!)
* $sum[i] = sum[i] - sum[0]$
* $sum[i] - sum[j] = k$ refers to the sum-up of array from index i till index j.
* Therefore, if $sum[j] = sum[i] - k$ is available, there is one index pair $i, j$ exist for sum-up == k. 
* Similar as Two-Sum Leetcode problem, we can use hashmap to check if $sum[j]$ is available in our database.

Note:
* Init pair (0,1) should be added in hashmap for case  $sum[i] = k$
* i != j

Time Complexity: $\Omicron(n)$ <br />
Space Complexity: $\Omicron(n)$ <br />

```
class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
    
        // sum[i] - sum[j] = k
        // use hashmap to save sum[i] as key, times of sum[i] as value
        HashMap<Integer, Integer> sums = new HashMap<Integer, Integer>();
        int sum = 0;
        // init
        sums.put(0,1);
        for (int i=0; i<nums.length; i++) {
            sum += nums[i];
            if (sums.get(sum-k) != null) {
                count += sums.get(sum-k);
            }
            int freq = sums.getOrDefault(sum, 0) + 1;
            sums.put(sum, freq);
        }
        
        return count;
    }
}
```
