# 215. Kth Largest Element in an Array
## Problem (Medium):
Given an integer array nums and an integer k, return the kth largest element in the array.

Note that it is the kth largest element in the sorted order, not the kth distinct element.
```
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```
## Solution:
### Qick Sort

Time Complexity: O(nlogn)

Space Complexity: O(1)

Notes: I write my own quick sort to practice. Embedded sort in java is actually also quicksort. 

```
class Solution {
    public int findKthLargest(int[] nums, int k) {
        // use quick sort
        quickSort(nums, 0, nums.length-1);
        return nums[nums.length-k];
    }
    
    private void quickSort(int[] nums, int left, int right) {
        if (left<right) {
            int pivotPosition = partition(nums, left, right);
            quickSort(nums, left, pivotPosition-1);
            quickSort(nums, pivotPosition+1, right);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        // select most right as pivot
        int pivot = nums[right];
        // most right position that smaller than pivot
        int pos = left-1;
        for(int i=left; i<=right-1; i++) {
            if(nums[i]<pivot) {
                pos++;
                swap(nums, pos, i);
            }
        }
        swap(nums, pos+1, right);
        return pos+1;
    }
    
    private void swap(int[] nums, int idx1, int idx2) {
        int tmp = nums[idx1];
        nums[idx1] = nums[idx2];
        nums[idx2] = tmp;
    }
}
```

### Quick Selection
Note: 
1. similar like quick sort. but it will igonore the half that does not contain target.
2. if select random pivot, always swap randwom pivot and most right element. The following is same as selecting most right element as pivot.
   
Time Complexity: in average O(n), worst case: O(n^2)

Space Complexity: O(1)
```
class Solution {
    public int findKthLargest(int[] nums, int k) {
        // use quickselection
        int pos = quickselection(nums, 0, nums.length-1, k);
        return nums[pos];
    }
    
    private int quickselection(int[] nums, int left, int right, int k) {
        int pivotPos = partition(nums, left, right);
        if ((nums.length-pivotPos)==k) return pivotPos;
        // if greater than k, target is in right part of pivot
        else if ((nums.length-pivotPos)>k) {
            return quickselection(nums, pivotPos+1, right, k);
        } else {
            return quickselection(nums, left, pivotPos-1, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        // take last element as pivot
        int pivot = nums[right];
        // pos marks the position before pivot
        int pos = left-1;
        for (int i=left; i<right; i++) {
            if (nums[i]<pivot) {
                pos++;
                swap(nums, pos, i);
            }
        }
        swap(nums, pos+1, right);
        return pos+1;
    }
    
    private void swap(int[] nums, int idx1, int idx2) {
        int tmp = nums[idx1];
        nums[idx1] = nums[idx2];
        nums[idx2] = tmp;
    }
    
}
```

# 347. Top K Frequent Elements
## Problem (Medium):
Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.
```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```
## Solution:

### HashSet + Heap
Note: heap is very proper to deal with top k problem. Please learn PriorityQueue in java first.
Idea: 
1. store (key, frequence) in hashmap. 
2. move key to a min-heap (with help of comparator), keep heap.size == k, always pop the smallest value, if size > k. 
3. move values into int array. Because the type saved in heap is not primitive data type.

Time Complexity: O(nlogk), because inserting node/delete min node in heap takes logm. we loop through n elements. 

Space Complexity: O(n+k)

```
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer, Integer> store = new HashMap<Integer, Integer>();
        
        for (int i=0; i<nums.length; i++) {
            int count = store.getOrDefault(nums[i], 0);
            store.put(nums[i], count+1);
        }
        
        // with help of heap/priorityQueue to find top k frequent element
        // create heap with comparator => compare frequence but save key in heap
        PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>(
            (a,b) -> store.get(a)-store.get(b));
        
        for (int key:store.keySet()) {
            minHeap.add(key);
            if (minHeap.size()>k) {
                minHeap.poll();
            }
        }
        
        // turn heap in to array
        // as priorityQueue.toArray() return array of object (Integer), 
        // but array of int is required
        int[] res = new int[k];
        for(int i=0; i<k; i++) {
            res[i] = minHeap.poll();
        }
        return res;
    }
}
```

### Bucket Sort
1. put frequnece in hashmap
2. initialize one bucket array of ArrayList, each position index refers to frequence. 
3. put all elements (add) with same frequence n in n position of bucket array.
4. get all element within k most frequence, concat them with addAll()
5. convert ArrayList to Array


Time Complexity: O(n) even many for loops, each loop is n, 4*n is n
Space Complexity: O(n)


class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer, Integer> store = new HashMap<Integer, Integer>();
        
        // (value, frequence)
        for (int i=0; i<nums.length; i++) {
            int count = store.getOrDefault(nums[i], 0);
            store.put(nums[i], count+1);
        }
        
        // with help of bucket 
        // create array of arraylist
        ArrayList<Integer>[] bucket = new ArrayList[nums.length];
        // save element according to frequence
        for (int key: store.keySet()) {
            int freq = store.get(key);
            if (bucket[freq-1]==null) {
                bucket[freq-1] = new ArrayList<>();
            }
            bucket[freq-1].add(key);
        }
        // get k most frequent element
        int count = 0;
        ArrayList<Integer> resList = new ArrayList<Integer>();
        for (int i=bucket.length-1; i>=0; i--) {
            if (bucket[i]!=null && count<k) {
                count = count + bucket[i].size();
                resList.addAll(bucket[i]);
            }
        }
        //turn resList to resArray
        int[] res = new int[resList.size()];

        for (int i=0; i<resList.size(); i++) {
            res[i] = resList.get(i);
        }
        return res;
        
    }
}