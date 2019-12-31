---
title: "Topliked100（四）"
date: 2019-12-18T10:50:39+08:00
lastmod: 2019-12-18T10:50:39+08:00
draft: false
tags: ["Leetcode"]
categories: ["算法"]
---

# Topliked100(四)

## 3-Longest Substring Without Repeating Characters「滑动窗口」

[Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

>给定一个字符串，找到最长不包含重复元素的子串长度。

```java
public int lengthOfLongestSubstring(String s) {
        if(s.isEmpty())
            return 0;
        Map<Character, Integer> map = new HashMap<>();
        int res = Integer.MIN_VALUE;
        int start = 0, end = 0, count = 0;//count 记录重复字符的数量
        while (end<s.length())
        {
            char cur = s.charAt(end);
            map.put(cur, map.getOrDefault(cur, 0) + 1);
            if(map.get(cur) > 1)
                count++;
            end++;
            while (count>0)
            {//有count个重复元素了，需要移动start使得[start, end)有效
                char e = s.charAt(start);
                if (map.get(e) > 1)
                    count--;
                map.put(e, map.get(e) - 1);
                start++;
            }
          //此时[start, end)是有效的
            res = Math.max(res, end-start);
        }
        return res;
    }
```

## 438-Find All Anagrams in a String「滑动窗口」

[Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

>给定一个字符串s和p，找到s中所有的起始下标i，使得s从i开始的子串的字符全都在p中。

```java
public List<Integer> findAnagrams(String s, String p) {
        Map<Character, Integer> map = new HashMap<>();
        List<Integer> res = new LinkedList<>();
        for(char e: p.toCharArray())
        {
            map.put(e, map.getOrDefault(e, 0) +1);
        }
        int count = map.size();  // 字符种类数

        int start = 0, end = 0;
        while (end < s.length())
        {
            char cur = s.charAt(end);
            if(map.containsKey(cur))
            {
                map.put(cur, map.get(cur) - 1);
                if(map.get(cur)==0)
                    count--;
            }
            end++;
            while (count==0)
            {
                char e = s.charAt(start);
                if(map.containsKey(e))
                {
                    if(map.get(e) == 0)
                        count++;
                    map.put(e, map.get(e) + 1);
                }
                if(end - start == p.length())
                    res.add(start);
                start++;
            }
        }
        return res;
    }
```

## 543-Diameter of Binary Tree「DFS」

[Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

>求一个二叉树的直径（最长路径从一个结点到另一个结点）。

显然，最长路径一定经过一个子树的根结点，可以转换为**求树的深度**的问题。

```java
public class Solution543 {
    public static class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
    }
    private int max = 0;
    public int diameterOfBinaryTree(TreeNode root) {
        maxdepth(root);
        return max;
    }
    private int maxdepth(TreeNode root)
    {
        if(root==null)
            return 0;
        else
        {
            int left = maxdepth(root.left);
            int right = maxdepth(root.right);
            max = Math.max(left + right, max);
            return Math.max(left, right) + 1;
        }
    }
}

```

## 560-Subarray Sum Equals K[presum+哈希]

[Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

>给定一个数组和一个整数k，找到连续子数组的数量，使得这个子数组和为k

这里计算和累积数组，因为`sum(i, j) = sum(0, j) - sum(i)`。

用一个map保存**累积和-频率**。

```java
public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        int res = 0;
        map.put(0, 1); //用于当sums==k的时候，下面的res+=1计算
        int sums = 0;
        for (int n: nums)
        {
            sums += n;
            if(map.containsKey(sums - k))
                //数组nums不一定全是正数，因此某些pre sum频率可能大于1
                res += map.get(sums-k);  
            map.put(sums, map.getOrDefault(sums, 0) + 1);
        }
        return res;
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

## 647-Palindromic Substrings「回文串/技巧型」

[Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

>给定一个字符串，找到所有回文子串的数量，起始和终止下标不一样的子串认为不是同一个。

采用一种比较技巧的方法，每次从中间向两边“扩张”，分为奇数和偶数两种情况。

```java
private int count = 0;
    public int countSubstrings(String s) {
        if(s.length()==0)
            return 0;
        for(int i = 0;i<s.length(); i++)
        {
            expand(s, i, i);
            expand(s, i, i+1);
        }
        return count;
    }
    private void expand(String s, int left, int right)
    {
        while (left>=0&&right<s.length()&&s.charAt(left) == s.charAt(right))
        {
            count++;
            left--;
            right++;
        }
    }
```

## 739-Daily Temperatures「栈」

[Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

>题目大意给定一个数组，对每个元素找到需要走几步才能到达下一个大于当前值的元素。

用栈保存元素下标

```java
public int[] dailyTemperatures(int[] T) {
    Stack<Integer> stack = new Stack<>();
    int[] res = new int[T.length];
    for(int i = 0;i<T.length; i++)
    {
        while (!stack.empty()&&T[stack.peek()]<T[i])
        {
            int top = stack.pop();
            res[top] = i - top;
        }
        stack.push(i);
    }
    return res;
}
```

## 621-Task Scheduler「贪心」

[Task Scheduler](https://leetcode.com/problems/task-scheduler/)

>给定一个任务序列，和一个n，要求相同任务至少间隔n个才行，问最终执行时间。
>
>Input: tasks = ["A","A","A","B","B","B"], n = 2
>Output: 8
>Explanation: A -> B -> idle -> A -> B -> idle -> A -> B.

采用贪心的思想，首先找到任务序列之中的出现频率最高的，比如例子中的A，然后可以得到A--A--A，其中-表示空槽，这样，只需要每次把剩余元素顺序填空槽即可保证满足题意，如果剩余空槽，则表示填充不满，因此必须有空闲槽，否则表示空槽不够，因此总时间等于序列长度。

```java
public int leastInterval(char[] tasks, int n) {
        int max = -1;  // 最大频率
        int maxCount = 0;  // 有几个最大频率的，考虑AAABBBC这种，AB都是最大频率
        int[] counter = new int[26];
        for(char e : tasks)
        {
            counter[e-'A']++;
            if(counter[e-'A']>max)
            {
                max = counter[e-'A'];
                maxCount = 1;
            }
            else if(counter[e-'A']==max)
                maxCount++;
        }
        int partCount = max - 1;
        int emptySlots = partCount * (n-(maxCount-1));
        int remainTasks = tasks.length - max * maxCount;
        int idles = Math.max(0, emptySlots-remainTasks);
        return tasks.length + idles;
    }
```

