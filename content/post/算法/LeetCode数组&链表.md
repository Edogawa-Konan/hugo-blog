---
title: "LeetCode数组&链表"
date: 2020-01-16T10:13:37+08:00
lastmod: 2020-01-16T10:13:37+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# LeetCode数组

## 26. Remove Duplicates from Sorted Array

[Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

> 从小到大的有序数组，要求在$O(1)$空间内，去除重复元素

## 80. Remove Duplicates from Sorted Array II

[Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

>从小到大的有序数组，要求在$O(1)$空间内，去除重复元素，每个元素最多出现2次。

以上两道题类似的结构，解法也一样，用两个指针即可。

```java
public int removeDuplicates(int[] nums) {
    int i = 0;
    for (int n : nums)
        if (i < 2 || n > nums[i-2])
            nums[i++] = n;
    return i;
}
```

## 83. Remove Duplicates from Sorted List「两个指针」

[Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个有序链表，去除重复元素。
>
> **Example 1:**
>
> Input: 1->1->2
> Output: 1->2

```java
public ListNode deleteDuplicates(ListNode head) {
        if(head == null)
            return null;
        ListNode res = new ListNode(0);
        res.next = head;
        ListNode cur = head, pre = res;
        while (cur != null)
        {
            while (cur.next != null && cur.val == cur.next.val)
            {
                cur = cur.next;
            }
            if(pre.next != cur)
            {// no duplicates
                pre.next = cur;
            }
            pre = pre.next;
            cur = cur.next;
        }
        return res.next;
    }
```

## 82. Remove Duplicates from Sorted List II「两个指针」

[Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

>给定一个有序链表，去除重复元素，要求不保留。
>
>**Example:**
>
>Input: 1->1->1->2->3
>Output: 2->3

```java
public ListNode deleteDuplicates(ListNode head) {
        if(head == null)
            return head;
        ListNode res = new ListNode(0);
        res.next = head;
        ListNode pre = res, cur = res.next;
        while (cur!=null)
        {
            while (cur.next!=null && cur.val == cur.next.val)
            {
                cur = cur.next;
            }
            if (pre.next == cur)
            {
                pre = pre.next;
                cur = cur.next;
            }
            else
            { // duplicate detected
                pre.next = cur.next;
                cur = pre.next;
            }
        }
        return res.next;
    }
```

## 86. Partition List「两个指针」

[Partition List](https://leetcode.com/problems/partition-list/)

>给定一个链表，和一个值x。要求完成一次快排的partition操作。

直接创立两个链表，一个保存小于`x`的结点，一个保存大于等于`x`的结点，最后将两个链表连接即可。

```java
public static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
        }
    }
    public ListNode partition(ListNode head, int x) {
        ListNode p = new ListNode(0), q = new ListNode(0);
        ListNode L1 = p, L2 = q;
        while (head!=null)
        {
            if(head.val < x)
            {
                L1.next = head;
                L1 = L1.next;
            }else {
                L2.next = head;
                L2 = L2.next;
            }
            head = head.next;
        }
        L1.next = q.next;
        L2.next = null;
        return p.next;
    }
```

## 88. Merge Sorted Array「两个指针」

[Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)

>给定两个有序数组nums1和nums2，其中nums1空间足够大，要求合并到nums1中使得整个数组依旧有序。

采用两个指针，一遍遍历即可。注意从后往前扫描，因为后面是有预留空间的。

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
        int end = m + n - 1;
        int p = m - 1, q = n - 1;
        while (p >= 0 && q >= 0) {
            if(nums1[p]  < nums2[q]){
                nums1[end--] = nums2[q--];
            }else{
                nums1[end--] = nums1[p--];
            }
        }
        while (q>=0)
        {
            nums1[end--] = nums2[q--];
        }
    }
```

## 92. Reverse Linked List II「四个指针」

[Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)

>给定一个链表，反转其从m到n的结点。要求一遍遍历。

采取逻辑上比较简单的思路，即把待反转的子链表通过插入新链表的方式反转，再拼接。

```java
public static class ListNode {
        int val;
        ListNode next;

        ListNode(int x) {
            val = x;
        }
    }
    public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode dummy = new ListNode(0), rev_dummy = new ListNode(0);  //反转子链表的头结点
        ListNode rev_end = null;  //指向反转后的最后一个结点
        dummy.next = head;
        ListNode fast = head, slow = dummy;
        for(int index = 1; index<=n; index++)
        {
            if(index < m)
            {
                fast = fast.next;
                slow = slow.next;
            }
            else{
                if(index == m)
                    rev_end = fast;
                ListNode tmp = fast;
                fast = fast.next;
                tmp.next = rev_dummy.next;
                rev_dummy.next = tmp;
            }
        }
        slow.next = rev_dummy.next;
        rev_end.next = fast;
        return dummy.next;
    }
```

##  138-Copy List with Random Pointer「链表」

[Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

深拷贝链表，关键在于random字段的填充。

采取一个比较简单的思路，第一次遍历用一个map保存**原结点-新结点**的对，然后第二次遍历填充next和random字段。

```c++
class Node {
public:
    int val;
    Node* next;
    Node* random;

    Node() {}

    Node(int _val, Node* _next, Node* _random) {
        val = _val;
        next = _next;
        random = _random;
    }
};

class Solution {
public:
    Node* copyRandomList(Node* head) {
        unordered_map<Node*, Node*> mm;
        Node* p = head;
        while (p)
        {
            mm[p] = new Node(p->val, nullptr, nullptr);
            p = p->next;
        }

        p = head;
        while (p){
            mm[p]->next = mm[p->next];
            mm[p]->random = mm[p->random];
            p = p->next;
        }
        return mm[head];
    }
};
```

---

**ps: map用[]访问不到，会插入元素**

## 141-Linked List Cycle「链表」

[Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

判断链表是否有环。

O(1)的方法，设置两个指针，一个走两步，一个走一步，如果有环它们终会回合，否则就是走到空指针循环结束。

```c++
struct ListNode {
    int val;
    ListNode *next;

    explicit ListNode(int x) : val(x), next(nullptr) {}
};


class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == nullptr)
            return false;
        ListNode* one = head;
        ListNode* two = head;
        while (two->next && two->next->next)
        {
            one = one->next;
            two = two->next->next;
            if(one == two)
                return true;
        }
        return false;
    }
};
```

## 142-Linked List Cycle II[两个指针]

[Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
同上一题，不过需要返回环开始的结点。解法也类似。
设$A$表示起点到环开始结点的距离，$B$为环开始结点到第一次相遇的距离，$N$表示环的长度。则有$2A+2B-(A+B)=A+B=N$。
因此找到第一次相遇的结点后，让慢指针继续走同时头结点再开始一个慢指针，它们相遇的结点即是环的开始。

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(head == nullptr)
            return nullptr;
        ListNode *one = head, *two = head;
        while (two && two->next)
        {
            one = one->next;
            two = two->next->next;
            if(one == two)
            {
                ListNode* one2 = head;
                while(one != one2)
                {
                    one = one->next;
                    one2 = one2->next;
                }
                return one2;
            }
        }
        return nullptr;
    }
};
```

## 148-Sort List[归并排序]

[Sort List](https://leetcode.com/problems/sort-list/)

对链表进行排序，要求O(nlogn)的时间复杂度且用常量的空间复杂度。

考虑使用迭代版归并排序（不使用递归是因为递归本身需要用栈，不是常量空间）。

```c++
struct ListNode {
    int val;
    ListNode *next{};

    explicit ListNode(int x) : val(x), next(nullptr) {}
};
class Solution {
private:
    ListNode *split(ListNode*head, int n)
    {//从head中取前n个结点，返回剩下的链表的首结点指针
        for(int i=1; head&&i<n; i++)
            head = head->next;
        if(!head)
            return nullptr;
        else
        {
            ListNode* second = head->next;
            head->next = nullptr;
            return second;
        }
    }
    ListNode *merge(ListNode*left, ListNode*right, ListNode*tail)
    {//把left和right链表按顺序追加到tail尾部，最后返回链表尾部的指针
        ListNode * cur = tail;
        while (left&&right)
        {
            if(left->val < right->val )
            {
                cur->next = left;
                cur = left;
                left=left->next;
            } else{
                cur->next = right;
                cur = right;
                right = right->next;
            }
        }
        while (left)
        {
            cur->next = left;
            cur = left;
            left = left->next;
        }
        while (right)
        {
            cur->next = right;
            cur = right;
            right = right->next;
        }
        return cur;
    }
public:
    ListNode *sortList(ListNode *head) {
        if(!head || !(head->next)) return head;
		//先求链表长度
        ListNode* p = head;
        int length = 0;
        while(p)
        {
            length++;
            p=p->next;
        }
        ListNode dummy(0);//增加一个头结点便于插入
        dummy.next = head;
        ListNode *left, *right, *tail, *cur;
        for(int step = 1; step<length; step*=2)
        {
            cur = dummy.next; 
            tail = &dummy;
            while(cur)
            {
                left = cur;
                right = split(left, step);
                cur = split(right, step);
                tail = merge(left, right, tail);
            }
        }
        return dummy.next;
    }
};
```

## 160-Intersection of Two Linked Lists「两个指针」

[Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

找到两个链表相交的开始阶段，不相交返回null。

和141相似，同样设置两个指针。假设链表1起点到相交结点的距离为A，链表2起点到相交结点距离为B，两个链表重合部分长度为C。

基本想法是：两个指针p、q分别从链表1、2起点开始，每次走一步。如果其中一个到了结尾（为null），则让其从另一个链表的头部开始。

显然，如果两个链表相交，则两个指针第二圈一定会重合，重合的地方就是开始的地方（都走了$A+B+C$的长度）。如果没有相交，最后它们都会走到结尾，为null，因为都走了$A+B+2C$的长度。

```c++
struct ListNode {
         int val;
         ListNode *next;
         explicit ListNode(int x) : val(x), next(nullptr) {}
     };


class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *p=headA, *q=headB;
        while (p!=q)
        {
            p = p?p->next:headB;
            q = q?q->next:headA;
        }
        return p;
    }
};
```

PS:`nullptr==nullptr`为true。

## 238-Product of Array Except Self「数组」

[Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

> 给定一个数组，要求返回一个数组，每个元素等于原数组除对应元素外其它所有元素的乘积。

先用一个数组记录累积，`product[i]`记录着从`nums[0]`到`nums[i-1]`的乘积。然后再从数组右侧开始遍历，用一个元素记录累积，这样就可以把当前元素`nums[i]`空出来了。

```java
public int[] productExceptSelf(int[] nums) {
        int[] product = new int[nums.length];
        product[0] = 1;

        for(int i = 1; i<nums.length; i++)
        {
            product[i] = product[i-1] * nums[i-1];
        }
        int right = 1;
        for(int i = nums.length-1; i>=0; i--)
        {
            product[i] = product[i] * right;
            right = right * nums[i];
        }
        return product;
    }
```

## 581-Shortest Unsorted Continuous Subarray[两个指针]

[Shortest Unsorted Continuous Subarray](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/)

>给定一个数组，找到一个连续子数组，如果这个子数组升序排序，那么整个数组升序有序。

采用两个指针，直接前后开始找子数组的起点和终点这种方式不可行。比如：

```
Input: [2, 6, 4, 8, 10, 9, 15]
Output: 5
Explanation: You need to sort [6, 4, 8, 10, 9] in ascending order to make the whole array sorted in ascending order.
```

这里应该从6开始，而不是从4开始。

转换一下思路，从数组最后向前扫描，找到起点；从数组开始向后扫描，找到终点。

```java
public int findUnsortedSubarray(int[] nums) {
        int start=-1, end = -1;
        int max = nums[0], min = nums[nums.length-1];
        for(int i =1; i<nums.length; i++)
        {
            max = Math.max(nums[i], max); // 数组前部分最大值
            min = Math.min(nums[nums.length-1-i], min); //数组后面的而最小值
            if(nums[i] < max)
            {
                end = i;
            }
            if(nums[nums.length-1-i]>min)
                start = nums.length-1-i;
        }
        if(start==-1&&end==-1)
            return 0;
        return end - start + 1;
    }
```