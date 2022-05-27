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

# Easy
## 88. Merge Sorted Array
### Problem:
You are given two integer arrays nums1 and nums2, sorted in non-decreasing order, and two integers m and n, representing the number of elements in nums1 and nums2 respectively.

Merge nums1 and nums2 into a single array sorted in non-decreasing order.

The final sorted array should not be returned by the function, but instead be stored inside the array nums1. To accommodate this, nums1 has a length of m + n, where the first m elements denote the elements that should be merged, and the last n elements are set to 0 and should be ignored. nums2 has a length of n.
```
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.
```
### Idea:
### Note:
### Solution:
#### Brute Force
copy nums2 into nums1. Then sort with mergeSort or QuickSort.
Time Complexity: n+(n+m)log(n+m) [built-in sort is nlogn]
```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // copy nums2 into nums1
        for (int i=m; i<m+n; i++) {
            nums1[i] = nums2[i-m];
        }
        Arrays.sort(nums1);
    }
}
```
#### Two Pointers
Time Complexity: n+m
Space Complexity: 1 (no extra array needed)
for loop is better than while. It will decrease pos automatically for each iteration.
```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // each number has one pointer, which starts from right to left, from biggerst to smallest number
        // third pointer is needed to mark the current position in nums1
        int idx1 = m-1;
        int idx2 = n-1;
        for(int pos=m+n-1; pos>=0; pos--) {
            if (idx2<0) break;
            if (idx1>=0 && nums1[idx1]>nums2[idx2]) nums1[pos] = nums1[idx1--];
            else nums1[pos] = nums2[idx2--];
        }
    }
}
```
