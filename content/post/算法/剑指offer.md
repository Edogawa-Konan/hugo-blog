---
title: "剑指offer"
date: 2019-12-05T15:11:09+08:00
lastmod: 2019-12-05T15:11:09+08:00
draft: false
tags: ['剑指offer']
categories: [’算法']
---

# 剑指offer67道

## 找到有序数组中k的个数【二分查找】

先找到第一个再找到最后一个。

```java
public class Solution1 {
    public int GetNumberOfK(int [] array , int k) {
        int first = GetFirstK(array, k);
        int last = GetLastK(array, k);
        if(first>-1 && last>-1)
            return last - first + 1;
        return 0;
    }

    public static void main(String[] args) {
        int[] array = {1,2,3,3,3,3,4,5};
        Solution1 s = new Solution1();
        System.out.println(s.GetNumberOfK(array, 3));
    }
    private int GetFirstK(int[] array, int k)
    {
        int low = 0, high = array.length-1;
        int mid;
        while (low<=high)
        {
            mid = (low + high) / 2;
            if(array[mid]==k)
            {
                if(mid>0&&(array[mid-1]!=k)||mid==0)
                    return mid;
                else
                {
                    high = mid - 1;
                }
            }else if(array[mid] > k)
                high = mid - 1;
            else
                low = mid + 1;
        }
        return -1;
    }
    private int GetLastK(int[] array, int k)
    {
        int low = 0, high = array.length-1;
        int mid;
        while (low<=high)
        {
            mid = (low + high) / 2;
            if(array[mid]==k)
            {
                if(mid<array.length-1&&array[mid+1]!=k||mid==array.length-1)
                    return mid;
                else
                {
                    low = mid + 1;
                }
            }else if(array[mid] > k)
                high = mid - 1;
            else
                low = mid + 1;
        }
        return -1;
    }
}
```

## 逆序对【归并排序】

```java
public class Solution2 {
    private int res = 0;

    public int InversePairs(int[] array) {
        int[] copy = new int[array.length];
        merge_sort(array, copy, 0, array.length - 1);
        return res;
    }

    private void merge(int[] array, int[] copy, int left, int mid, int right) {
        int i = left, j = mid + 1;
        for (int index_copy = left; index_copy <= right; index_copy++) {
            if (i > mid) {
                copy[index_copy] = array[j++];
            } else if (j > right) {
                copy[index_copy] = array[i++];
            } else if (array[i] > array[j]) {
                res += mid - i + 1;
                res %= 1000000007;
                copy[index_copy] = array[j++];
            } else {
                copy[index_copy] = array[i++];
            }
        }
        for(int m=left;m<=right;m++) //复制别忘了
            array[m]=copy[m];
    }

    private void merge_sort(int[] array, int[] copy, int start, int end) {
        if (start < end) {
            int mid = start + (end - start) / 2;
            merge_sort(array, copy, start, mid);
            merge_sort(array, copy, mid + 1, end);
            merge(array, copy, start, mid, end);
        }
    }

    public static void main(String[] args) {
        Solution2 s = new Solution2();
        int[] array = {1, 8, 3, 4, 5, 6, 7, 0};
        System.out.println(s.InversePairs(array));
    }
}

```

