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