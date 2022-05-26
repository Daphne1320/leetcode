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