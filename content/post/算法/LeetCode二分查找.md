---
title: "LeetCode二分查找"
date: 2020-01-18T20:02:25+08:00
lastmod: 2020-01-18T20:02:25+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# LeetCode二分查找

## 33.Search in Rotated Sorted Array

[Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

>一个递增数组，随机rotate一下，(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).查找制定元素，找到返回下标，否则返回-1.

```java
public int search(int[] nums, int target) {
        int low = 0, high = nums.length - 1;
        int mid;
        while (low<=high)
        {
            mid = (low + high) >> 1;
            if(nums[mid]==target)
                return mid;

            if(nums[mid] < nums[low])  //左边无序
            {
                if((nums[mid]<target)&&(target<=nums[high]))
                    low = mid + 1;
                else
                    high = mid -1;
            }
            else if(nums[mid] > nums[high])  //右边无序
            {
                if((nums[low] <= target) && (target < nums[mid]))
                    high = mid - 1;
                else 
                    low = mid + 1;
            }
            else //no rotate，正常的二分查找
            {
                if(nums[mid] < target)
                    low = mid + 1;
                else 
                    high = mid -1;
            }
        }
        return -1;
    }
```

##81.Search in Rotated Sorted Array II

[Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)

>同上一题，只不过包括重复元素。

思路和上题相似，只不过需要手动过滤掉重复的情况，也就是当遇到`nums[low]==nums[mid]`以及`nums[mid]==nums[high]`的时候，跳过它们。

```java
public boolean search(int[] nums, int target) {
        int low = 0, high = nums.length - 1;
        while (low<=high)
        {
            int mid = low + (high - low) / 2;
            if(nums[mid]==target)
                return true;
            if(nums[low]<nums[mid]) // 左边有序
            {
                if((nums[low]<=target)&&(target<nums[mid]))
                {
                    high = mid-1;
                }
                else
                    low = mid + 1;
            }
            else if (nums[mid]<nums[high])  //右边有序
            {
                if((nums[mid]<target)&&(target <=nums[high]))
                    low = mid + 1;
                else
                    high = mid - 1;
            }
          /*此时左右都不有序，也就是出现了重复值的问题。不一定是nums[low]==nums[mid]==nums[high]，也可能只满足一个，另一个是不等的关系，比如3 1 t=1的情况 */
            else  
            {
                if(nums[low]==nums[mid]) low++;
                if(nums[mid]==nums[high]) high--;
            }
        }
        return false;
    }
```

