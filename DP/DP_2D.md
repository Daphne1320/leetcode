# 64. Minimum Path Sum
## Problem (Medium):
Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right, which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.

```
Input: grid = [[1,3,1],[1,5,1],[4,2,1]]
Output: 7
Explanation: Because the path 1 → 3 → 1 → 1 → 1 minimizes the sum.
```
## Solution:
### Recusion with memoization:
Note: save the minPath for every cell. 

Time Complexity: $\Omicron(m*n)$

Space Complexity: $\Omicron(m*n)$

```
class Solution {
    public int minPathSum(int[][] grid) {
        int[][] minSum = new int[grid.length][grid[0].length];
        
        for (int row=0; row<grid.length; row++) {
            for (int col=0; col<grid[0].length; col++) {
                if (row==0 && col==0) {
                    minSum[row][col] = grid[row][col];
                } 
                else if (row==0) {
                    minSum[row][col] = minSum[row][col-1] + grid[row][col];
                }
                else if (col==0) {
                    minSum[row][col] = minSum[row-1][col] + grid[row][col];
                }
                else {
                    minSum[row][col] = Math.min(minSum[row][col-1], minSum[row-1][col]) + grid[row][col];
                }
            }
        }
        
        return minSum[grid.length-1][grid[0].length-1];
    }
}
```

### DP with compressed space:
Note: only 1-D array is needed. 

```
class Solution {
    public int minPathSum(int[][] grid) {
        // DP: try to reduce space by using one array
        // as update of cell(m,n) is only related to cell(m-1, n) and cell(m, n-1)
        int[] minSum = new int[grid[0].length];
        
        for (int row=0; row<grid.length; row++) {
            for (int col=0; col<grid[0].length; col++) {
                if (row==0 && col==0) {
                    minSum[col] = grid[row][col];
                } 
                else if (row==0) {
                    // can only go from left (col-1)
                    minSum[col] = minSum[col-1] + grid[row][col];
                }
                else if (col==0) {
                    // can only go from cell on the top
                    // minSum[col] is still saving value of (row-1, col) => last row, same col
                    minSum[col] = minSum[col] + grid[row][col];
                }
                else {
                    // minSum[col] is value of top: (row-1, col), minSum[col-1] is value on the left.
                    minSum[col] = Math.min(minSum[col-1], minSum[col]) + grid[row][col];
                }
            }
        }
        
        return minSum[grid[0].length-1];
    }
}
```

# 542. 01 Matrix
## Problem (Medium):
Given an m x n binary matrix mat, return the distance of the nearest 0 for each cell.

The distance between two adjacent cells is 1.

```
Input: mat = [[0,0,0],[0,1,0],[0,0,0]]
Output: [[0,0,0],[0,1,0],[0,0,0]]
```
## Solution:
### Brute Force:
search for every cell with Breadth First Search to find the first zero. Many duplicate calculations. In worst case, go through the whole matrix for each cell. 

Time Complexity: $\Omicron((m*n)^2)$

Space Complexity: $\Omicron(1)$

### BFS with memoization:
Initialize memo wit Integer.MAX_VALUE to mark if cells are visited. To avoid duplicate calculations.

Time Complexity: $\Omicron(m*n)$

Space Complexity: $\Omicron(m*n)$

```
class Solution {
    int[][] memo;
    int[][] mat;
    int[] dr = new int[] {0, 0, 1, -1};
    int[] dc = new int[] {1, -1, 0, 0};
    
    public int[][] updateMatrix(int[][] mat) {
        // DP with memo
        this.memo = new int[mat.length][mat[0].length];
        this.mat = mat;
        // -1 means not visited
        for (int[] memoRow: memo) {
            Arrays.fill(memoRow, Integer.MAX_VALUE);
        }
        // BFS: start from cells with zero
        Queue<int[]> queue = new LinkedList<>();
        for (int row=0; row<mat.length; row++) {
            for (int col=0; col<mat[0].length; col++) {
                if (mat[row][col] == 0) {
                    queue.add(new int[]{row, col});
                    memo[row][col] = 0;
                }
            }
        }
        int dis = 0;
        while (queue.size()!=0) {
            int size = queue.size();
            dis++;
            while (size>0) {
                int[] pos = queue.poll();
                for (int i=0; i<4; i++) {
                    int row = pos[0] + dr[i];
                    int col = pos[1] + dc[i];
                    if (row>=0 && col>=0 && row<mat.length && col<mat[0].length && memo[row][col] == Integer.MAX_VALUE) {
                        memo[row][col] = dis;
                        queue.add(new int[]{row, col});
                    }
                }
                size--;
            }
        }
        
        return memo;
    }
}
```

### DP:
The distance of a cell from 0 can be calculated if we know the nearest distance for all the neighbors, in which case the distance is the minimum distance of any neighbor + 1. And, instantly, the words that come to mind are Dynamic Programming (DP)!!
From each 1, the minimum path to 0 could be in any direction. So, we need to check all the 4 directions. In one iteration from top to bottom, we can check left and top directions, and we need another iteration from bottom to top to check for right and bottom directions.

Algorithm:

Iterate over the matrix from top to bottom-left to right:
Update $\text{dist}[i][j]=\min(\text{dist}[i][j],\min(\text{dist}[i][j-1], \text{dist}[i-1][j])+1)$ i.e., minimum of the current dist and distance from top or left neighbor +1, that would have been already calculated previously in the current iteration.

Now, we need to do the back iteration in the similar manner: from bottom to top-right to left:
Update $\text{dist}[i][j]=\min(\text{dist}[i][j],\min(\text{dist}[i][j+1],\text{dist}[i+1][j])+1)$ i.e. minimum of current dist and distances calculated from bottom and right neighbors, that would be already available in current iteration.
```
class Solution {
    int[] dr = new int[] {0, 0, 1, -1};
    int[] dc = new int[] {1, -1, 0, 0};
    
    public int[][] updateMatrix(int[][] mat) {
        // DP: go through from top to bottom then from botton to top
        for (int row=0; row<mat.length; row++) {
            for (int col=0; col<mat[0].length; col++) {
                if (mat[row][col] != 0) {
                    mat[row][col] = Integer.MAX_VALUE-1;
                }
            }
        }
        
        // update from top & left
        for (int row=0; row<mat.length; row++) {
            for (int col=0; col<mat[0].length; col++) {
                if (row>=1) {
                    mat[row][col] = Math.min(mat[row][col], mat[row-1][col]+1);
                }
                if (col>=1) {
                    mat[row][col] = Math.min(mat[row][col], mat[row][col-1]+1);
                }
            }
        }
        
        // update from bottom & right
        for (int row=mat.length-1; row>=0; row--) {
            for (int col=mat[0].length-1; col>=0; col--) {
                if (row<mat.length-1) {
                    mat[row][col] = Math.min(mat[row][col], mat[row+1][col]+1);
                }
                if (col<mat[0].length-1) {
                    mat[row][col] = Math.min(mat[row][col], mat[row][col+1]+1);
                }
            }
        }
        
        return mat;
    }
}
```

# 221. Maximal Square
## Problem (Medium):
Given an m x n binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

```
Input: matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
Output: 4
```
## Solution:

### DP:
We initialize another matrix (dp) with the same dimensions as the original one initialized with all 0’s.

dp(i,j) represents the side length of the maximum square whose bottom right corner is the cell with index (i,j) in the original matrix.

Starting from index (0,0), for every 1 found in the original matrix, we update the value of the current element as

$\text{dp}(i, j) = \min \big( \text{dp}(i-1, j), \text{dp}(i-1, j-1), \text{dp}(i, j-1) \big) + 1$.

We also remember the size of the largest square found so far. In this way, we traverse the original matrix once and find out the required maximum size. This gives the side length of the square (say maxsqlen). The required result is the area $maxsqlen^2$.

Time Complexity: $\Omicron(m*n)$

Space Complexity: $\Omicron(m*n)$

```
class Solution {
    public int maximalSquare(char[][] matrix) {
        // edge cases
        if (matrix.length==0 || matrix[0].length==0) {
            return 0;
        }
        // DP
        int count = 0;
        int[][] dp = new int[matrix.length+1][matrix[0].length+1];
        
        for (int row=1; row<matrix.length+1; row++) {
            for (int col=1; col<matrix[0].length+1; col++) {
                if (matrix[row-1][col-1] == '1') {
                    dp[row][col] = Math.min(Math.min(dp[row][col-1], dp[row-1][col]), dp[row-1][col-1]) + 1;
                    count = Math.max(dp[row][col], count);
                }
            }
        }
        
        return count*count;
    }
}
```

