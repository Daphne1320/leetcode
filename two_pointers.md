# 167. Two Sum II - Input Array Is Sorted
## Problem (Medium):
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
## Solution:
### Two pointers:
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
### Binary Search combined with two pointer:
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

# 88. Merge Sorted Array
## Problem (Easy):
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
## Solution:
### Brute Force
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
### Two Pointers
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

#  142. Linked List Cycle II
## Problem (Medium):
Given the head of a linked list, return the node where the cycle begins. If there is no cycle, return null.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to (0-indexed). It is -1 if there is no cycle. Note that pos is not passed as a parameter.

Do not modify the linked list.
Picture: https://leetcode.com/problems/linked-list-cycle-ii/ 
```
Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```
```
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
 ```
### Idea:
### Note:
## Solution:
### Brute Force:
use hash set/map to store node. Disadvantages: if there are too many nodes, the hash set/map will be extrem large. In addition, hash set is enought. We dont need store key-value pair.
Time Complexity: n (visit exactly n node in cyclic and acyclic situations.)
Space complexity: n (new Set contains n elements)
```
public class Solution {
    public ListNode detectCycle(ListNode head) {
        HashSet<ListNode> nodes = new HashSet<ListNode>();
        ListNode node = head;
        int idx = 0;
        
        while(node!=null) {
            if (nodes.contains(node)) return node;
            nodes.add(node);
            node = node.next;
        }
        return null;
    }
}
```
### Floyd's Circle Detection:
https://zhuanlan.zhihu.com/p/105269431
Use one quick pointer and one slow pointer.

Time complexity : O(n)O(n)

For cyclic lists, hare and tortoise will point to the same node after F+C-h iterations, as demonstrated in the proof of correctness. F+C-h <= F+C = n, so phase 1 runs in O(n)) time. Phase 2 runs for F < nF < n iterations, so it also runs in O(n) time.

For acyclic lists, hare will reach the end of the list in roughly n/2 iterations, causing the function to return before phase 2. Therefore, regardless of which category of list the algorithm receives, it runs in time linearly proportional to the number of nodes.

Space Complexity: O(1)
```
public class Solution {
    public ListNode detectCycle(ListNode head) {
        // one quick pointer
        ListNode quick = head;
        // one slow pointer
        ListNode slow = head;
        
        // detect if there is one circle exist
        while(true) {
            if (quick==null) return null;
            if (quick.next==null) return null;
            quick = quick.next.next;
            slow = slow.next;
            if (slow == quick) break;
        }
        
        // find start position of circle
        quick = head;
        while(quick != slow) {
            quick = quick.next;
            slow = slow.next;
        }
        return quick;
    }
}
```

# 76. Minimum Window Substring
## Problem (Hard):
Given two strings s and t of lengths m and n respectively, return the minimum window substring of s such that every character in t (including duplicates) is included in the window. If there is no such substring, return the empty string "".

The testcases will be generated such that the answer is unique.

A substring is a contiguous sequence of characters within the string.

```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.
```
### Notes: 
Too hard for me. Copied answers from solution Approach 1. Suggest do it again after a while.
## Solution:
### Brute Force:
Loop String s from first letter: find the minimum length, which contains string t, and store the length & idx of this letter in hashmap (length as key and idx as value). Then check hashmap.contains(key) from 1 til length of s and find the first available key in hashmap. 
Time Complexity: O(n^2) because loop string s two times
Space Complexity: O(n) hashmap takes O(n) space.

### Two Pointers => sliding window method

Complexity Analysis

Time Complexity: O(|S| + |T|)O(∣S∣+∣T∣) where |S| and |T| represent the lengths of strings SS and TT. In the worst case we might end up visiting every element of string SS twice, once by left pointer and once by right pointer. |T|∣T∣ represents the length of string TT.

Space Complexity: O(|S| + |T|)O(∣S∣+∣T∣). |S|∣S∣ when the window size is equal to the entire string SS. |T|∣T∣ when TT has all unique characters.

```
class Solution {
    public String minWindow(String s, String t) {
        // use two pointers
        int right = 0;
        int left = 0;
        // one hashmap for t
        HashMap<Character, Integer> dictT = new HashMap<Character, Integer>();
        for(int i = 0; i<t.length(); i++) {
            int count = dictT.getOrDefault(t.charAt(i), 0);
            dictT.put(t.charAt(i), count+1);
        }
        // required number of unique characteres in t
        int required = dictT.size();
        
        // one hashmap for s to keeps a count of all the unique characters in the current window
        HashMap<Character, Integer> countS = new HashMap<Character, Integer>();
        
        // formed is used to keep track of how many unique characters in t
        // are present in the current window in its desired frequency.
        // e.g. if t is "AABC" then the window must have two A's, one B and one C.
        // Thus formed would be = 3 when all these conditions are met.
        int formed = 0;
        
        // array to store position and length of current minimum window
        int[] result = {-1,0,0};
        
        while(right < s.length()) {
            char rightChar = s.charAt(right);
            int count = countS.getOrDefault(rightChar, 0);
            countS.put(rightChar, count+1);
            
            if (dictT.containsKey(rightChar) && countS.get(rightChar).intValue() == dictT.get(rightChar).intValue()) {
                formed ++;
            }
            
            // move left pointer in right direction til current window donot contains all t anymore
            while(left <= right && formed == required) {
                // save the samllest result
                if (right-left+1<result[0]|| result[0] == -1) {
                    result[0] = right-left+1;
                    result[1] = left;
                    result[2] = right;
                }
                
                //move left pointer one step right
                char leftChar = s.charAt(left);
                countS.put(leftChar, countS.get(leftChar)-1);
                if (dictT.containsKey(leftChar) && countS.get(leftChar).intValue() < dictT.get(leftChar).intValue()) {
                    formed --;
                }
                
                left++;      
            }
            right++;
        }
        
        return result[0] == -1 ? "" : s.substring(result[1], result[2]+1);
        
    }
}
```