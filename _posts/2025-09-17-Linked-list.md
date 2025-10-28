---
title: "Linked list"
date: 2025-08-29 10:00:00 +0800
categories: [CS basics, Python] # [Top_category, sub_category]
tags: [python, data structure]      # TAG names should always be lowercase
# author:gaoshan
---

```python
# wrtie a linked list class
class ListNode():
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# linked list has two parts: one store its data,one store the address of the next node. it can be stored in any place because the address of next node can help get to the right spot.
# Noted: the last pointer point to None
```
Learn some basic operations on Linked list: Need to remember like operations of add, minus, divide..

First, Traversing a linked list: Starting from the head node, follow the next pointer all the way until it points to null.
```python
def traverse(head):
    current = head
    while current is not None: # while curent is none,then stop the loop.
        print(current.val)
        current = current.next

# delete a node:
pre.next = current.next
```

1. Dummy Node(node of replica/fake)
why needs it?

when to use it?
reverse a linked list.
206. 反转链表

```python
# 1->2->3->null
# we need to get this: null->3->2->1 ,in fact, doing this 1<-2<-3<-null is equivenlent to null->3->2->1
# so changing next pointer to previous one can achieve the requirement. 
# prev = cur.next; update, in loop, cur = cur.next
# prev = cur.next; 
class Solution:
    def reverseNode(self, head):
        prev = None # because reversed Linked list start with a null describes as above.
        cur = head

        while cur is not None:
            temp = cur.next
            cur.next = prev # doing null<-1; 1<-2; 2<-3; 3<-null iterativly

            prev = cur
            
            cur =  temp
        return prev 
            
# A order mistake I took :
prev = None
cur = head # (Node 1)

# --- First Iteration ---
temp = cur     # temp now points to Node 1
cur.next = prev # Node 1's next pointer now points to None.
                 # The original link from Node 1 to Node 2 is now GONE!
prev = cur      # prev now points to Node 1
cur = temp.next # temp is Node 1, and we just set temp.next to None. So, curr becomes None.
        
```

160. 相交链表
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。
```python
# intersect: len(a)+len(b)+len(c)==len(b)+len(a)+len(c)
# not instersect len(a)+len(b)==len(b)+len(a)
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode):
        if not headA or not headB:
            return None
        
        #准备工作：两个“人” pA 和 pB，站在各自的起点
        pA = headA
        pB = headB
        
        #只要两个人没相遇，就一直走下去
        while pA != pB:
            if pA is None:
                pA = headB
            else:
                pA = pA.next
            
            if pB is None:
                pB = headA
            else:
                pB = pB.nex
        # two results
        #1. have intersect: return pA or pB
        #2. no intersect: when it meets the pA = pB #len(a)+len(b)==len(b)+len(a), return pA or pB(both are null)
        return pA
```
### neat version:
```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        if not headA or not headB:
            return None

        pA, pB = headA, headB

        while pA != pB:
            # 如果pA走到头了，就去B的起点；否则，就走下一步
            pA = headB if pA is None else pA.next
            # 如果pB走到头了，就去A的起点；否则，就走下一步
            pB = headA if pB is None else pB.next
            
        return pA
```