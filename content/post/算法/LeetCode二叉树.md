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



