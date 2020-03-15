---
title: "LeetCode二叉树"
date: 2020-02-25T19:43:58+08:00
lastmod: 2020-02-25T19:43:58+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# LeetCode二叉树

## 103. Binary Tree Zigzag Level Order Traversal「BFS」

[Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

>之字形遍历二叉树。

仍然采用普通的队列，只是对于奇偶层，在保存改层结果时改变顺序。

```java
public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        if (root == null) return res;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()){
            List<Integer> level = new LinkedList<>();
            int cnt = queue.size();
            for (int i=1; i<=cnt; i++){
                TreeNode tmp = queue.poll();
                if (res.size() % 2 == 0){
                    level.add( tmp.val);
                }else
                    level.add(0, tmp.val);
                if(tmp.left!=null)
                    queue.offer(tmp.left);
                if(tmp.right!=null)
                    queue.offer(tmp.right);
            }
            res.add(level);
        }
        return res;
    }
```

## 106. Construct Binary Tree from Inorder and Postorder Traversal「重建二叉树」

[Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

>根据中序和后序build二叉树

```java
public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    private HashMap<Integer, Integer> map = new HashMap<>();//record inorder val to index

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for (int index = 0; index<inorder.length; index++)
            map.put(inorder[index], index);
        return build(inorder, 0, inorder.length - 1, postorder, 0, postorder.length - 1);
    }

    public TreeNode build(int[] inorder, int in_start, int in_end, int[] postoder, int post_start, int post_end) {
        if (in_start > in_end || post_start > post_end)
            return null;
        TreeNode root = new TreeNode(postoder[post_end]);
        int i = map.get(root.val);
        root.left = build(inorder, in_start, i - 1, postoder, post_start, post_start + i - in_start - 1);
        root.right = build(inorder, i + 1, in_end, postoder, post_start+i-in_start, post_end - 1);
        return root;
    }
```

## 108. Convert Sorted Array to Binary Search Tree

[Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

>给定一个有序数组，将其重建成BST树。答案不唯一，输出一个即可

**采用二分查找的思想，每次用子数组的中间结点作为根**。这样自带平衡属性，又因为数组有序，所以也满足BST树的要求。

```java
static public class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
  }
    public TreeNode sortedArrayToBST(int[] nums) {
        return DFS(nums, 0, nums.length-1);
    }

    public TreeNode DFS(int[] nums, int start, int end)
    {
        if (start > end)
            return null;
        int mid = start + (end - start) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = DFS(nums, start, mid-1);
        root.right = DFS(nums, mid+1, end);
        return root;
    }
```

## 109.  Convert Sorted List to Binary Search Tree[重建BST树]

[ Convert Sorted List to Binary Search Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)

>给定一个有序链表，将其重建成BST树。答案不唯一，输出一个即可

和上一题同样的思路，**关键在于找到链表的中点，这里采用了两个指针的策略，前闭后开**。

```java
    static public class ListNode {
        int val;
        ListNode next;

        ListNode(int x) {
            val = x;
        }
    }
    static public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null)
            return null;
        return DFS(head, null);
    }
    public TreeNode DFS(ListNode start, ListNode end) {
        if (start == end)
            return null;
        ListNode fast = start;
        ListNode slow = start;
        while (fast != end && fast.next != end) {
            fast = fast.next.next;
            slow = slow.next;
        }
        TreeNode root = new TreeNode(slow.val);
        root.left = DFS(start, slow);
        root.right = DFS(slow.next, end);
        return root;
    }
```

## 110. Balanced Binary Tree「DFS」

[Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

>Given a binary tree, determine if it is height-balanced.
>
>For this problem, a height-balanced binary tree is defined as:
>
>a binary tree in which the left and right subtrees of *every* node differ in height by no more than 1.

判断平衡二叉树的问题，显然最简单的方法就是复用求高度的方法，但是如果直接用这样的话，时间复杂度是$O(n^2)$。显然这是不够好的。

一直可能的优化是将判断逻辑放入求高度的过程中。

```java
 static public class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
    }

    private boolean isbalance = true;

    public boolean isBalanced(TreeNode root) {
        if (root==null)
            return true;
        if (Math.abs(getTreeHeight(root.left) - getTreeHeight(root.right)) > 1)
            return false;
        else
            return isBalanced(root.left) && isBalanced(root.right);
    }

    public int getTreeHeight(TreeNode root)
    {//未优化的版本
        if (root==null)
            return 0;
        return Math.max(getTreeHeight(root.left), getTreeHeight(root.right)) + 1;
    }

    public boolean isBalanced2(TreeNode root)
    {
        getTreeHeight2(root);
        return isbalance;
    }

    public int getTreeHeight2(TreeNode root)
    {// 利用类变量isbalance标示
        if (root==null)
            return 0;
        int left = getTreeHeight2(root.left);
        int right = getTreeHeight2(root.right);
        if (Math.abs(left - right) > 1)
            isbalance = false;
        return Math.max(left, right) + 1;
    }
```

## 111. Minimum Depth of Binary Tree「DFS」

[Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

>Given a binary tree, find its minimum depth.
>
>The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.
>
>**Note:** A leaf is a node with no children.

```java
public int minDepth(TreeNode root) {
        if (root==null)
            return 0;
        int left = minDepth(root.left);
        int right = minDepth(root.right);
        if (left == 0 || right == 0)  // 有一个子树为空的时候，不能用min的方式，否则可能这个结点不是叶子但是返回0了
            return left + right + 1;
        else
            return Math.min(left, right) + 1;

    }
```

## 112. Path Sum「DFS」

[Path Sum](https://leetcode.com/problems/path-sum/)

>Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.**Note:** A leaf is a node with no children.

```java
public boolean hasPathSum(TreeNode root, int sum) {
        if (root==null)
            return false;
        else
        {
            if (root.val == sum && root.left == null && root.right == null)
                return true;
            return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
        }

    }
```

## 113. Path Sum II「DFS」

[Path Sum II](https://leetcode.com/problems/path-sum-ii/)

>Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.**Note:** A leaf is a node with no children.

```java
static public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int x) {
            val = x;
        }
    }
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new LinkedList<>();
        DFS(root, sum, new ArrayList<>(), res);
        return res;
    }
    public void DFS(TreeNode root, int sum, List<Integer> path, List<List<Integer>> result)
    {
        if (root == null)
            return;
        path.add(root.val);
        if (root.val == sum && root.left == null && root.right == null)
            result.add(new ArrayList<>(path));
        DFS(root.left, sum - root.val, path, result);
        DFS(root.right, sum - root.val, path, result);
        path.remove(path.size() - 1);
    }
```

## 116. Populating Next Right Pointers in Each Node「层序遍历」

[Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

>给定一个完全二叉树，要求填写其中的`next`指针。比如下图：
>
>![](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

显然，这是一个层序遍历问题，但是不一定非要用队列。。因为完全二叉树，一直向左孩子前进即可进入下一层，同时对该层层序遍历填充`next`域。

```java
static class Node {
        public int val;
        public Node left;
        public Node right;
        public Node next;

        public Node() {}

        public Node(int _val) {
            val = _val;
        }

        public Node(int _val, Node _left, Node _right, Node _next) {
            val = _val;
            left = _left;
            right = _right;
            next = _next;
        }
    };
    public Node connect(Node root) {
        if (root ==  null)
            return root;
        Node pre = root;// 一直向树的最左孩子遍历
        Node cur = null;
        while (pre.left != null)
        {//进入下一层的循环
            cur = pre;
            while (cur!=null)
            {// 通过next进行该层平移
                cur.left.next = cur.right;
                if (cur.next!=null)
                {
                    cur.right.next = cur.next.left;
                }
                cur = cur.next;
            }
            pre = pre.left;
        }
        return root;
    }
```

## 117. Populating Next Right Pointers in Each Node II「层序遍历」

[Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

>同上一题，只不过不保证是个完全二叉树。

显然，需要检查子树的存在性。之前的每次由左子树进入下一层的方法不再适用。考虑使用层序遍历，但是不算使用队列，因为目的是链接，因此更像是链表的操作。

```java
public Node connect(Node root) {
        Node res = root;
        Node dummy = new Node(0);
        while (root != null)
        {//树的层序遍历
            Node cur = dummy; // 用一个临时结点，作为每一层"链表"的头结点。
            while (root != null)
            {//遍历该层
                if (root.left != null)
                {
                    cur.next = root.left;
                    cur = cur.next;
                }
                if (root.right != null)
                {
                    cur.next = root.right;
                    cur = cur.next;
                }
                root = root.next;
            }
            root = dummy.next;
            dummy.next = null;
        }
        return res;
    }
```





