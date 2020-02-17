---
title: "Topliked100(二)"
date: 2019-12-12T16:06:11+08:00
lastmod: 2019-12-12T16:06:11+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
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

