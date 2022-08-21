# 437. Path Sum III
## Problem (Medium):
Given the root of a binary tree and an integer targetSum, return the number of paths where the sum of the values along the path equals targetSum.

The path does not need to start or end at the root or a leaf, but it must go downwards (i.e., traveling only from parent nodes to child nodes).

```
Input: root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
Output: 3
Explanation: The paths that sum to 8 are shown.
```
## Solution:
### Brute Force (Depth First Search)
* Go through every node and find the number of paths starting with current node. (recursive)
* Write a help method to calculate number of paths starting with current node. (recursive)
* Integer Max Value in java: [-2147483648, 2147483647]. Values could exceed the interval. Use long instead of int.

Time Complexity: $\Omicron(n^2)$ in worst case,  $\Omicron(nlogn)$ in best case. 
* Worst case: no branches [n + (n-1) + (n-2) ... + 1] => n^2
* Best case: balanced tree. There are three search for each node. The time cost of each search takes height of the subtree h. Wenn tree is balanced, h == logn. As we have n nodes in total, total time takes n*logn.<br />
  
Space Complexity: $\Omicron(n)$ in worst case (no branches), it will go n levels <br />

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
public class Solution {
    public int pathSum(TreeNode root, int sum) {
        if (root == null) return 0;
        return pathSumFrom(root, sum) + pathSum(root.left, sum) + pathSum(root.right, sum);
    }
    
    private int pathSumFrom(TreeNode node, long sum) {
        if (node == null) return 0;
        return (node.val == sum ? 1 : 0) 
            + pathSumFrom(node.left, sum - node.val) + pathSumFrom(node.right, sum - node.val);
    }
}
```
### Prefix Sum with Hashmap

* The idea is using prefix sum while go over the tree. It is similar with LeetCode 560.
* Pre-order tranverse, similar as backtracking. Always delete information related to left node before go to right node.
* There is one pdf of introduction of Prefix Sum Theory. 

Time Complexity: $\Omicron(n)$ <br />
Space Complexity: $\Omicron(n)$ <br />

```
public class Solution {
    // use prefix sum with hashmap
    HashMap<Long, Integer> map;
    int count;
    long prefixSum;
    
    public int pathSum(TreeNode root, int sum) {
        if (root == null) return 0;
        this.map = new HashMap<Long, Integer>();
        this.count = 0;
        this.prefixSum = 0;
        // init for subtree starting from root
        map.put((long) 0, 1);
        calPrefixSum(root, sum);
        return count;
    }
    
    private void calPrefixSum(TreeNode root, int sum) {
        if (root == null) return;
        prefixSum += root.val;
        count += map.get(prefixSum - sum) == null? 0 : map.get(prefixSum-sum);
        map.put(prefixSum, map.getOrDefault(prefixSum, 0)+1); 
        
        calPrefixSum(root.left, sum);
        calPrefixSum(root.right, sum);
        
        map.put(prefixSum, map.getOrDefault(prefixSum, 0)-1);
        prefixSum -= root.val;
    }
}
```