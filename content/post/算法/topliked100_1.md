---
title: "Top liked 100（一）"
date: 2019-11-07T22:25:52+08:00
draft: false
tags: ["Leetcode"]
categories: ["算法"]
---

# Top liked 100（一）

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

## 76-Minimum Window Substring「字符串」:warning:

[Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

给定字符串s和t，找到s中包括t的最小窗口。

按照模板写如下:

此处m初始化为t中字符的计数，而在while循环中，针对s[start, end]子串，对t串进行变换。外层while循环找到满足条件的子串，内层while保证子串最小。

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        vector<int> m(128, 0);
        for(auto & c : t)
            m[c]++;
        size_t start = 0, end = 0, count = 0, minStart = 0, minLen = INT_MAX;
        while (end < s.size())
        {//end右移，扫过的字符对应都-1
            if(m[s[end]] > 0) //字符在t之中
                count++;
            m[s[end++]]--;  // s中不在t中的字符会为负值
            while (count == t.size())
            {
                if(end-start < minLen)
                {
                    minStart = start;
                    minLen = end - start;
                }
              	m[s[start]]++; // start指针向后移动，这个位置字符加一
                if(m[s[start]]>0)
                    count--; //s[start]在t中，故而跳出循环
                start++;
            }
        }
        if (minLen != INT_MAX)
            return s.substr(minStart, minLen);
        return "";
    }
};

```

## 78-Subsets「回溯」

[Subsets](https://leetcode.com/problems/subsets/)

子集问题，元素没有重复值。直接套回溯模板：

```c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> tmp;
//        sort(nums.begin(), nums.end());
        backtrack(res, 0, tmp, nums);
        return res;
    }

    void backtrack(vector<vector<int>> & res, int start, vector<int>& tmp, vector<int>& nums)
    {
        res.push_back(tmp);
        for(int i=start; i<nums.size(); i++)
        {
            tmp.push_back(nums[i]);
            backtrack(res, i+1, tmp, nums);
            tmp.pop_back();
        }
    }
};

```

## 80-Subsets II「回溯」

[Subsets II](https://leetcode.com/problems/subsets-ii/)

同上题相似，但是给定数组中有重复值，需要注意条件

```c++
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> path;
        sort(nums.begin(), nums.end());
        backtrack(res, path, nums, 0);
        return res;

    }

    void backtrack(vector<vector<int>> & res, vector<int> & path, vector<int> &nums, int start)
    {
        res.push_back(path);
        for(int i=start;i<nums.size();i++)
        {
            if(i==start||nums[i]!=nums[i-1])//注意如果122这种，如果对2已经回溯过了，那么下一个2应该跳过，不然结果是一样的！
            {
                path.push_back(nums[i]);
                backtrack(res, path, nums, i+1);
                path.pop_back();
            }
        }
    }
};

```

## 79-Word Search「DFS」

[ Word Search ]( https://leetcode.com/problems/word-search/ )

路径搜索，寻找匹配字符。

```c++
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        for (int i = 0;i<board.size();i++)
            for(int j = 0; j<board[0].size();j++)
                if(dfs(board, i, j, word))
                    return true;
        return false;

    }

    bool dfs(vector<vector<char>>& board, int i, int j, string word)
    {
        if(!word.size())
            return true;
        if(i<0||i>=board.size()||j<0||j>=board[0].size()||word[0]!=board[i][j])
            return false;
        char store = board[i][j];
        word = word.substr(1);
        board[i][j] = '*';
        bool ret = dfs(board, i+1, j, word)||dfs(board, i-1, j, word)||dfs(board, i, j+1, word)||dfs(board, i, j-1, word);
        board[i][j] = store;
        return ret;
    }
};

```

##  94-Binary Tree Inorder Traversal「二叉树」

[Binary Tree Inorder Traversal]( https://leetcode.com/problems/binary-tree-inorder-traversal/ )

中序遍历二叉树，两种方法，一种是常规方法，一种是**Morris Traversal**。第二种不借助额外的空间，属于利用树中原有的空指针。

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> res;
        inorder(root, res);
        return res;
    }

    void inorder(TreeNode *root, vector<int> & res)
    {
        if(root!= nullptr)
        {
            inorder(root->left, res);
            res.push_back(root->val);
            inorder(root->right, res);
        }
    }
};

```

第二种方法，算法可以描述为：

1. 如果当前节点的左孩子为空，则输出当前节点并将其右孩子作为当前节点。

2. 如果当前节点的左孩子不为空，在当前节点的左子树中找到当前节点在中序遍历下的前驱节点。
   1. 如果前驱节点的右孩子为空，将它的右孩子设置为当前节点。当前节点更新为当前节点的左孩子。
   2. 如果前驱节点的右孩子为当前节点，将它的右孩子重新设为空（恢复树的形状）。输出当前节点。当前节点更新为当前节点的右孩子。

3. 重复以上1、2直到当前节点为空。

```c++
class Solution1 {
public:
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> res;
        inorderMorrisTraversal(root, res);
        return res;
    }

    void inorderMorrisTraversal(TreeNode *root, vector<int> & res)
    {
        TreeNode * pre = nullptr;
        TreeNode* cur = root;
        while (cur != nullptr)
        {
            if(cur->left == nullptr)
            {//如果当前节点的左孩子为空，则输出当前节点并将其右孩子作为当前节点。 1
                res.push_back(cur->val);
                cur = cur->right;
            } else
            {//左孩子不为空
                // 寻找前驱结点
                pre = cur->left;
                while (pre->right!= nullptr && pre->right != cur)
                {
                    pre = pre->right;
                }
                //前驱结点右孩子为空，则用这个前驱结点的右指针指向当前结点  2.1
                if(pre->right == nullptr)
                {
                    pre->right = cur;
                    cur = cur->left;
                } else
                {//前驱结点右孩子指向当前结点，则恢复成空指针   2.2
                    pre->right = nullptr;
                    res.push_back(cur->val);
                    cur = cur->right;
                }
            }
        }
    }
};

```

也可以改为先序遍历，只需要改变输出位置即可。**只放在2.1处**。

[参考]( https://www.cnblogs.com/anniekim/archive/2013/06/15/morristraversal.html )

## 96-Unique Binary Search Trees「DP」

[Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)

给定一个序列，求BST树的种类数。

假定G[n]表示序列1...n的BST树的种类数，而F[i, n]表示以i为根结点的BST树的种类数。

显然有:
$$
F(i, n) = G(i-1) \times G(n-i) \quad 1\le i\le n\\
G(n) = F(1, n) + F(2, n) + \cdots + F(n, n)
$$
代码如下：

```c++
class Solution {
public:
    int numTrees(int n) {
        int G[n+1];
        fill(G, G+n+1, 0); //数据初始化
        G[0] = 1;
        G[1] = 1;
        for(int i=2; i<=n; i++)
            for(int j = 1; j<=i; j++)
                G[i] += G[j-1] * G[i-j];
        return G[n];
    }
};

```

## 98-Validate Binary Search Tree「中序遍历」

[Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)

判断是不是BST树，根据中序遍历序列递增可以判断。

```c++
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    explicit TreeNode(int x): val(x), left(nullptr), right(nullptr) {}
};


class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> s;
        TreeNode* pre= nullptr;
        while(root != nullptr || !s.empty())
        {
            while (root!= nullptr){
                s.push(root);
                root = root->left;
            }
            root = s.top();
            s.pop();

            if(pre!= nullptr && root->val <= pre->val)
                return false;
            pre = root;

            root = root->right;
        }
        return true;
    }
};

```

## 101-Symmetric Tree「二叉树」

[Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)

判断二叉树是否对称。

```c++
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    explicit TreeNode(int x): val(x), left(nullptr), right(nullptr) {}
};


class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(root== nullptr)
            return true;
        return valid(root->left, root->right);

    }

    bool valid(TreeNode* L, TreeNode* R)
    {
        if(L== nullptr||R== nullptr)
            return L==R;
        if(L->val != R->val)
            return false;
        return valid(L->right, R->left) && valid(L->left, R->right);
    }
};

```

还可以使用队列，进行层序遍历进行判断。

## 102-Binary Tree Level Order Traversal「队列」

[Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

层序遍历二叉树。**注意队列只放非空结点，这样节省空间和时间。**

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> Q;
        vector<vector<int>> res;
        if(root == nullptr)
            return res;
        Q.push(root);
        int num;
        while (!Q.empty())
        {
            vector<int> tmp;
            num = Q.size();
            while (num > 0)
            {
                root = Q.front();
                Q.pop();

                if(root->left != nullptr)
                    Q.push(root->left);
                if(root->right != nullptr)
                    Q.push(root->right);
                tmp.push_back(root->val);
                num--;
            }
            res.push_back(tmp);
        }
        return res;
    }
};

```



## 104-Maximum Depth of Binary Tree「DFS/BFS」

[Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

求二叉树的最大深度。

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root == nullptr)
            return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};

```

使用BFS就是用队列层序遍历。

## 105-Construct Binary Tree from Preorder and Inorder Traversal「二叉树遍历」

[Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

经典题目

```c++
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {

        return build(preorder, inorder, 0, preorder.size()-1, 0, inorder.size()-1);
    }

    TreeNode* build(vector<int>& preorder, vector<int>& inorder, int pres, int pree, int ins, int ine)
    {
        if(pres > pree)
            return nullptr;
        auto* root = new TreeNode(preorder[pres]);
        int pos; // 根在中序遍历中的位置
        for(int i=ins; i<=ine; i++)
        {
            if(inorder[i] == root->val)
            {
                pos = i;
                break;
            }
        }
        root->left = build(preorder, inorder, pres+1, pres + (pos - ins), ins, pos-1);
        root->right = build(preorder, inorder, pres + (pos - ins)+ 1, pree, pos+1, ine);
        return root;
    }
};
```

## 114-Flatten Binary Tree to Linked List「二叉树遍历」

[Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

将一个二叉树压成链表。

这里采用一种**右-左-根**的遍历方式。这样树的遍历结果是`[6->5->4->3->2->1]`，也就是链表从后往前生成。

只需要在处理“根部”的时候，将右孩子指向链表的当前结点，左孩子为空即可。

```c++
class Solution {
private:
    TreeNode* pre = nullptr; //链表的前驱
public:
    void flatten(TreeNode* root) {
        if(root!= nullptr)
        {
            flatten(root->right);
            flatten(root->left);
            root->right = pre;
            root->left = nullptr;
            pre = root;
        }
    }
};
```

相反，采用根左右这种相反的顺序不可行，因为处理完根之后，左右孩子指向变了，需要加辅助变量，复杂很多。

