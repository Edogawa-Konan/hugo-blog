---
title: "Leetcode(39-76)"
date: 2019-11-07T22:25:52+08:00
draft: false
tags: ["LeetCode"]
categories: ["算法"]
---

# Leetcode(39-76)

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

## 43-Multiply Strings「字符串」

[Multiply Strings](https://leetcode.com/problems/multiply-strings/)

> 计算两个字符串的乘积。

考虑乘法公式，计算过程如图：

![](https://prime-blog.oss-cn-hangzhou.aliyuncs.com/leetcode/leetcode43.jpg)

```java
public String multiply(String num1, String num2) {
        int[] num = new int[num1.length() + num2.length()];
        int m = num1.length();
        int n = num2.length();
        for(int i = m-1; i>=0; i--)
            for (int j = n-1; j>=0; j--)
            {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int sum = mul + num[i + j + 1];
                num[i+j] += sum / 10;
                num[i+j+1] = sum % 10;
            }
        StringBuilder sb = new StringBuilder();
        for(int p : num) if(!(sb.length() == 0 && p == 0)) sb.append(p); //去除前导0
        return sb.length()==0? "0":sb.toString();
}
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

## 50-Pow(x, n)「分治」

[Pow(x, n)](https://leetcode.com/problems/powx-n/)

>计算pow(x, n)；

考虑把指数n表示成二进制。假设n==9，于是有`n = 9 = 2^3 + 2^0 = 1001`。所以结果为`x^9 = x^(2^3) * x^(2^0)`。也就是说，从后往前，只需要每次遇到比特位**1**，那么就把结果乘`x^(2^i)`，其中`i`表示第i个比特位。

```java
public double myPow(double x, int n) {
        double ans = 1;
        long absN = Math.abs((long)n); //指数
        while(absN > 0) {
            if((absN&1)==1) ans *= x;
            absN >>= 1;
            x *= x; //以 2， 4， 8， 16， ……
        }
        return n < 0 ?  1/ans : ans;
    }
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

## 54-Spiral Matrix「矩阵」

[Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)

> 遍历一个螺旋矩阵。

```java
public List<Integer> spiralOrder(int[][] matrix) {
        if(matrix.length==0)
            return new ArrayList<>();
        int rowBegin = 0, rowEnd = matrix.length-1, colBegin=0, colEnd=matrix[0].length-1;
        List<Integer> ans = new ArrayList<>();
        while (rowBegin<=rowEnd&&colBegin<=colEnd)
        {
            for(int j = colBegin; j<= colEnd; j++)
                ans.add(matrix[rowBegin][j]);
            rowBegin++;
            for(int i = rowBegin; i<= rowEnd; i++)
                ans.add(matrix[i][colEnd]);
            colEnd--;
            if(rowBegin<=rowEnd)
            {//边界检查是需要的,注意这里检查和下面matrix的维度有关
                for(int j=colEnd; j>=colBegin; j--)
                    ans.add(matrix[rowEnd][j]);
                rowEnd--;
            }
            if(colBegin<=colEnd) {
                for (int i = rowEnd; i >= rowBegin; i--)
                    ans.add(matrix[i][colBegin]);
                colBegin++;
            }
        }
        return ans;
    }
```

## 59-Spiral Matrix II「矩阵」

[Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/)

>同样是螺旋矩阵问题，这次是填充一个$n\times n$的矩阵，用1～$n^2$。

```java
public int[][] generateMatrix(int n) {
        int[][] matrix = new int[n][n];
        int rowBegin = 0, rowEnd = matrix.length-1, colBegin=0, colEnd=matrix[0].length-1;
        int count = 1;
        while (rowBegin<=rowEnd&&colBegin<=colEnd)
        {
            for(int j = colBegin; j<= colEnd; j++)
                matrix[rowBegin][j] = count++;
            rowBegin++;
            for(int i = rowBegin; i<= rowEnd; i++)
                matrix[i][colEnd] = count++;
            colEnd--;
            if(rowBegin<=rowEnd)
            {//边界检查是需要的,上面对rowBegin进行了++，注意这里检查和下面matrix的维度有关
                for(int j=colEnd; j>=colBegin; j--)
                    matrix[rowEnd][j] = count++;
                rowEnd--;
            }
            if(colBegin<=colEnd) {
                for (int i = rowEnd; i >= rowBegin; i--)
                    matrix[i][colBegin] = count++;
                colBegin++;
            }
        }
        return matrix;
    }
```

## 61-Rotate List「链表」

[Rotate List](https://leetcode.com/problems/rotate-list/)

>给定一个链表，和一个非负整数k，讲链表向右循环k次，输出结果。
>
>Input: 1->2->3->4->5->NULL, k = 2
>Output: 4->5->1->2->3->NULL
>Explanation:
>rotate 1 steps to the right: 5->1->2->3->4->NULL
>rotate 2 steps to the right: 4->5->1->2->3->NULL

先求出链表长度`len`，然后可以得到头结点与循环后的头结点之间的距离`len-k%len`。然后修改链表即可。

```java
public static class ListNode {
        int val;
        ListNode next;

        ListNode(int x) {
            val = x;
        }
    }

    public ListNode rotateRight(ListNode head, int k) {
        if(head==null)
            return null;
        int len = 1;
        ListNode tail = head;
        while (tail.next!=null)
        {
            len++;
            tail = tail.next;
        }
        int step = len - k%len; //头结点移动多少步
        tail.next = head; //链表成环
        ListNode pre_ans = head; //移动step-1步，找到目标结点的前一个结点
        for(int i = 1; i< step; i++)
            pre_ans = pre_ans.next;
        ListNode ans = pre_ans.next;
        pre_ans.next = null;
        return ans;
    }
```

## 62-Unique Paths「DP」

[Unique Paths](https://leetcode.com/problems/unique-paths/)

>给定一个矩阵，每次只能向右或者向下走，问从左上角到右下角的最多有多少路径。

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

## 63-Unique Paths II「DP」

[Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

>同上一题的背景，但是其中有障碍物。

dp思路也一样，只是增加了障碍物的判断。

```java
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if(obstacleGrid[0][0]==1)
            return 0;
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = 1;
        for(int i = 1; i<m; i++)
            dp[i][0] = obstacleGrid[i][0] == 1? 0: dp[i-1][0];
        for(int j = 1; j<n; j++)
            dp[0][j] = obstacleGrid[0][j] == 1? 0: dp[0][j-1];
        for(int i = 1; i<m; i++)
            for(int j = 1; j<n; j++)
            {
                if(obstacleGrid[i][j]==1)
                    dp[i][j] = 0;
                else
                    dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }

        return dp[m-1][n-1];
    }
```

## 64-Minimum Path Sum「DP」

[Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

和上一题类似的场景，只不过要求经过的路径上和最小。

dp的递推公式可以概括为：

```python
dp[i][j] = min(dp[i][j-1], dp[i-1][j]) + grid[i][j]

```

边界条件就是第一行和第一列的初始化。

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        vector<vector<int>> dp(grid.size(), vector<int> (grid[0].size(), grid[0][0]));
        auto m = grid.size();
        auto n = grid[0].size();
        for(int i = 1; i< m; i++)
            dp[i][0] = dp[i-1][0] + grid[i][0];
        for(int j = 1; j<n;j++)
            dp[0][j] = dp[0][j-1] + grid[0][j];
        for (int i = 1; i < m; ++i) {
            for(int j=1;j<n;j++)
                dp[i][j] = min(dp[i][j-1], dp[i-1][j]) + grid[i][j];
        }
        return dp[m-1][n-1];
    }
};

```

## 70-Climbing Stairs「DP」

[Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

> 每次只能爬1级或2级，问有几种方式爬到n级。

表面看有点像斐波那契数列，仔细想想又不是。考虑使用dp，递推公式可以概括为:

```python
dp[i] = dp[i-1] + dp[i-2]  # dp[i]表示到达i级可用的方式数

```

由于此公式比较简单，其实可以简化不用保存整个dp数组。

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n==1)
            return 1;
        if(n==2)
            return 2;
        vector<int> dp(n+1, 0);
        dp[1] = 1;
        dp[2] = 2;
        for (int i =3; i<=n;i++)
            dp[i] = dp[i-1] + dp[i-2];
        return dp[n];
    }
};

```

## 72-Edit Distance「DP」:warning:

[Edit Distance](https://leetcode.com/problems/edit-distance/)

hard难度的dp，参考discuss中的讨论。

用`dp[i][j]`表示最小操作次数从`word1[0..i)`转换到`word2[0...j)`，由此可分解成子问题如下：

- 如果`word1[i - 1] == word2[j - 1]`，那么`dp[i][j] = dp[i - 1][j - 1]`.
- 如果`word1[i - 1] != word2[j - 1]`，那么可以分为三种情况，应取以下三种情况的最小值：
  - **Replace** `word1[i - 1]` by `word2[j - 1]` (`dp[i][j] = dp[i - 1][j - 1] + 1`);
  - If `word1[0..i - 1) = word2[0..j)` then **delete** `word1[i - 1]` (`dp[i][j] = dp[i - 1][j] + 1`);
  - If `word1[0..i) + word2[j - 1] = word2[0..j)` then **insert** `word2[j - 1]` to `word1[0..i)` (`dp[i][j] = dp[i][j - 1] + 1`).

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        auto m = word1.size();
        auto n = word2.size();
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        for (int i=1; i<=m; i++)
            dp[i][0] = i; // 转化为空串就是连续删除
        for(int j =1; j<=n; j++)
            dp[0][j] = j; //空串插入就是连续插入
        for (int i = 1; i<=m; i++)
            for(int j = 1; j<=n; j++)
            {
                if(word1[i-1] == word2[j-1])
                    dp[i][j] = dp[i-1][j-1];
                else
                {
                    dp[i][j] = min(dp[i-1][j-1]+1, min(dp[i-1][j] +1, dp[i][j-1] +1));
                }
            }
        return dp[m][n];
    }
};

```

下面是一个例子：

Let's say we have 2 words **"abcde"** and **"fghij"**, and we already know the min distance from **"abcd"** to **"fgh"**.

```
a b c d
f g h

```

Now we would like to go a step further and convert **"abcde"** to **"fghi"**.

```
a b c d e
f g h i

```

There're 3 ways to accomplish it:

1. Replace "e" by "i".

```
a b c d        a b c d e 
          ->                           ->  dp[i-1][j-1] + 1 (for replacement)
f g h          f g h i

```

1. If we know the min distance from **"abcd"** to **"fghi"**, then for **"abcde"**, we just need to delete **"e"**.

```
a b c d        a b c d e(delete)
          ->                           ->  dp[i-1][j] + 1 (for deletion)
f g h i        f g h i

```

1. If we know the min distance from **"abcde"** to **"fgh"**, then we need to add an **"i"** to **"abcde"**

```
a b c d e      a b c d e (add an "i")
          ->                           ->  dp[i][j-1] + 1 (for insertion)
f g h          f g h i

```

## 75-Sort Colors「排序」

[Sort Colors](https://leetcode.com/problems/sort-colors/)

排序算法实现，三种方法：

1. bucket sort

   ```c++
   void sortColors2(vector<int>& nums)
       {
           int n0 = 0, n1 = 0, n2 = 0;
           for(auto & v : nums)
           {
               if(v == 0)
               {
                   n0++;
               } else if(v == 1)
               {
                   n1++;
               } else
                   n2++;
           }
           for (int i = 0;i < n0; i++)
               nums[i] = 0;
           for (int i = n0; i < n0+n1; i++)
               nums[i] = 1;
           for (int i = n1+n0; i < nums.size(); i++)
               nums[i] = 2;
       }
   
   ```

2. 保证永远是0-1-2这种顺序，也是最amazing的方法

   ```c++
   void sortColors1(vector<int>& nums)
       {
           int n0 = -1, n1 = -1, n2 = -1;
           for(auto & v : nums)
           {
               if(v == 0)
               {
                   nums[++n2] = 2;
                   nums[++n1] = 1;
                   nums[++n0] = 0;
               } else if(v == 1)
               {
                   nums[++n2] = 2;
                   nums[++n1] = 1;
               } else
                   nums[++n2] = 2;
           }
       }
   
   ```

3. two point

   ```c++
   void sortColors(vector<int>& nums) {
           int first=0, last = nums.size()-1;
           for (int i = 0; i<=last; i++)
           {
               if(nums[i] == 0)
                   swap(nums[i], nums[first++]);
               else if (nums[i] == 2)
                   swap(nums[i--], nums[last--]);
           }
       }
   
   ```

## 76-Minimum Window Substring「滑动窗口」:warning:

[Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

> 给定字符串s和t，找到s中包括t的最小窗口。

```java
public String minWindow(String s, String t) {
        int res_start = 0, res_end = 0, len = Integer.MAX_VALUE;
        Map<Character, Integer> map = new HashMap<>();
        for(char e : t.toCharArray())
        {
            map.put(e, map.getOrDefault(e, 0) + 1);
        }
        int start=0, end=0, count=map.size();//字符种类数
        while (end<s.length())
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
                    if(map.get(e)==0)
                        count++;
                    map.put(e, map.get(e) + 1);
                }
                if(end-start < len)
                {
                    res_start = start;
                    res_end = end;
                    len = end - start;
                }
                start++;
            }
        }
        return s.substring(res_start, res_end);
    }
```

