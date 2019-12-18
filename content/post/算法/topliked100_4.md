---
title: "Topliked100（四）"
date: 2019-12-18T10:50:39+08:00
lastmod: 2019-12-18T10:50:39+08:00
draft: false
tags: ['Leetcode']
categories: ['算法']
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



