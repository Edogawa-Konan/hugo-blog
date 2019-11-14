---
title: "Topliked100"
date: 2019-11-07T22:25:52+08:00
draft: false
tags: ["Leetcode"]
categories: ["Leetcode"]
---

# Top liked 100

## 39-Combination Sum「回溯法」

[Combination Sum](https://leetcode.com/problems/combination-sum/)

注意此处的是**无限制数量**，回溯法可以解决

```c++
#include <vector>

using namespace std;

class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> res;
        vector<int> stack;
        backtrace(candidates, target, stack, 0, res);
        return res;
    }

private:
    void backtrace(vector<int> & candidates, int target, vector<int>& stack, int begin, vector<vector<int>>& res)
    {
        if(target==0)
        {
            res.push_back(stack);
            return;
        }
        for(int i=begin; i< candidates.size() && target >= candidates[i]; i++)
        {
            stack.push_back(candidates[i]);
            backtrace(candidates, target-candidates[i], stack, i, res);//注意此处是i
            stack.pop_back();
        }
    }
};
```

## 40-Combination Sum II「回溯法」

[Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

同上一题，要求每个元素只能用一次。

```c++
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> res;
        vector<int> stack;
        backtrace(candidates, target, stack, 0, res);
        return res;
    }

private:
    void backtrace(vector<int> & candidates, int target, vector<int>& stack, int begin, vector<vector<int>>& res)
    {
        if(target==0)
        {
            res.push_back(stack);
            return;
        }
        for(int i=begin; i< candidates.size() && target >= candidates[i]; i++)
        {
            if(i==begin||candidates[i]!=candidates[i-1])
            {//此处的判断条件是用来防止结果中有重复的。
                stack.push_back(candidates[i]);
                backtrace(candidates, target-candidates[i], stack, i+1, res);
                stack.pop_back();
            }
            
        }
    }
};
```



## 216-Combination Sum III「回溯法」

[Combination Sum III](https://leetcode.com/problems/combination-sum-iii/)

限制序列长度，同时不能重复利用。

```c++
class Solution {
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> res;
        vector<int> stack;
        backtrace(k, n, stack, res);
        return res;
    }

private:
    void backtrace(int count, int target, vector<int> &stack, vector<vector<int>> &res) {
        if (target == 0 && stack.size() == count) {
            res.push_back(stack);
            return;
        } else if (stack.size() == count)
            return;
        for (int i = stack.empty()? 1 : stack.back() + 1; i<10 && target >= i; i++)
        {//注意起始条件
            stack.push_back(i);
            backtrace(count, target-i, stack, res);
            stack.pop_back();
        }
    }
};
```

## 46-Permutations「回溯」

[Permutations](https://leetcode.com/problems/permutations/)

依旧和上面类似的“模板”。不包含重复值的回溯。

```c++
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> stack;
        backtrace(res, stack, nums);
        return res;
    }

private:
    void backtrace(vector<vector<int>>& res, vector<int>& stack, vector<int>& nums)
    {
        if(stack.size() == nums.size())
        {
            res.push_back(stack);
            return;
        }
        for (const auto & i: nums)
        {
            if(find(stack.begin(), stack.end(), i) == stack.end())//stack中不包含i的情况下
            {
                stack.push_back(i);
                backtrace(res, stack, nums);
                stack.pop_back();
            }
        }
    }
};
```

## 47-Permutations II「回溯」

[Permutations II](https://leetcode.com/problems/permutations-ii/)

同上述模板，包含重复值。

```c++
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> stack;
        sort(nums.begin(), nums.end());//下面的if判断要求这里要排序
        vector<bool> used = vector<bool >(nums.size(), false);
        backtrace(res, stack, nums, used);
        return res;
    }

private:
    void backtrace(vector<vector<int>>& res, vector<int>& stack, vector<int>& nums, vector<bool>& used)
    {
        if(stack.size() == nums.size())
        {
            res.push_back(stack);
            return;
        }
        for (int i =0; i<nums.size(); ++i)
        {
            if(used[i] || i > 0 && nums[i] == nums[i-1] && !used[i - 1]) continue;
          //后一个条件表示此时前一个元素的所有组合完成，而此元素和前一个元素相等故而产生的所有组合和前面一样，因此跳过。
            
                used[i] = true;
                stack.push_back(nums[i]);
                backtrace(res, stack, nums, used);
                stack.pop_back();
                used[i] = false;
            
        }
    }
};
```

## 48-Rotate Image「技巧」

[Rotate Image](https://leetcode.com/problems/rotate-image/)

题目要求顺时针旋转一个矩阵，可以采用如下算法：

1. 反转每一行
2. 沿着主对角线，两边元素互换

另外，如果希望采用逆时针旋转90度，可以将上述步骤反过来即可！

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        std::reverse(matrix.begin(), matrix.end());
        for(int i =0 ;i<matrix.size(); i++)  //行索引
            for(int j=i+1; j<matrix.size(); j++)  //列索引
                std::swap(matrix[i][j], matrix[j][i]);
    }
};
```

## 49-Group Anagrams「排序+map」

[Group Anagrams](https://leetcode.com/problems/group-anagrams/)

anagram意为变位词，也就是说交换字母顺序之后形成的新词。

题目要求将相同字符的词分为一组，思路是先按字符排序，以这个为key，然后依次将单词放入unordered_map之中。

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> res;
        for (string s: strs)
        {
            string tmp = s;
            std::sort(s.begin(), s.end());
            res[s].push_back(tmp);
        }
        vector<vector<string>> fina;
        for (auto &s : res)
        {
            fina.push_back(s.second);
        }
        return fina;
    }
};
```

## 53-Maximum Subarray「DP」

[Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

题目给定一个数组，就连续和的最大值。

采用简单的dp思想，递推关系式如下：

```python
maxSubArray(A, i) = maxSubArray(A, i - 1) > 0 ? maxSubArray(A, i - 1) : 0 + A[i]; 
```

其中`maxSubArray(A, i)`表示数组A中0～i下标的子数组最大值（包括i）。

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int preview = nums[0];
        int Max = preview;
        for (int i = 1;i<nums.size();i++)
        {
            preview = preview > 0 ? preview + nums[i] : nums[i];
            if(preview > Max)
                Max = preview;
        }
        return Max;
    }
};

```

## 55-Jump Game「贪心」

[Jump Game](https://leetcode.com/problems/jump-game/solution/)

采用贪心法，每扫描一个元素，记录当前所能到达的最远距离。

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int max_reach = 0, i;
        for (i = 0; i < nums.size() && i <= max_reach; ++i) {
            max_reach = max(nums[i] + i, max_reach);
        }
        return i == nums.size();
    }
};

```

## 56-Merge Intervals「排序」

[Merge Intervals](https://leetcode.com/problems/merge-intervals/)

将区间合并，直接先对区间左侧排序后合并即可。

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        if(intervals.empty())
            return res;
        sort(intervals.begin(), intervals.end(), [](const vector<int> & interval1, const vector<int> & interval2) -> bool { return interval1[0] < interval2[0];});
        res.push_back(intervals[0]);
        for (int i = 1; i< intervals.size(); ++i)
        {
            if(intervals[i][0] > res.back()[1])
                res.push_back(intervals[i]);
            else
                res.back()[1] = max(intervals[i][1], res.back()[1]);
        }
        return res;
    }
};

```

## 62-Unique Paths「DP」

[Unique Paths](https://leetcode.com/problems/unique-paths/)

每次只能向右和向下两种选择，假设我们使用`dp[i][j]`表示到第i、j个格子的路径数量，由此可以得到以下递推公式：`dp[i][j] = dp[i][j - 1] + dp[i - 1][j]`.

故而可以写出如下代码：

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        int dp[m][n];
        for(int j=0; j< n; j ++)
            dp[0][j] = 1;
        for(int i=0; i<m; i++)
            dp[i][0] = 1;
        for (int i = 1; i < m; i ++)
            for(int j = 1; j < n; j++)
                dp[i][j] = dp[i][j-1] + dp[i-1][j];
        return dp[m-1][n-1];
    }
};

```

---

除了上述算法，还可以根据公式直接求解此问题，利用排列组合的思想，路径数应该为$C_n^k$，其中n为需要移动的步数，即m-1+n-1；k表示向下的步数，即m-1（也可以是向右的步数，n-1）。可以得到的代码如下：

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        int length = m + n -2;
        return (int)nChoosek(length, m-1);

    }

    constexpr double nChoosek(double n, double k )
    {
        if (k > n) return 0;
        if (k * 2 > n) k = n-k;
        if (k == 0) return 1;

        int result = n;
        for( int i = 2; i <= k; ++i ) {
            result = result * (n-i+1) / i;
        }
        return result;
    }
};

```















