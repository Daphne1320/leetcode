# Medium
## 167. Two Sum II - Input Array Is Sorted
### Problem:
Given a 1-indexed array of integers numbers that is already sorted in non-decreasing order, find two numbers such that they add up to a specific target number. Let these two numbers be numbers[index1] and numbers[index2] where 1 <= index1 < index2 <= numbers.length.

Return the indices of the two numbers, index1 and index2, added by one as an integer array [index1, index2] of length 2.

The tests are generated such that there is exactly one solution. You may not use the same element twice.

Your solution must use only constant extra space.
```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore, index1 = 1, index2 = 2. We return [1, 2].
```

### Idea:
### Note:
### Solution:
#### Two pointers:
As the array is sorted in ascending order, we use one pointer starting from left, one pointer starting from right. Add values saved on them, if smaller than target, left pointer move 1 to right; if bigger than target, right pointer move 1 to left.(Time complexity: n) 
```
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length-1;
        while(true) {
            if (numbers[left] + numbers[right] == target) return new int[] {left+1, right+1};
            else if (numbers[left] + numbers[right] < target) {
                left ++;
            } else right --;
        }
    }
}
```
#### Binary Search combined with two pointer:
I dont think this is a better solution. Time Complexity in worst case is nlogn. nlogn is worser than n, because if n is large, logn is greater than 1. I wrote for practicing binary search.

```
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length-1;
        while(left<right) {
            
            if (numbers[left] + numbers[right] == target) return new int[] {left+1, right+1};
            else if (numbers[left] + numbers[right] <= target) {
                // smaller than target, move left to smallest value which makes sum larger than target
                left = smallestLargerEqualThanTarget(numbers, left, right, target-numbers[right]);
            } else {
                // greater than target, move right to largest value which makes sum smaller than target
                right = largestSmallerEqualThanTarget(numbers, left, right, target-numbers[left]);
            }
        }
        return new int[2];
    }
    
    public int largestSmallerEqualThanTarget(int[] numbers, int left, int right, int target) {
     
        while(left<right) {
            int mid = left+(right-left)/2;
            if (mid==left) break;
            if(numbers[mid]<=target) {
                left = mid;
            } else {
                right = mid;
            }
        }
 
        return left;
    }
    
    public int smallestLargerEqualThanTarget(int[] numbers, int left, int right, int target) {

        while(left<=right) {
            int mid = left+(right-left)/2;
            if (mid==left) break;
            if(numbers[mid]>=target) {
                right = mid;
            } else {
                left = mid;
            }
        }
        return right;
    }
}
```