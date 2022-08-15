# 240. Search a 2D Matrix II
## Problem (Medium):
Write an efficient algorithm that searches for a value target in an m x n integer matrix matrix. This matrix has the following properties:

1. Integers in each row are sorted in ascending from left to right.
2. Integers in each column are sorted in ascending from top to bottom.

Example:
```
Input: matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 5
Output: true
```
Constraints:
1. m == matrix.length
2. n == matrix[i].length
3. $1 \le n, m \le 300$
## Solution:

### Binary search in horizontal & vertical direction:
Iterate matrix diagonals and search with binary search for row & col at each diagonal cell step. It is very intuitive to think of binary search method, because each row & col is sorted ascending. However, this method still takes very long time, because at each diagonal cell, we do 2 times binary search.

Time Complexity: $\Omicron(log(n!))$
1. The asymptotically-largest amount of work performed is in the main loop, which runs for $min(m, n)$ iterations, where mm denotes the number of rows and n denotes the number of columns. 
2. On each iteration, we perform two binary searches on array slices of length $m−i$ and $n-i$. Therefore, each iteration of the loop runs in $\Omicron(log(m-i) + log(n-i))$ time, where i denotes the current iteration. We can simplify this to $\Omicron(2 * log(n-i)) = \Omicron(log(n-i))$ by seeing that, in the worst case, n = m. 
3. By summing the runtimes of all iterations, we get the following expression:
   $\Omicron( log(n) + log(n-1) + log(n-3) + ... + log(1) )$
   By using log multiplication rule: $(log(a) + log(b) = log(ab) )$, we get:
   $\Omicron(log( n*(n-1)*(n-2)*(n-3)*...*1 ))$ = $\Omicron(log(n!))$

4.  Because this time complexity is fairly uncommon, it is worth thinking about its relation to the usual analyses. For one, $log(n!) = \Omicron(nlogn)$. To see why, recall the first equation in step 3 from the analysis above; there are n terms, each no greater than $log(n)$. Therefore, the asymptotic runtime is certainly no worse than that of an $\Omicron(nlogn)$ algorithm.

Space Complexity: $\Omicron(1)$

### Divide and Conquer (Excellent Explaination!!!):
Based on the binary search method above, it is easily to think, we can divide the matrix in to 4 sub matrix every time by finding a proper cell. Two of them might contain target and other two definitely not. 

Algorithm:
Because this algorithm operates recursively, its correctness can be asserted via the correctness of its base and recursive cases.

Base Case:

For a sorted two-dimensional array, there are two ways to determine in constant time whether an arbitrary element target can appear in it. First, if the array has zero area, it contains no elements and therefore cannot contain target. Second, if target is smaller than the array's smallest element (found in the top-left corner) or larger than the array's largest element (found in the bottom-right corner), then it definitely is not present.

Recursive Case:

If the base case conditions have not been met, then the array has positive area and target could potentially be present. Therefore, we seek along the matrix's middle column for an (the first) index row such that $matrix[row-1][mid] < target < matrix[row][mid]$ (obviously, if we find target during this process, we immediately return true). The existing matrix can be partitioned into four submatrice around this index; the top-left and bottom-right submatrice cannot contain target (via the argument outlined in Base Case section), so we can prune them from the search space. Additionally, the bottom-left and top-right submatrice are sorted two-dimensional matrices, so we can recursively apply this algorithm to them.

Time Complexity: $\Omicron(nlogn)$

First, for ease of analysis, assume that $n \approx m$. Also, assign $x = n^2 =  ∣matrix∣$; this will make the [master method](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms)) easier to apply. Now, let's model the runtime of the divide & conquer approach as a recurrence relation:<br />
$T(x) = 2 \cdot T(\frac x 4) + \sqrt x$ <br />
The first term $(2 * T(\frac x 4))$ arises from the fact that we recurse on two submatrices of roughly one-quarter size, while $\sqrt x$ comes from the time spent seeking along a $\Omicron(n)$-length column for the partition point. After binding the master method variables (a=2;b=4;c=0.5a=2;b=4;c=0.5) we notice that $log_ba = c$. Therefore, this recurrence falls under case 2 of the master method, and the following falls out:

$T(x) = \Omicron(x^c \cdot log x) \\
    = \Omicron(x^{0.5} \cdot log x) \\
    = \Omicron((n^2)^{0.5} \cdot log(n^2)) \\
    = \Omicron(n \cdot log(n^2)) \\
    = \Omicron(2n \cdot logn) \\
    = \Omicron(n \cdot logn)$



Space Complexity: $\Omicron(logn)$<br />
Although this approach does not fundamentally require greater-than-constant addition memory, its use of recursion means that it will use memory proportional to the height of its recursion tree. Because this approach discards half of matrix on each level of recursion (and makes two recursive calls), the height of the tree is bounded by $logn$.

```
class Solution {
    private int[][] matrix;
    private int target;
    
    public boolean searchMatrix(int[][] matrix, int target) {
        // edge cases
        if (matrix.length == 0 || matrix == null) {
            return false;
        }
        this.matrix = matrix;
        this.target = target;
        // divide & conquer: always divides into 4 sub matrix
        int row_min = 0;
        int col_min = 0;
        int row_max = matrix.length-1;
        int col_max = matrix[0].length-1;
        
        return searchRec(row_min, col_min, row_max, col_max);
    }
    
    private boolean searchRec(int r_min, int c_min, int r_max, int c_max) {
        // boundaries
        if (r_min<0 || c_min<0 || r_max>matrix.length-1 || c_max>matrix[0].length-1) {
            return false;
        }
        
        if (r_min>r_max || c_min>c_max) {
            return false;
        }
        // base cases: target is smaller than smallest element or larger than lartest element
        if (target<matrix[r_min][c_min] || target>matrix[r_max][c_max]) {
            return false;
        }
        
        // search the first cell in column, which is greater than target.
        int mid = c_min + (c_max-c_min)/2;
        int row = r_min;
        while (row<=r_max && matrix[row][mid] <= target) {
            if (matrix[row][mid] == target) {
                return true;
            }
            row ++;
        }
        return searchRec(r_min, mid+1, row-1, c_max) || searchRec(row, c_min, r_max, mid-1);
    }
}
```

### Search Space Reduction:
Because the rows and columns of the matrix are sorted (from left-to-right and top-to-bottom, respectively), we can prune \mathcal{O}(m)O(m) or \mathcal{O}(n)O(n) elements when looking at any particular value.

1. start from top-right: if cell is larger than target, move one step left, if cell is smaller than target, move one step down. 
2. start from bottom-left: similar.

Time Complexity: $\Omicron(m + n)$

Space Complexity: $\Omicron(1)$

```
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        // start from top-right or bottom-left
        // from top-right
        int row = 0;
        int col = matrix[0].length-1;
        while (row<matrix.length && col>=0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--;
            } else {
                row++;
            }
        }
        return false;
    }
}
```