# 695. Max Area of Island
## Problem (Medium):
You are given an m x n binary matrix grid. An island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

The area of an island is the number of cells with a value 1 in the island.

Return the maximum area of an island in grid. If there is no island, return 0.
```
Input: grid = [[0,0,1,0,0,0,0,1,0,0,0,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,1,1,0,1,0,0,0,0,0,0,0,0],[0,1,0,0,1,1,0,0,1,0,1,0,0],[0,1,0,0,1,1,0,0,1,1,1,0,0],[0,0,0,0,0,0,0,0,0,0,1,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,0,0,0,0,0,0,1,1,0,0,0,0]]
Output: 6
Explanation: The answer is not 11, because the island must be connected 4-directionally.
```
More details on this problem see: https://leetcode.com/problems/max-area-of-island/
## Solution:
### Depth First Search (recursive)
Time Complexity: O(r*c), r is number of row, c is number of columen. because every node is visited.
Space Complexity: O(r*c), boolean matrix to save visiting status of each node.

```
class Solution {
    int[][] grid;
    boolean[][] visited;
    
    public int maxAreaOfIsland(int[][] grid) {
        // recursive Depth First Search
        // edge case
        if (grid.length==0 || grid[0].length==0) return 0;
        
        this.grid = grid;
        visited = new boolean[grid.length][grid[0].length];
        
        int area = 0;
        
        for (int row=0; row<grid.length; row++) {
            for (int col=0; col<grid[0].length; col++) {
                area = Math.max(area, getArea(row, col));
            }
        }
        
        return area;
    }
    
    // helper to calculate area
    private int getArea(int row, int col) {
        if (row<0 || row>=grid.length || col<0 || col>=grid[0].length || grid[row][col]==0 || visited[row][col]) {
            return 0;
        }
        visited[row][col] = true;
        return 1 + getArea(row-1, col) + getArea(row+1, col) + getArea(row, col+1) + getArea(row, col-1);
    }
}
```

### Depth First Search (iteratively)

Note: use Stack to mock resursiv method. 
1. Similar as recursive, we go through all the points. 
2. For each point: if this point is true, push it into stack. check all points related to this point, push them into stack. if there is still element in Stack, the search for this point is not finished. Go through all the element in stack, untill there is no element any more. 
3. only true element will be pushed in stack.
4. Each check will pop the top element and add 1 to area.

Time Complexity: O(n*n) every point will be traversed.
Space Complexity: O(n)

```
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        // iterative Depth First Search
        // edge case
        if (grid.length==0 || grid[0].length==0) return 0;
        
        boolean[][] visited = new boolean[grid.length][grid[0].length];
        
        // helpers
        int[] dr = new int[] {1, -1, 0, 0};
        int[] dc = new int[] {0, 0, 1, -1};
        int res = 0;
        
        for (int row=0; row<grid.length; row++) {
            for (int col=0; col<grid[0].length; col++) {
                if (!visited[row][col] && grid[row][col]==1) {
                    int area = 0;
                    visited[row][col] = true;
                    Stack<int[]> stack = new Stack<int[]>();
                    stack.push(new int[] {row, col});
                    while(!stack.empty()) {
                        area++;
                        int[] cur = stack.pop();
                        int cur_r = cur[0];
                        int cur_c = cur[1];
                        // search all 4 nodes related to current node 
                        for (int i=0; i<4; i++) {
                            int r = cur_r + dr[i];
                            int c = cur_c + dc[i];
                            if (r>=0 && r<grid.length && c>=0 && c<grid[0].length && grid[r][c]==1 && !visited[r][c]) {
                                stack.push(new int[]{r,c});
                                visited[r][c] = true;
                            }
                        }
                    }
                    res = Math.max(area, res);
                }
            }
        }
        return res;
    }
}
```

### Depth First Search (recursively)

Time Complexity: O(n*n) every point will be traversed.
Space Complexity: O(n)

```
class Solution {
    int[][] isConnected;
    boolean[] visitedRow;
    int count;
    
    public int findCircleNum(int[][] isConnected) {
        // 1 1 0
        // 1 1 0
        // 0 0 1
        // depth first search
        if (isConnected.length==0 || isConnected[0].length==0) return 0;
        // each row is one visiting node.
        this.visitedRow = new boolean[isConnected.length];
        this.isConnected = isConnected;
        this.count = 0;
        for (int row=0; row<isConnected.length; row++) {
            if (!visitedRow[row]) {
                count++;
                connectedCity(row);
            }
        }
        return count;
    }
    
    private void connectedCity(int row) {
        if (!visitedRow[row]) {
            visitedRow[row] = true;
            for (int c=0; c<isConnected[0].length; c++) {
                if (isConnected[row][c]==1 && !visitedRow[c]) {
                    connectedCity(c);
                }
            }
        }
    }
}
```

# 417. Pacific Atlantic Water Flow
## Problem (Medium):
There is an m x n rectangular island that borders both the Pacific Ocean and Atlantic Ocean. The Pacific Ocean touches the island's left and top edges, and the Atlantic Ocean touches the island's right and bottom edges.

The island is partitioned into a grid of square cells. You are given an m x n integer matrix heights where heights[r][c] represents the height above sea level of the cell at coordinate (r, c).

The island receives a lot of rain, and the rain water can flow to neighboring cells directly north, south, east, and west if the neighboring cell's height is less than or equal to the current cell's height. Water can flow from any cell adjacent to an ocean into the ocean.

Return a 2D list of grid coordinates result where result[i] = [ri, ci] denotes that rain water can flow from cell (ri, ci) to both the Pacific and Atlantic oceans.

```
Input: heights = [[1,2,2,3,5],[3,2,3,4,4],[2,4,5,3,1],[6,7,1,4,5],[5,1,1,2,4]]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```
More details see: https://leetcode.com/problems/pacific-atlantic-water-flow/
## Solution: 
### Depth First Search:

Note: check very point, if they can reach in both ocean, is very complicated. Therefore, think in another way, start with oceans and try to go to cells. If cells can be reached by ocean, mark it. 

Time Complexity: O(m*n)
Space Complexity: O(m*n)

```
class Solution {
    int[][] heights;
    boolean[][] reachP;
    boolean[][] reachA;
    
    int[] dr = new int[] {0, 0, 1, -1};
    int[] dc = new int[] {1, -1, 0, 0};
    
    public List<List<Integer>> pacificAtlantic(int[][] heights) {
        if (heights.length==0 || heights[0].length==0) {
            return new ArrayList<>();
        }
        // init values
        this.heights = heights;
        this.reachP = new boolean[heights.length][heights[0].length];
        this.reachA = new boolean[heights.length][heights[0].length];
        
        // recursive
        // pacific: first row & first col are all reachable
        for (int row=0; row<heights.length; row++) {
            // pacific
            checkReachability(row, 0, reachP);
            checkReachability(row, heights[0].length-1, reachA);
        }
        
        for (int col=0; col<heights[0].length; col++) {
            // pacific
            checkReachability(0, col, reachP);
            checkReachability(heights.length-1, col, reachA);
        }
        
        // compare pacific and atlantic reachability
        List<List<Integer>> res = new ArrayList<>();
        for (int r=0; r<heights.length; r++) {
            for (int c=0; c<heights[0].length; c++) {
                if (reachP[r][c] && reachA[r][c]) {
                    res.add(List.of(r,c));
                }
            }
        }
        
        return res;
    }
    
    private void checkReachability(int row, int col, boolean[][] reach) {
        reach[row][col] = true;
        // go in 4 directions
        for (int i=0; i<4; i++) {
            int newRow = row + dr[i];
            int newCol = col + dc[i];
            if (newRow>=0 && newCol>=0 && newRow<heights.length && newCol<heights[0].length 
                && !reach[newRow][newCol] && heights[newRow][newCol]>=heights[row][col]) {
                checkReachability(newRow, newCol, reach);
            }
        }
    }
}
```

### Breadth First Search:

TODO