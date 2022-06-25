# 279. Perfect Squares
## Problem (Medium):
Given an integer n, return the least number of perfect square numbers that sum to n.

A perfect square is an integer that is the square of an integer; in other words, it is the product of some integer with itself. For example, 1, 4, 9, and 16 are perfect squares while 3 and 11 are not.

```
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
```
## Solution:
### Brute Force:
list all square numbers and find the combination that has the least number of combinations.
$numSquares(n)=min(numSquares(n-k) + 1)$  
$∀k∈{square numbers}$

### DP:
The current solution is based on positions of $i-1, i-4, i-9...i-k^2$. We can save the the solution of each number smaller than n. Seach for each position with following state transition function: $dp[i] = 1 + min(dp[i-1], dp[i-4], dp[i-9], ...$

Time Complexity: $\Omicron(n*\sqrt n)$

Space Complexity: $\Omicron(n)$
```
class Solution {
    public int numSquares(int n) {
        // DP
        // state transition function: dp[i] = 1 + min(dp[i-1], dp[i-4], dp[i-9], ...)
        int[] dp = new int[n+1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        // dp[0] is fictional element and used as base case.
        dp[0] = 0;
        
        for (int i=1; i<=n; i++) {
            for (int j=1; j*j<=i; j++) {
                dp[i] = Math.min(dp[i], dp[i-j*j]+1);
            }
        }
        return dp[n];
    }
}
```

# 91. Decode Ways
## Problem (Medium):
A message containing letters from A-Z can be encoded into numbers using the following mapping:
```
'A' -> "1"
'B' -> "2"
...
'Z' -> "26"
```
To decode an encoded message, all the digits must be grouped then mapped back into letters using the reverse of the mapping above (there may be multiple ways). For example, "11106" can be mapped into:

"AAJF" with the grouping (1 1 10 6)
"KJF" with the grouping (11 10 6)
Note that the grouping (1 11 06) is invalid because "06" cannot be mapped into 'F' since "6" is different from "06".

Given a string s containing only digits, return the number of ways to decode it.

The test cases are generated so that the answer fits in a 32-bit integer.
```
Input: s = "226"
Output: 3
Explanation: "226" could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```
## Solution:
### Brute Force (with recursion)
Time Complexity: $\Omicron(2^n)$

Space Complexity: $\Omicron(1)$

```
class Solution {
    public int numDecodings(String s) {
        // brute force
        int count = numDec(s);
        return count;
    }
    
    private int numDec(String s) {
        if (s.length()==0 || s.startsWith("0")) {
            return 0;
        }
        
        if (s.length()==1) {
            if (s.equals("0")) {
                return 0;
            }
            else {
                return 1;
            }
        }
        
        if (!(s.startsWith("2") || s.startsWith("1")) || (s.startsWith("2") && s.charAt(1) > '6'))  {
            return numDec(s.substring(1));
        }
        
        if (s.length()==2) {
            return 1 + numDec(s.substring(1));
        }
        
        return numDec(s.substring(2)) + numDec(s.substring(1));
    }
}
```

### DP with memoization:
We go from end to start. For each position dp(i) = dp(i-1) + dp(i-2), if the current 1 digit or 2 digits are valid. 
1. Many boundaries need to be took care of. 
2. The following codes can be simplified with two variables, prev & curr. Actually there is no need use array. 

Time Complexity: $\Omicron(n)$

Space Complexity: $\Omicron(n)$
```
class Solution {
    public int numDecodings(String s) {
        // DP
        // dp to memorize how many combinations at each position
        int len = s.length();
        int[] dp = new int[len+1];
        
        // fictional elements as based case
        dp[len] = 1;
        
        if (s.charAt(len-1) == '0') {
            dp[len-1] = 0;
        } else {
            dp[len-1] = 1;
        }
        
        for (int i=len-2; i>=0; i--) {
            int count = 0;
            String tmp = s.substring(i);
            if (!tmp.startsWith("0")) {
                count = dp[i+1];
            }
            if ((tmp.startsWith("2") && tmp.charAt(1)<='6') || tmp.startsWith("1")) {
                count = count + dp[i+2];
            }
            dp[i] = count;
        }
        return dp[0];
    }
}
```

# 139. Word Break
## Problem (Medium):
Given a string s and a dictionary of strings wordDict, return true if s can be segmented into a space-separated sequence of one or more dictionary words.

Note that the same word in the dictionary may be reused multiple times in the segmentation.
```
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```
## Solution:
### DP:
1. substring in java takes $\Omicron(n)$.
2. substring(i,j) takes substring from i before j. j is not included.
3. save at each end index whether it can break or not. If the end index is i, word length is n, whether i-n is break or not will influence the current index.
4. if dp[i] is already set to true. It is equal to visited, just skip it.
   
Time Complexity: $\Omicron(n^3)$

Space Complexity: $\Omicron(n)$

```
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        // DP: save at each end index whether it can break or not. 
        // if the end index is i, word length is n, 
        // whether i-n is break or not will influence the current index status.
        boolean[] dp = new boolean[s.length()+1];
        Arrays.fill(dp, false);
        // fictional element
        dp[0] = true;
        
        for (int i=0; i<s.length(); i++) {
            for (int j=i+1; j<s.length()+1; j++) {
                // substring takes O(n) in java
                // if dp[j] is already set to true, ignore it, because it has its way to break.
                if (!dp[j] && wordDict.contains(s.substring(i,j))) {
                    dp[j] = dp[i];
                }
            }
        }
        return dp[s.length()];
    }
}
```