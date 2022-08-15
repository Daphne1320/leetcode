# 23. Merge k Sorted Lists
## Problem (Hard):
You are given an array of k linked-lists lists, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.
```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```
## Solution:
### 1. Brute Force:
* Traverse all the linked lists and collect the values of the nodes into an array.
* Sort and iterate over this array to get the proper value of nodes.
* Create a new sorted linked list and extend it with the new nodes.

Note: use ArrayList -> Collections.sort(list)

Time Complexity: $\Omicron(N \cdot logN)$ 
* Collecting all values costs $\Omicron(N)$
* A stable sorting algorithm costs $\Omicron(N \cdot logN)$
* Iterate to create new linked list costs $\Omicron(N)$
  
Space Complexity: $\Omicron(N)$ 
* Sorting cost $\Omicron(N)$ space
* Creating a new linked list costs $\Omicron(N)$

### 2. Compare One by One using PriorityQueue
* Compare every k nodes (head of every linked list) and get the smallest value.
* Extend the result with the selected node.

Time Complexity: $\Omicron(N \cdot logK)$ 
* There are N nodes in the final linked list.
* Each Comparison: $\Omicron(logK)$ for every pop und insertion in priority queue. Finding the smallest values only costs $\Omicron(1)$

Space Complexity: $\Omicron(N)$ <br />
* Create a new linked list costs N
* Priority Queue costs k space.

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode res = new ListNode();
        ListNode head = res;
        PriorityQueue<ListNode> queue = new PriorityQueue<ListNode>((a,b) -> a.val-b.val);
        
        for(ListNode list: lists) {
            if (list != null) {
                queue.add(list);
            }
        }
        
        while(!queue.isEmpty()) {
            head.next = queue.poll();
            head = head.next;
            if (head.next != null) {
                queue.add(head.next);
            }
        }
        return res.next;
    }
}
```

### 3. Merge with Divide and Conquer
* Pair up $\text{k}$ lists and merge each pair.
* After the first pairing, $\text{k}$ lists are merged into $k/2$ lists with average $2N/k$ length, then $k/4$, $k/8$ and so on.
* Repeat this procedure until we get the final sorted linked list.
  
![](stack_23_divide_and_conquer.png)

Time Complexity: $\Omicron(N \cdot logK)$
* n: number of nodes in the two merges lists
* N: total number of nodes in all lists
* Each merge is O(n) not O(N)
* Each level has a $\sum (each merge) = \Omicron(N)$
* There are logK levels and in each level you perform $\Omicron(N)$
  
Space Complexity: $\Omicron(1)$ <br />

```
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length==0) return null;
        if (lists.length==1) return lists[0];
        
        int interval = 1;
        while (interval < lists.length) {
            for (int i=0; i+interval<lists.length; i= i+interval*2) {
                lists[i] = mergeTwoLists(lists[i], lists[i+interval]);
            }
            interval *= 2;
        }
        return lists[0];
    }
    
    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(-1);
        ListNode res = head;
        while (l2 != null && l1 != null) {
            if (l1.val < l2.val) {
                head.next = l1;
                l1 = l1.next;
            } else {
                head.next = l2;
                l2 = l2.next;
            }
            head = head.next;
        }
        if (l1!=null) head.next = l1;
        if (l2!=null) head.next = l2;
        return res.next;
    }
}
```
