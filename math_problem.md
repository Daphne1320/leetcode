# 204. Count Primes
## Problem (Medium):
Given an integer n, return the number of prime numbers that are strictly less than n.
```
Input: n = 10
Output: 4
Explanation: There are 4 prime numbers less than 10, they are 2, 3, 5, 7.
```
## Solution:
### Sieve of Eratosthenes
1. 0 & 1 are not prime numbers
2. [sieve of eratosthenes wikipedia](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)
3. iterate from 2 till $\sqrt n$
4. multiples of p from p*p till n. Because every number smaller than p*p has already been checked.

Time Complexity: $\Omicron(\sqrt n (loglogn))$

Space Complexity: $\Omicron(n)$

```
class Solution {
    public int countPrimes(int n) {
        if (n==0 || n==1) {
            return 0;
        }
        boolean[] arr = new boolean[n];
        // 1, 2, 3, 4, 5
        // false refers to prime
        arr[0] = true;
        // 1 is not prime
        arr[1] = true;
        for (int i=2; i<=(int) Math.sqrt(n); i++) {
            if (arr[i]==false) {
                for (int j=i*i; j<n; j+=i) {
                    arr[j] = true;
                }
            }
        }
        
        int count = 0;
        for (int i=2; i<n; i++) {
            if (arr[i]==false) {
                count++;
            }
        }
        return count;
    }
}
```

# 172. Fractorial Trailing Zeroes
## Problem (Medium):
Given an integer n, return the number of trailing zeroes in n!.

Note that n! = n * (n - 1) * (n - 2) * ... * 3 * 2 * 1.

```
Input: n = 3
Output: 0
Explanation: 3! = 6, no trailing zero.
```
## Solution:
### Brute Force:
calculate factorial. It will take large space. We need use BigInteger to save results. As the number is not a fixed-bit-size like int, long..., the multiplications cannnot be considered as O(1). It takes much more longer.

### Count factors of 5
As trailing zero caused by 10 and numbers of 2 are more than numbers of 5. Therefore, we only need count how many 5 while calculating factorial. 

As only 5, 10, 15, 20... contains 5, we go through with step 5.

Time Complexity: $\Omicron(n*logn)$ loop through every fifth number. O(n/5) is equal to O(n). 

Space Complexity: $\Omicron(1)$

```
class Solution {
    public int trailingZeroes(int n) {
        int count = 0;
        
        for (int i=5; i<=n; i+=5) {
            int curr = i;
            // only 5, 10, 15, 20, 25 have 5, therefore go up in steps of 5
            while (curr%5 == 0) {
                count++;
                curr = curr/5;
            }
        }
        
        return count;
    }
}
```

### Improved Count factors of 5
We are counting 5 for 1, 2, 3, 4, 5, 6, 7, 8, 9, 10...n in last approach. The number of 5 is actually $\frac n 5$. As we will get 5*5, 5*5*5 ..., therefore, we also need to get number of them. But for example, for 5*5, as we already count one 5 factor, we only need add one 5 facor for 5*5 (not adding two 5 factor). Therefore:

$res = \frac{n}{5} + \frac{n}{5*5} + \frac{n}{5*5*5} + \frac{n}{5*5*5*5}...$
We can rewrite it as:

$res = \frac{n}{5} + \frac{n/5}{5} + \frac{n/(5*5)}{5} + \frac{n/(5*5*5)}{5}...$

Time Complexity: $\Omicron(logn)$ We divide n by each power of 5: $log_5^n * O(1) = logn$

Space Complexity: $\Omicron(1)$
```
class Solution {
    public int trailingZeroes(int n) {
        int count = 0;
        
       while (n!=0) {
           n /= 5;
           count += n;
       }
        
        return count;
    }
}
```

# 528. Random Pick with Weight
## Problem (Mediun):
You are given a 0-indexed array of positive integers w where w[i] describes the weight of the ith index.

You need to implement the function pickIndex(), which randomly picks an index in the range [0, w.length - 1] (inclusive) and returns it. The probability of picking an index i is w[i] / sum(w).

For example, if w = [1, 3], the probability of picking index 0 is 1 / (1 + 3) = 0.25 (i.e., 25%), and the probability of picking index 1 is 3 / (1 + 3) = 0.75 (i.e., 75%).
```
Input
["Solution","pickIndex","pickIndex","pickIndex","pickIndex","pickIndex"]
[[[1,3]],[],[],[],[],[]]
Output
[null,1,1,1,1,0]

Explanation
Solution solution = new Solution([1, 3]);
solution.pickIndex(); // return 1. It is returning the second element (index = 1) that has a probability of 3/4.
solution.pickIndex(); // return 1
solution.pickIndex(); // return 1
solution.pickIndex(); // return 1
solution.pickIndex(); // return 0. It is returning the first element (index = 0) that has a probability of 1/4.

Since this is a randomization problem, multiple answers are allowed.
All of the following outputs can be considered correct:
[null,1,1,1,1,0]
[null,1,1,1,1,1]
[null,1,1,1,0,0]
[null,1,1,1,0,1]
[null,1,0,1,0,0]
......
and so on.
```
## Solution:
### Brute Force:
We sum up all weights together, then create one array with length of this sum-up. We go through input array, for i'th weight w, put w of i (index) in the array. We get one random number num of [1, sum-up], and find value at position of num in the new array. It is our output. Problem is this method takes too much space. 

### Prefix Sums with Linear Search:
Based on concept of brute force method, we can try to find one solution that don't need created one array with sum-up length, but we can still find right index. Prefix sums can be used here. For each i position, all values in smaller and equal to i position will be added up and saved in i position. 

Select one random value between 1 and sum-up, because we donnot have 0 weight.

Find the first element in new array greater or equal to random value. Return the index.

Time Complexity: $\Omicron(N)$ for construction of prefix-sum, $\Omicron(N)$ for pickIndex().

Space Complexity: $\Omicron(N)$ for construction of prefix-sum,  $\Omicron(1)$ for pickIndex().

```
class Solution {
    int[] prefixSum;
    int len;
    
    public Solution(int[] w) {
        this.len = w.length;
        this.prefixSum = new int[len];
        prefixSum[0] = w[0];
        for (int i=1; i<len; i++) {
            prefixSum[i] = prefixSum[i-1] + w[i];
        }
    }
    
    public int pickIndex() {
        // find the first number larger or equal to selected values
        Random rndm = new Random();
        // there is no weight of 0, therefore, we get random from [1, max]
        int selected = rndm.nextInt(prefixSum[len-1]) + 1; 
        for (int i=0; i<len; i++) {
            if (prefixSum[i]>=selected) {
                return i;
            }
        }
        return 0;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(w);
 * int param_1 = obj.pickIndex();
 */
```
### Prefix Sums with Binary Search:
Time Complexity: $\Omicron(N)$ for construction of prefix-sum, $\Omicron(logN)$ for pickIndex().

Space Complexity: $\Omicron(N)$ for construction of prefix-sum,  $\Omicron(1)$ for pickIndex().

```
class Solution {
    int[] prefixSum;
    int len;
    
    public Solution(int[] w) {
        this.len = w.length;
        this.prefixSum = new int[len];
        prefixSum[0] = w[0];
        for (int i=1; i<len; i++) {
            prefixSum[i] = prefixSum[i-1] + w[i];
        }
    }
    
    public int pickIndex() {
        // find the first number larger or equal to selected values
        Random rndm = new Random();
        // there is no weight of 0, therefore, we get random from [1, max]
        int selected = rndm.nextInt(prefixSum[len-1]) + 1;
        int left = 0;
        int right = len-1;
        while (left<=right) {
            int mid = left + (right-left)/2;
            if (prefixSum[mid]>=selected) {
                right = mid-1;
            } else {
                left = mid+1;
            }
        }
        return right+1;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(w);
 * int param_1 = obj.pickIndex();
 */
```
