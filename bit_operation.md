# 190. Reverse Bits
## Problem (Easy):
Reverse bits of a given 32 bits unsigned integer.

Note:

Note that in some languages, such as Java, there is no unsigned integer type. In this case, both input and output will be given as a signed integer type. They should not affect your implementation, as the integer's internal binary representation is the same, whether it is signed or unsigned.
In Java, the compiler represents the signed integers using 2's complement notation. Therefore, in Example 2 above, the input represents the signed integer -3 and the output represents the signed integer -1073741825.

```
Input: n = 00000010100101000001111010011100
Output:    964176192 (00111001011110000010100101000000)
Explanation: The input binary string 00000010100101000001111010011100 represents the unsigned integer 43261596, so return 964176192 which its binary representation is 00111001011110000010100101000000.
```
## Solution:
### Bit Manipulation:
```
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int tmp = 0;
        int res = 0;
        int count = 32;
        while (count>0) {
            tmp = n & 1;
            res = res << 1;
            res = res | tmp;
            n = n >> 1;
            count --;
        }
        return res;
    }
}
```

Time Complexity: $\Omicron(1)$ Int has a fixed size in java: 32 bit.

Space Complexity: $\Omicron(1)$

### Byte by Byte with memoization:
If this method will be called many times, we can save the reverse solution for each Byte in cache (HashMap) for reuse. There are only 2^8 space are needed. It is also a constant.

### Divide & Conquer with bit mask:
Note: for java, when doing right shift, >> is arithmetic shift.  In an arithmetic shift, the sign bit is extended to preserve the signedness of the number. >>> is logical shift. 
```
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        n = ((n & 0xffff0000) >>> 16) | ((n & 0x0000ffff) << 16);
        n = ((n & 0xff00ff00) >>> 8) | ((n & 0x00ff00ff) << 8);
        n = ((n & 0xf0f0f0f0) >>> 4) | ((n & 0x0f0f0f0f) << 4);
        n = ((n & 0xcccccccc) >>> 2) | ((n & 0x33333333) << 2);
        n = ((n & 0xaaaaaaaa) >>> 1) | ((n & 0x55555555) << 1);
        return n;
    }
}
```
Time Complexity: $\Omicron(1)$ No loop.

Space Complexity: $\Omicron(1)$

# 136. Single Number
## Problem (Easy):
Given a non-empty array of integers nums, every element appears twice except for one. Find that single one.

You must implement a solution with a linear runtime complexity and use only constant extra space.
```
Input: nums = [4,1,2,1,2]
Output: 4
```
## Solution:
### HashMap:

Time Complexity: $\Omicron(n)$ 

Remove element of List takes n. But remove element of HashSet takes 1.

Space Complexity: $\Omicron(n)$

```
class Solution {
    public int singleNumber(int[] nums) {
        Set<Integer> store = new HashSet<>();
        for (int i=0; i<nums.length; i++) {
            if (store.contains(nums[i])) {
                store.remove(nums[i]);
            } else {
                store.add(nums[i]);
            }
        }
        return store.iterator().next();
    }
}
```

### Bit Manipulation:

Note: 
Concept
1. If we take XOR of zero and some bit, it will return that bit:<br />
    $a \oplus 0 = a$
2. If we take XOR of two same bits, it will return 0: <br />
    $a \oplus a = 0$
3. $a \oplus b \oplus a = (a \oplus a) \oplus b = 0 \oplus b = b$

Time Complexity: $\Omicron(n)$

Space Complexity: $\Omicron(1)$

```
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0;
        for (int ele:nums) {
            res = res ^ ele;
        }
        return res;
    }
}
```

# 318. Maximum Product of Word Lengths
## Problem (Medium):
Given a string array words, return the maximum value of length(word[i]) * length(word[j]) where the two words do not share common letters. If no such two words exist, return 0.
Input: words = ["abcw","baz","foo","bar","xtfn","abcdef"]
Output: 16
Explanation: The two words can be "abcw", "xtfn".

## Notes:
1. constraints:
```
2 <= words.length <= 1000
1 <= words[i].length <= 1000
words[i] consists only of lowercase English letters.
```
Only lowercase is very important. Therefore, we only need space for 24 characters and can always compare with 'a'

2. The problem can be separated into 2 parts. First part is how to check if two string has common letters. Second part is how to reduce comparison of all strings.
3. checkCommonLetters: precomputation & bit mask & bit manipulation. Use 26 bits mask to refer a - z letters. Each bit represents whether this letter appears in the string or not.
4. string comparison: use hash table, only save the longest length for strings have same letters. (such as: ab, bbba, aaaaab)
   
## Solution: 
### Brute Force

Time Complexity: $\Omicron(N*N*L*L)$ L is length of string.

Space Complexity: $\Omicron(1)$
```
class Solution {
    public int maxProduct(String[] words) {
        // the main problem is how to check if two words have common letters.
        int max = 0;
        
        for (int i=0; i<words.length; i++) {
            for (int j=1; j<words.length; j++) {
                if (!containCommonLetters(words[i], words[j])) {
                    max = Math.max(max, words[i].length() * words[j].length());
                }
            }
        }
        
        return max;
    }
    
    // brute force
    public boolean containCommonLetters(String s1, String s2) {
        for (char ch: s1.toCharArray()) {
           if (s2.indexOf(ch) != -1) return true;
        }
        return false;
    }
}
```
### Bit Mask (commonletters) & HashMap (comparison)

Notes: 
1. calculate a 26 bit mask for each string, each bit refers to one char from a, b, c...z
2. use hash map to store the bit mask

Time Complexity: $\Omicron(N*N + L)$ L is the total length of all words.

Space Complexity: $\Omicron(N)$

```
class Solution {
    public int maxProduct(String[] words) {
        // the main problem is how to check if two words have common letters.
        int max = 0;
        // bit mask, max. length
        Map<Integer, Integer> dict = new HashMap<Integer, Integer>();
        
        // fill all string in the dict
        // bitMask: z ... c, b, a
        for (int i=0; i<words.length; i++) {
            int bitMask = 0;
            for (char ch : words[i].toCharArray()) {
                int num = ch - 'a';
                bitMask = bitMask | (1 << num);
            }
            dict.put(bitMask, Math.max(dict.getOrDefault(bitMask, 0), words[i].length()));
        }
        
        // find the max value
        for (int x : dict.keySet()) {
            for (int y : dict.keySet()) {
                if ((x & y) == 0) {
                    max = Math.max(dict.get(x)*dict.get(y), max);
                }
            }
        }
        
        return max;
    }
}
```
