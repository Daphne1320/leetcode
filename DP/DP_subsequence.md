# 300. Longest Increasing Subsequence
## Problem (Medium):
Given an integer array nums, return the length of the longest strictly increasing subsequence.

A subsequence is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, [3,6,2,7] is a subsequence of the array [0,3,1,6,2,2,7].

```
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
```
## Solution:
### DP:
1. For subsequence problem, dp[i] marks the properties of subsequence ends with i. At last, go through dp again to get the final result.
2. dp[i] = max(dp[j]+1, dp[i]), for every j<i

Time Complexity: $\Omicron(n*n)$

Space Complexity: $\Omicron(n)$

```
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums.length==0) {
            return 0;
        }
        // DP: length of current state i is dp[i]
        // dp[i] = max(dp[j]+1, dp[i]), for every j<i
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        
        for (int i=1; i<nums.length; i++) {
            for (int j=0; j<i; j++) {
                if (nums[i]>nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j]+1);
                }
            }
        }
        
        int max = 1;
        for (int i=0; i<nums.length; i++) {
            max = Math.max(dp[i], max);
        }
        return max;
    }
}
```

### Greedy Algorithm + Binary Search

1. try to build longest sub sequence: 
2. if the new element is greater than the last element in the sub sequnece, just add it at last
3. else, find the first element in sub sequence that is larger than current new element, swap them.
4. In this way, we try to leave larger space for other lefting elements.
   
Time Complexity: $\Omicron(N*log(N))$, binary search takes logN.

Space Complexity: $\Omicron(N)$

```
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums.length==0) {
            return 0;
        }
        // Greedy algorithm
        // try to build longest sub sequence: 
        // if the new element is greater than the last element in the sub sequnece, just add it at last
        // else, find the first element in sub sequence that is larger than current new element, swap them.
        // In this way, we try to leave larger space for other lefting elements.
        
        List<Integer> sortedSeq = new ArrayList<>();
        sortedSeq.add(nums[0]);
        for (int i=1; i<nums.length; i++) {
            if (nums[i]>sortedSeq.get(sortedSeq.size()-1)) {
                sortedSeq.add(nums[i]);
            } else {
                // improve the search with binary search
                int j = binarySearch(sortedSeq, nums[i]);
                sortedSeq.set(j, nums[i]);
            }
        }
        
        return sortedSeq.size();
    }
    
    private int binarySearch(List<Integer> arr, int target) {
        // find the first equal or larger element
        int left = 0;
        int right = arr.size()-1;
        
        while (left<=right) {
            int mid = (right-left)/2 + left;
            if (arr.get(mid) == target) {
                return mid;
            } else if (arr.get(mid)>target) {
                // 1, 2, 4, 6  ==> 3
                right = mid-1;
            } else {
                left = mid+1;
            }
        }
        
        return left;
    }
}
```

# 1143. Longest Common Subsequence
## Problem (Medium):
Given two strings text1 and text2, return the length of their longest common subsequence. If there is no common subsequence, return 0.

A subsequence of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

For example, "ace" is a subsequence of "abcde".
A common subsequence of two strings is a subsequence that is common to both strings.

```
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.
```
## Solution:
### Brute Force:
Iterate every subsequence of first string and check whether or not it is also one subsuquence of the second one.

Time Complexity: $\Omicron(2^L)$ L is the length of the string. Because, for each character, it has two options: it belongs to subsequence or it does not belong to subsequence.

### DP:

1. create one 2d array dp. dp[i][j] refers to till the position of i in first string & position of j in the second string, the number of longest common subsequence.
2. The following codes can be improved by using two 1D array instead of 2D. We only need to keep tracking of last two columns. One array to mark results of last row, another array to mark results of current row.

Time Complexity: $\Omicron(M*N)$
Space Complexity: $\Omicron(M*N)$

```
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int[][] dp = new int[text1.length()+1][text2.length()+1];
        
        for (int row=1; row<=text1.length(); row++) {
            for (int col=1; col<=text2.length(); col++) {
                if (text1.charAt(row-1) == text2.charAt(col-1)) {
                    dp[row][col] = 1 + dp[row-1][col-1];
                } else {
                    dp[row][col] = Math.max(dp[row-1][col], dp[row][col-1]);
                }
            }
        }
        
        return dp[text1.length()][text2.length()];
    }
}
```