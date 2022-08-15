# 739. Daily Temperatures
## Problem (Medium):
Given an array of integers temperatures represents the daily temperatures, return an array answer such that answer[i] is the number of days you have to wait after the $i^{th}$ day to get a warmer temperature. If there is no future day for which this is possible, keep answer[i] == 0 instead.
```
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
```

## Solution:

### Monotonic Stack (Descreasing)

Algorithm

1. Initialize an array answer with the same length as temperatures and all values initially set to 0. Also, initialize a stack as an empty array.

2. Iterate through temperatures. At each index currDay:

    * If the stack is not empty, that means there are previous days for which we have not yet seen a warmer day. While the current temperature is warmer than the temperature of prevDay (the index of the day at the top of the stack): <br />
    Set answer[prevDay] equal to the number of days that have passed between prevDay and the current day, that is, answer[prevDay] = currDay - prevDay.<br />
    kick prevDay out of the stack. Go to check the next one in stack.
    * Push the current index currDay onto the stack.
3. Return answer.


Time Complexity: $\Omicron(n)$ <br />
An easier way to think about this is that in the worst case, every element will be pushed and popped once: $\Omicron(2 \cdot n) = \Omicron(n)$


Space Complexity: $\Omicron(n)$ <br />
If the input was non-increasing, then on element would ever be popped from the stack, and the stack will be a size of N elements at the end.

```
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        Stack<Integer> stack = new Stack<Integer>();
        int[] res = new int[temperatures.length];
        
        for(int i=0; i<temperatures.length; i++) {
            while (!stack.empty() && temperatures[stack.peek()]<temperatures[i]) {
                int pos = stack.pop();
                res[pos] = i - pos;
            }
            stack.push(i);
        }
        
        return res;
    }
}
```