# 206. Reverse Linked List
## Problem (Easy):
Given the head of a singly linked list, reverse the list, and return the reversed list.
```
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```
## Solution:
### Iteratively
Time Complexity: O(n)
Space Complexity: O(1)
The following code will change given value of head. If head should be not changed. ListNode curr = head;
´´´
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
    public ListNode reverseList(ListNode head) {
        // iteratively
        ListNode prev = null;
        ListNode next = new ListNode();
        while(head!=null) {
            next = head.next;
            head.next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }
}
´´´

### Recursively
Time Complexity: O(n)
Space Complexity: O(n) because recursion can go up to n levels deep.
notes: consider base case first. 
for n: 0->1->2->... n -> n+1 <-n+2<-n+3... put next of n+1 to n. In addition, everytime you get from last recusion is the head of reversed listnode, it does not need any change.
```
class Solution {
    public ListNode reverseList(ListNode head) {
        // recursively
        // head = null for edge case that given head is null
        if(head==null || head.next==null) return head;
        ListNode reverse = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return reverse;
    }
}
```

# 21. Merge Two Sorted Lists
## Problem (Easy):
You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists in a one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.
```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```
## Solution:
### Iteratively:
Time Complexity: O(n+m)
Space Complexity: O(1)
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
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        
        ListNode prehead = new ListNode();
        ListNode head = prehead;
        
        while(list1!=null && list2!=null) {
            if (list1.val<=list2.val) {
                head.next = list1;
                list1 = list1.next;
            } else {
                head.next = list2;
                list2 = list2.next;
            }
            head = head.next;
        }
        
        // if list1 or list2 still contains nodes
        head.next = list1 == null ? list2: list1;
        
        return prehead.next;
    }
}
```
### Recursively
Time Complexity: O(n+m)
Space Complexity: O(n+m) (the first call to mergeTwoLists does not return untill the ends of both left & right have been reached.)
```
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if(list1==null) return list2;
        else if(list2==null) return list1;
        else if(list1.val<=list2.val) {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        } else {
            list2.next = mergeTwoLists(list1, list2.next);
            return list2;
        }
    }
}
```

# 24. Swap Nodes in Pairs
## Problem (Easy):
Given a linked list, swap every two adjacent nodes and return its head. You must solve the problem without modifying the values in the list's nodes (i.e., only nodes themselves may be changed.)
```
Input: head = [1,2,3,4]
Output: [2,1,4,3]
```
## Solution:
### Iteratively:
notes: 1. use dummy node. 2. node will not disappear. the connections between nodes can be deleted/changed, which will affect order of nodes or delink nodes(disappear because lose of connection). 

Time Complexity: O(n)
Space Complexity: O(1)
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
    public ListNode swapPairs(ListNode head) {
        ListNode prehead = new ListNode(-1);
        prehead.next = head;
        
        ListNode prevNode = prehead;
        while(head!=null && head.next!=null) {
            ListNode firstNode = head;
            ListNode secondNode = head.next;
            //swap
            prevNode.next = secondNode;
            head.next = secondNode.next;
            secondNode.next = head;

            //next round
            prevNode = head;
            head = firstNode.next;
        }
        return prehead.next;
    }
}
```

### Recursively:
notes: consider the base case first. Then for other cases, always consider the rest is already processed. Only consider what should you do now for current elements.
Time Complexity: O(n)
Space Complexity: O(n)
```
class Solution {
    public ListNode swapPairs(ListNode head) {
        // base: less than two nodes in the list
        if(head==null) return null;
        if(head.next==null ) return head;
        // more than two nodes in the list
        ListNode firstNode = head;
        ListNode secondNode = head.next;
        firstNode.next = swapPairs(secondNode.next);
        secondNode.next= firstNode;
        return secondNode;
    }
}
```

# 160. Intersection of Two Linked Lists
## Problem (Easy):
Given the heads of two singly linked-lists headA and headB, return the node at which the two lists intersect. If the two linked lists have no intersection at all, return null.

The test cases are generated such that there are no cycles anywhere in the entire linked structure.

Note that the linked lists must retain their original structure after the function returns.

```
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
Output: Intersected at '8'
Explanation: The intersected node's value is 8 (note that this must not be 0 if the two lists intersect).
From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads as [5,6,1,8,4,5]. There are 2 nodes before the intersected node in A; There are 3 nodes before the intersected node in B.
```
More details for examples please check: https://leetcode.com/problems/intersection-of-two-linked-lists/
## Solution:
### BruteForce: 
Time Complexity: O(N * M)
Space Complexity: O(1)
### HashSet:
Notes: Similar with Two Sum, we can use hashset to solve this problem. 
Time Complexity: O(N+M)
Space Complexity: O(N) as we save first ListNode into Hashset.
```
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // hashset
        Set<ListNode> store = new HashSet<ListNode>();
      
        while(headA!=null) {
            store.add(headA);
            headA = headA.next;
        }
        
        while(headB!=null) {
            if(store.contains(headB)) return headB;
            headB = headB.next;
        }  
        return null;
    }
}
```
### Two Pointers => Very Smart!
Original Idea: If A & B has intersection, then they have a shared path. A & B might have different length. A has m  and B has n length. If m < n, we can start pointerA at begin of A and start with pointerB at n-m position of B. Each pointer go one step one time. They will meet at intersection point. What we need do is find out length for A&B. 

Improved Idea: put pointerA at begin of A, pointerB at begin of B. Move both pointers one step one time. If pointerA reaches end, put pointerA to head of B. If pointerB reaches end, put pointerB to head of A. If A has n + k length, B has m + k length. k is shared length. Each pointer will go n + m + k steps and meet at intersection point. If both list has no intersection, after second iteration, both pointer reach end, which means null. 

Time Complexity: in worst case: O(n+m)
Space Complexity: O(1)
```
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // two pointers
        ListNode pA = headA;
        ListNode pB = headB;
        
        while(pA!=pB) {
            pA = pA==null ? headB : pA.next;
            pB = pB==null ? headA : pB.next;
        }
        return pA;
    }
}
```

# 234. Palindrome Linked List
## Problem (Easy):
Given the head of a singly linked list, return true if it is a palindrome.
```
Input: head = [1,2,2,1]
Output: true
```
## Solution:
### Brute Force:
Notes: 
At first, my idea is get reverse list then compare reverse list & original list. But it turned out, if list is reversed, the original list is lost. If we copy the list by value, it is hard to compare two node. Because the node adresse are different. only node values and list structure are same.

Therefore, here is suggested to copy list into one ArrayList. Then use two pointer to check if it is palindrome. 

!!! becaues Integer not int is saved in hashset. By comparing using .equals() instead of ==. 

Time Complexity: O(n) first part of copy takes O(n), second part of comparison takes O(n/2) => in total is O(n)
Space Complexity: O(n)

```
class Solution {
    public boolean isPalindrome(ListNode head) {
        // brute force: copy linkedlist into arraylist
        ArrayList values = new ArrayList<Integer>();
        while(head!=null) {
            values.add(head.val);
            head = head.next;
        }
        
        // use two pointers to check if list is palindrome
        int left = 0;
        int right = values.size()-1;
        
        while(left<=right) {
            // Dont use !=. Because we compare Integer not int. Use .equals instead
            if (!values.get(left).equals(values.get(right))) return false;
            left++;
            right--;
        }
        return true;
    }
}
```

### Reverse second half of list
Note: as mentioned above, reverse whole list is not possible. But we can reverse the second half of list. 

1. use quick and slow pointers to find the middle point.
2. reverse the seconde half
3. compare fisrt half and second half

Time Complexity: O(n) => find middle is n, reverse is n, compare is n, therefore, in total is n.
Space Complexity: O(1)

```
class Solution {
    public boolean isPalindrome(ListNode head) {
        // use quick & slow pointers to find the middle pointer
        // n n 1
        // n n
        ListNode quickP = head;
        ListNode slowP = head;
        while(quickP.next != null && quickP.next.next!=null) {
            slowP = slowP.next;
            quickP = quickP.next.next;
        }
        
        // slowP is mid point
        slowP.next = reverseList(slowP.next);
        slowP = slowP.next; // now slowP is start of second half
        // compare two halfs
        while(slowP!=null) {
            if (slowP.val != head.val) return false;
            slowP = slowP.next;
            head = head.next;
        }
        return true;
    }
    
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        while(head!=null) {
            // head -> next
            ListNode next = head.next;
            head.next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }
}
```
