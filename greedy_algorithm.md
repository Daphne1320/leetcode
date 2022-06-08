# Easy
## 455. Assign Cookies

### Problem:
Assume you are an awesome parent and want to give your children some cookies. But, you should give each child at most one cookie.

Each child i has a greed factor g[i], which is the minimum size of a cookie that the child will be content with; and each cookie j has a size s[j]. If s[j] >= g[i], we can assign the cookie j to the child i, and the child i will be content. Your goal is to maximize the number of your content children and output the maximum number.

 

Example 1:
```
Input: g = [1,2,3], s = [1,1]
Output: 1
Explanation: You have 3 children and 2 cookies. The greed factors of 3 children are 1, 2, 3. 
And even though you have 2 cookies, since their size is both 1, you could only make the child whose greed factor is 1 content.
You need to output 1.
```
### Ideas:
According to Greedy Algorithm, always try to satisfy the child with smallest greed factor. Because he/she is easily to satisfty. 

### Notes:
Java has no pointers. We can create two int variables as pointer to indicate the position of each array. Time complexity should be n.  

### Solution:
```
class Solution {
    //g: children, s: cookies
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        
        // start from children with smallest greed factor
        int i = 0;
        int j = 0;
        while (i<g.length && j<s.length) {
            if (g[i] <= s[j]) {
                i++;
                j++;
            } else j++;
        }
        
        return i;
    }
}
```

# Hard
## 135. Candy
### Problem:
There are n children standing in a line. Each child is assigned a rating value given in the integer array ratings.

You are giving candies to these children subjected to the following requirements:

1. Each child must have at least one candy.
Children with a higher rating get more candies than their neighbors.
2. Return the minimum number of candies you need to have to distribute the candies to the children.

Example 1:
```
Input: ratings = [1,0,2]
Output: 5
Explanation: You can allocate to the first, second and third child with 2, 1, 2 candies respectively.
```
### Idea: Try to update from left to right then right to left. Greedy Algorithm is used as updating one direction each time.
### Note:
### Solution:
#### Brute Force (Time Out: n^2): 
go through array from left to right, but each time will only increase 1 for each element. If one child should get n candies, the counters will be updated n times (each time + 1). Therefore time complexity is n^2. One array is used, space complexity is n.  

```
class Solution {
    public int candy(int[] ratings) {
        //number of candies
        int[] candies = new int[ratings.length];
        // because each child has minimum 1 candy
        Arrays.fill(candies, 1);
        
        boolean hasChanged = true;
        while(hasChanged) {
            hasChanged = false;
            for(int i=0; i<ratings.length; i++) {                
              if (i<ratings.length-1 && ratings[i]>ratings[i+1]&&candies[i]<=candies[i+1]) {
                  candies[i] = candies[i+1] + 1;
                  hasChanged = true;
              }
              if (i>0 && ratings[i]>ratings[i-1]&&candies[i]<=candies[i-1]) {
                  candies[i] = candies[i-1] + 1;
                  hasChanged = true;
              }
            }
        }
        return Arrays.stream(candies).sum();
    }
}
```
#### One Array loop two times

```
class Solution {
    public int candy(int[] ratings) {
        //number of candies
        int[] candies = new int[ratings.length];
        // because each child has minimum 1 candy
        Arrays.fill(candies, 1);
        
        // go from left to right, compare to left variable
        for (int i=1; i<ratings.length; i++) {
            if (ratings[i]>ratings[i-1] && candies[i]<=candies[i-1]) candies[i] = candies[i-1] + 1;
        }
        
        // go from right to left, compare to right variable
        for (int i=ratings.length-2; i>=0; i--) {
            if (ratings[i]>ratings[i+1] && candies[i]<=candies[i+1]) candies[i] = candies[i+1] + 1;
        }
        
        return Arrays.stream(candies).sum();
    }
}
```

# 435. Non-overlapping Intervals
## Problem (Medium):
Given an array of intervals intervals where intervals[i] = [starti, endi], return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

```
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.
```
## Solution:
### BruteForce:
Calcualte all combinations of intervals, then check whether each combination contains overlapping intervals. Get the combination which has no overlapp and max. number.

Time Complexity: O(2^n), for n elements, the total number of all combinations are 2^n.
Space Complexity: depends on which method is used.

Not implemented, because too complex.

### GreedyAlgorithm
There are actually two kinds of method using greedy algorithm. One is sort the intervals by start point, the second is sort intervals by end point. Both are similar, key point is always select the smaller end to get more space for remaining intervals. 

Here implements sort by start method.

Notes:
1. user Comparator to sort arrays.
2. move current pointer directly to next pointer. Dont use current ++. Becaues, it is possible next element of current is already skipped by next pointer. 

```
class Solution {
    // Greedy Algorithm
    // sort start point
    class MyComparator implements Comparator<int[]> {
        @Override
        public int compare(int[]a, int[]b) {
            return a[0] - b[0];
        }
    }
    
    public int eraseOverlapIntervals(int[][] intervals) {
        MyComparator comparator = new MyComparator();
        
        Arrays.sort(intervals, comparator);
        
        // go through the array to find out which intervals will be removed
        int count = 0;
        int current = 0;
        int next = 1;
        while(current<intervals.length && next<intervals.length) {
            // case1: current interval has no intersection with next
            // end of current is smaller or equal to start of next
            if(intervals[current][1]<=intervals[next][0]) {
                current=next;
                next++;
            }
            // case2: current interval has intersection, next has smaller end, select next, remove current
            // end of current is bigger to start of next, end of next is smaller or equal to end of current
            else if (intervals[next][1]<=intervals[current][1]) {
                current=next;
                next++;
                count++;
            } else {
                // case3: current interval has interseciotn, next has larger end, select current, remove next
                next++;
                count++;
            }
            
        }
        return count;
    }
}
```

### DP based on starting/ending point. 
TODO, as DP is not learned yet. 