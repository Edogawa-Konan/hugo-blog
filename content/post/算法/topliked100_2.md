---
title: "Topliked100(二)"
date: 2019-12-12T16:06:11+08:00
lastmod: 2019-12-12T16:06:11+08:00
draft: false
tags: ["LeetCode"]
categories: ["算法"]
---

# Topliked100(二)

## 121-Best Time to Buy and Sell Stock「贪心」

[Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

题目就是找到数组中两个数相差最大的。可以采用双重循环的简单思路，但是不够好。

对题目稍加转换，求相邻两个元素的差，得到差值数组，问题即可转化为求这个差值数组的最大和问题。

```c++
 public int maxProfit(int[] prices) {
        int maxCur = 0, maxSoFar = 0;
        for(int i = 1; i < prices.length; i++) {
            maxCur = Math.max(0, maxCur += prices[i] - prices[i-1]);
            maxSoFar = Math.max(maxCur, maxSoFar);
        }
        return maxSoFar;
    }
```

## 124-Binary Tree Maximum Path Sum「贪心/递归」:warning:

[Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/discuss/39775/Accepted-short-solution-in-Java)

一条路径一定有个最高结点，用`maxsubtree（TreeNode * root）`来表示最高结点为root的子树的路径和。

```c++
class Solution {
private:
    int res = INT32_MIN;
public:
    int maxPathSum(TreeNode* root) {
        maxsubtree(root);
        return res;
    }

    int maxsubtree(TreeNode* root)
    {
        if(root== nullptr)
            return 0;
        int left = max(0, maxsubtree(root->left));
        int right = max(0, maxsubtree(root->right));
        res =  max(res, left+right+root->val);
        return max(left, right) + root->val;
    }
};
```

## 128-Longest Consecutive Sequence「数组」:warning:

[Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)

给定一个数组，找到可以凑成的最长连续序列。

考虑用set保存所有数字，然后遍历数组，针对数组nums中每个元素`x`，如果`x-1`不在集合中，那么`x`可以认为是一个候选序列的开始，然后逐一判断`x+1`、`x+2`……，更新最大长度即可。

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> s(nums.begin(), nums.end());
        int best = 0;
        int y;
        for(auto & x : s)
        {
            if(s.find(x-1) == s.end())
            {// x是序列的开始
                y = x + 1;
                while (s.find(y) != s.end())
                {
                    y = y + 1;
                }
                best = max(best, y-x);
            }
        }
        return best;

    }
};
```

## 136-Single Number「异或」

[Single Number](https://leetcode.com/problems/single-number/)

题目：给定一个数组，里面有一个元素出现一次，其它的都出现两次。找出这个出现仅仅一次的家伙。

将所有元素逐一异或即可。**异或具有交换性。**

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int res = 0;
        for (const auto & v:nums)
            res ^= v;
        return res;
    }
};
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

## 139-Word Break「DP」

[Word Break](https://leetcode.com/problems/word-break/)

给定字符串，判断是否能被一个字典中单词完全拆分。

考虑使用`dp[i]`表示以i结尾的子串是否满足，则针对每个i，对其前面所有的元素进行遍历，如果存在j满足要求`dp[j]==true`且`s[j..i]`在字典中，令`dp[i]==true`。

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        vector<bool> dp(s.size()+1, false);
        dp[0]=true;
        for(int i=1;i<s.length();i++)
        {
            for(int j=i-1; j>=0; j--)
            {
                if(dp[j])
                {
                    string word = s.substr(j, i-j);
                    if(find(wordDict.begin(), wordDict.end(), word) != wordDict.end())
                    {
                        dp[i] = true;
                        break;
                    }
                }
            }
        }
        return dp[s.length()];
    }
};
```

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

## 146-LRU Cache[链表+map]

[LRU Cache](https://leetcode.com/problems/lru-cache/)

实现LRU算法。用一个链表表示操作，其中存储key。最新的操作在队首，最晚的在队末，每次维持这个链表。

对于get和put操作，使用map记录key->(value, it)，这里it是存储key的链表结点的迭代器。

```c++
class LRUCache {
private:
    list<int> L;
    unordered_map<int, pair<int, list<int>::iterator>> mp;
    int capacity;

    void update(int key)
    { // 更新使用记录的list
        auto it = mp[key].second;
        L.erase(it);
        L.push_front(key);
        mp[key].second = L.begin();
    }

public:
    explicit LRUCache(int capacity): capacity(capacity) {}

    int get(int key) {
        if(mp.find(key) == mp.end())
            return -1;
        else
        {
            update(key);
            return mp[key].first;
        }
    }

    void put(int key, int value) {
        if(mp.find(key) == mp.end())
        {
            if(mp.size() == capacity)
            {//容量超限制
                mp.erase(L.back());
                L.pop_back();
            }
            L.push_front(key);
            mp[key] = make_pair(value, L.begin());
        } else
        {
            mp[key].first = value;
            update(key);
        }
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

## 152-Maximum Product Subarray

[Maximum Product Subarray]()

>给定一个数组，求最大连续积。

本题可以分为两种场景，`aAbB`和`aAbBc`两种情况，大写表示正，小写表示负数。第一种最大值就是其本身，而第二种情况最大值在于`aAbB`和`AbBc`之间。

因此设置两个方向，前后同步开始，更新最大值！

```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int front = 1;
        int back = 1;
        int res = INT_MIN;
        for(int i = 0; i< nums.size(); i++)
        {
            front *= nums[i];
            back *= nums[nums.size()-1 - i];
            res = max(res, max(front, back));
            if(front==0)
                front = 1;
            if(back==0)
                back = 1;
        }
        return res;
    }
};
```

## 155-Min Stack「栈」

[Min Stack](https://leetcode.com/problems/min-stack/)

> 实现一个栈，要求以常量时间返回栈的最小值。

基本思路是，用一个变量保存当前栈的最小值。但是这涉及一个问题，如果最小值被pop掉，如果找下一个最小值的问题。因此，可以在push和pop上加一点技巧，即如果push时当前值**小于等于**最小值，那么先把最小值push，再push当前值。同理，pop时，如果栈顶值等于最小值，pop一次之后，再次取栈顶元素，其表示次最小值，然后再pop一次。

```c++
class MinStack {
private:
    int min_element = INT32_MAX;
    stack<int> s;
public:
    /** initialize your data structure here. */
    MinStack() {

    }

    void push(int x) {
        if(x<=min_element)//很关键
        {
            s.push(min_element);
            min_element = x;
        }
        s.push(x);
    }

    void pop() {
        if(s.top() == min_element)
        {
            s.pop();
            min_element = s.top();
            s.pop();
        }
        else
            s.pop();
    }

    int top() {
        return s.top();
    }

    int getMin() {
        return min_element;
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

## 169-Majority Element「莫尔投票法」

[Majority Element](https://leetcode.com/problems/majority-element/)

>找到一个数组中出现次数超过一半的元素。

莫尔投票法：采取两两抵消的策略，即两个不同的元素相互抵消。

```java
public class Solution169 {

    public int majorityElement(int[] nums) {
        int major = nums[0];
        int count = 1;
        for(int i = 1; i<nums.length; i++)
        {
            if(count == 0)
            {
                count++;
                major = nums[i];
            }else if (major == nums[i])
                count++;
            else
                count--;
        }
        return major;
    }
}

```

补充一个位运算的方法：

```java
 public int majorityElement_(int[] nums) {

        int res = 0;
        for(long i = 0, mask=1; i<32; i++)//mask一定要是long类型
        {
            int bits = 0;
            for(int num : nums)
            {
                if ((num & mask) > 0)
                    bits ++;
                if (bits > nums.length / 2)
                    res |= mask;
            }
            mask<<=1;
        }
        return res;
    }
```

PS:java中`1<<32`等于`1<<0`，不同于`1<<31<<1`。因为*All shifts are done mod 32 for ints and mod 64 for longs.*

## 198-House Robber「DP」

[House Robber](https://leetcode.com/problems/house-robber/)

>题目给定一个正数组，相邻的元素不能选，求最大和问题

设`dp[i]`表示到元素i的最大值，则有：

`dp[i] = max(dp[i-2] + nums[i], dp[i-1])`。

针对这一关系，有不同的解决方法，精简后如下：

```java
public class Solution198 {
    public int rob(int[] nums) {
        int odd = 0, even = 0;
        for(int i = 0; i<nums.length; i++)
        {
            if(i%2==0)
            {
                even = Math.max(even+nums[i], odd);
            }
            else
                odd = Math.max(nums[i]+odd, even);
        }
        return Math.max(odd, even);
    }
}
```

## 207-Course Schedule[拓扑排序]

[Course Schedule](https://leetcode.com/problems/course-schedule/)

> 判断DAG是否有环

采用拓扑排序算法，[参考资料](https://oi-wiki.org/graph/topo/)。

```JAVA
public class Solution207 {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        ArrayList<ArrayList<Integer>> G = new ArrayList<>(numCourses);
        Queue<Integer> Q = new LinkedList<>();
        int count=numCourses;
        int[] degree = new int[numCourses];
        for(int i=0;i<numCourses;i++)
            G.add(new ArrayList<Integer>());
        for (int i = 0 ;i<prerequisites.length; i++)
        {
            degree[prerequisites[i][0]]++;
            G.get(prerequisites[i][1]).add(prerequisites[i][0]);
        }
        for(int i = 0; i<degree.length; i++)
        {
            if(degree[i]==0)
            {
                Q.add(i);
            }
        }
        while (Q.size()!=0)
        {
            int cur = Q.poll();
            count--;
            for(int next: G.get(cur))
            {
                if(--degree[next]==0)
                {
                    Q.add(next);
                }
            }
        }
        return count==0;
    }
}
```

