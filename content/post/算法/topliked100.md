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

