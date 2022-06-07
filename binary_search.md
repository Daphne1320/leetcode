
# 69. Sqrt(x)
## Problem (Easy):
Given a non-negative integer x, compute and return the square root of x.

Since the return type is an integer, the decimal digits are truncated, and only the integer part of the result is returned.

Note: You are not allowed to use any built-in exponent function or operator, such as pow(x, 0.5) or x ** 0.5.
```
Input: x = 8
Output: 2
```
## Solution:
find x for x^2 - a = 0
find x between interval 0 and a
because f(0) = -a <=0 f(a) = a(a-1) >= 0, f(x) is monotonically increasing, we can find one result make x^2 - a = 0.
### Brute Force:
loop over from 0 to x.
### Binary search:
time complexity: O(logn)
space complexity: O(1)
notes: 1. while condition. 2. return which value. 3. be carefull with dead loop (timeout).
```
class Solution {
    // binary search in 0-x
    public int mySqrt(int x) {
    
        int left = 0;
        int right = x;
        int mid = 0;
        
        while(left<=right) {
            mid = left + (right-left)/2;
            // notes: x*x might exceed max value of integer
            long target = (long) mid*mid;
            if(target==x) return mid;
            else if (target>x) {
                right = mid-1; // stop at equal target or first smaller than target position
            }else left=mid+1; // stop at equal targe or first larger than target position
        }  
        return right;
    }
}
```
