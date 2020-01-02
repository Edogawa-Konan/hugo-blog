---
title: "Leetcode(78-144)"
date: 2019-12-31T11:11:30+08:00
lastmod: 2019-12-31T11:11:30+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# leetcode(78-144)

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

