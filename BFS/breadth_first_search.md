# 934. Shortest Bridge
## Problem (Medium):
You are given an n x n binary matrix grid where 1 represents land and 0 represents water.

An island is a 4-directionally connected group of 1's not connected to any other 1's. There are exactly two islands in grid.

You may change 0's to 1's to connect the two islands to form one island.

Return the smallest number of 0's you must flip to connect the two islands.

```
Input: grid = [[1,1,1,1,1],[1,0,0,0,1],[1,0,1,0,1],[1,0,0,0,1],[1,1,1,1,1]]
Output: 1
```
## Solution:
### Breadth First Search:
Note: Use DFS to find the first island. Go from first island (every point inside it is a starting point) to find the second island with BFS.

For queue, always mark the size of current queue, because we want to process level by level, not item by item. In the end number of levels will be returned.

Optimierung: instead of visited matrix, always mark 2 after visiting cell to mark if visited or not.

Time Complexity: ??? 
Space Complexity: ???

```
class Solution {
    // global variables
    int[][] grid;
    boolean[][] visited;
    Queue<int[]> queue = new LinkedList<>();
    
    int[] dr = new int[]{0, 0, 1, -1};
    int[] dc = new int[]{1, -1, 0, 0};
    
    
    public int shortestBridge(int[][] grid) {
        // edge case
        if (grid.length==0 || grid[0].length==0) {
            return 0;
        }
        // init values
        this.grid = grid;
        this.visited = new boolean[grid.length][grid[0].length];
        
        
        // DFS to find the first island
        // find start point first
        boolean found = false;
        for (int row=0; row<grid.length; row++) {
            if (found) {
                break;
            }
            for (int col=0; col<grid[0].length; col++) {
                if (grid[row][col]==1) {
                    dfs(row, col);
                    found = true;
                    break;
                }
            }
        }
        
        // try to find second islan with bfs
        int step = 0;
        while (queue.size()!=0) {
            // Important!! every step go over one level. 
            // process level by level, not item by item, because we need return the level numer.
            int size = queue.size();
            
            while (size!=0) {
                int[] curr = queue.poll();
                for (int i=0; i<4; i++) {
                    int row = curr[0] + dr[i];
                    int col = curr[1] + dc[i];
                    
                    if (row>=0 && col>=0 && row<grid.length && col<grid[0].length && !visited[row][col]) {
                        // base
                        if (grid[row][col] == 1) {
                            return step;
                        }
                        queue.add(new int[]{row, col});
                        visited[row][col] = true;
                    }
                }
                size--;
            }
            step++;
        }
        return -1;
    }
    
    // helper for dfs
    private void dfs(int row, int col) {
        // boundaries
        if (row<0 || col<0 || row>grid.length-1 || col>grid[0].length-1 || visited[row][col] || grid[row][col]==0) {
            return;
        }
        
        visited[row][col] = true;
        if (grid[row][col] == 1) {
            // rewrite value of first island && add point to queue
            grid[row][col] = 2;
            queue.offer(new int[]{row, col});
            for (int i=0; i<4; i++) {
                dfs(row+dr[i], col+dc[i]);
            }
        }
    }
}
```


# 126. Word Ladder II
## Problem (hard):
A transformation sequence from word beginWord to word endWord using a dictionary wordList is a sequence of words beginWord -> s1 -> s2 -> ... -> sk such that:

Every adjacent pair of words differs by a single letter.
Every si for 1 <= i <= k is in wordList. Note that beginWord does not need to be in wordList.
sk == endWord
Given two words, beginWord and endWord, and a dictionary wordList, return all the shortest transformation sequences from beginWord to endWord, or an empty list if no such sequence exists. Each sequence should be returned as a list of the words [beginWord, s1, s2, ..., sk].

```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: [["hit","hot","dot","dog","cog"],["hit","hot","lot","log","cog"]]
Explanation: There are 2 shortest transformation sequences:
"hit" -> "hot" -> "dot" -> "dog" -> "cog"
"hit" -> "hot" -> "lot" -> "log" -> "cog"
```
## Solution:
### BFS + Backtracking:
Note: too hard for me. I copied solution from leetcode. Suggest do it again by myself in future.

Time Complexity: $\Omicron(NK^2 + 2)$
Here NN is the Number of words in wordList, KK is the maximum length of a word, αα is the Number of possible paths from beginWord to endWord in the directed graph we have.

Space Complexity: $\Omicron(NK)$
```
class Solution {
    Map<String, List<String>> adjList = new HashMap<String, List<String>>();
    List<String> currPath = new ArrayList<String>();
    List<List<String>> shortestPaths = new ArrayList<List<String>>();
    
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        // step 1: convert list to hashset for efficient deletion in BFS
        Set<String> copiedWordList = new HashSet<>(wordList);
        
        // step 2: build DAG(directed acyclic graph) using BFS
        bfs(beginWord, endWord, copiedWordList);
        
        // every path will start fromt the beginword
        currPath.add(beginWord);
        // traverse the DAG to find all paths between beginWord & endWord
        backtrack(beginWord, endWord);
        
        return shortestPaths;
    }
    
    // find words which have only one char different from target word
    private List<String> findNeighbors(String word, Set<String> wordList) {
        List<String> neighbors = new ArrayList<String>();
        char[] charList = word.toCharArray();
        
        for (int i=0; i<word.length(); i++) {
            char oldChar = charList[i];
            // replace the i-th character with all letters from a to z except the original character
            for (char c='a'; c<='z'; c++) {
                charList[i] = c;
                // skip if the character is same as original or if the word is not present in the wordList
                if (c==oldChar || !wordList.contains(String.valueOf(charList))) {
                    continue;
                }
                neighbors.add(String.valueOf(charList));
            }
            charList[i] = oldChar;
        }
        return neighbors;
    }
    
    private void bfs(String beginWord, String endWord, Set<String> wordList) {
        Queue<String> q = new LinkedList<>();
        q.add(beginWord);
        
        // remove root word, which is the first layer in the BFS
        if (wordList.contains(beginWord)) {
            wordList.remove(beginWord);
        }
        
        Map<String, Integer> isEnqueued = new HashMap<String, Integer>();
        isEnqueued.put(beginWord, 1);
        
        while (q.size()>0) {
            // visited will store the words of current layer
            List<String> visited = new ArrayList<String>();
            
            for (int i=q.size()-1; i>=0; i--) {
                String currWord = q.poll();
                
                // findNeighbors will have the adjacent words of the currWord
                List<String> neighbors = findNeighbors(currWord, wordList);
                for (String word: neighbors) {
                    visited.add(word);
                    
                    if (!adjList.containsKey(currWord)) {
                        adjList.put(currWord, new ArrayList<String>());
                    }
                    
                    // add the edge from currWord to word in the list
                    adjList.get(currWord).add(word);
                    if (!isEnqueued.containsKey(word)) {
                        q.add(word);
                        isEnqueued.put(word, 1);
                    }
                }
            }
            
            // remove the words of previous layer
            for (int i=0; i<visited.size(); i++) {
                if (wordList.contains(visited.get(i))) {
                    wordList.remove(visited.get(i));
                }
            }
        }
    }
    
    private void backtrack(String source, String dest) {
        // store the path if we reached the endWord
        if (source.equals(dest)) {
            List<String> tmpPath = new ArrayList<String>(currPath);
            shortestPaths.add(tmpPath);
        }
        
        if (!adjList.containsKey(source)) {
            return;
        }
        
        for (int i=0; i<adjList.get(source).size(); i++) {
            currPath.add(adjList.get(source).get(i));
            backtrack(adjList.get(source).get(i), dest);
            currPath.remove(currPath.size()-1);
        }
    }
}
```