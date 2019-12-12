---
title: "Topliked100_2"
date: 2019-12-09T14:23:47+08:00
lastmod: 2019-12-09T14:23:47+08:00
draft: false
tags: ['Leetcode']
categories: ['算法']
---

# Topliked100（三）

## 208-Implement Trie (Prefix Tree)「树」

[Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

> 实现一个前缀树。

```java
public class Trie {
    static class Node {
        char val;
        boolean isword;  //判断当前结点是不是一个单词
        Node[] children;

        Node(char val) {
            this.val = val;
            isword = false;
            children = new Node[26];
        }
    }
    private Node root;
    /**
     * Initialize your data structure here.
     */
    public Trie() {
        root = new Node(' ');
    }

    /**
     * Inserts a word into the trie.
     */
    public void insert(String word) {
        Node cur = root;
        for(int i = 0; i<word.length(); i++)
        {
            if(cur.children[word.charAt(i)-'a'] == null)
            {
                cur.children[word.charAt(i)-'a'] = new Node(word.charAt(i));
            }
            cur = cur.children[word.charAt(i)-'a'];
        }
        cur.isword = true;
    }

    /**
     * Returns if the word is in the trie.
     */
    public boolean search(String word) {
        Node cur = root;
        for(int i = 0;i<word.length();i++)
        {
            if(cur.children[word.charAt(i)-'a']!=null)
                cur = cur.children[word.charAt(i)-'a'];
            else
                return false;
        }
        return cur.isword;
    }

    /**
     * Returns if there is any word in the trie that starts with the given prefix.
     */
    public boolean startsWith(String prefix) {
        Node cur = root;
        for(int i = 0;i<prefix.length();i++)
        {
            if(cur.children[prefix.charAt(i)-'a']!=null)
                cur = cur.children[prefix.charAt(i)-'a'];
            else
                return false;
        }
        return true;
    }
}
```

## 215-Kth Largest Element in an Array「heap/partition」

[Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

> 找到一个数组中第k大的数

可以直接维护一个最小堆，保证容量为k，最后取出堆顶的元素即可。另一种方法基于快排的切分，最好情况下$O(n)$的时间复杂度，最坏情况下$O(n^2)$。只需要一个额外空间。可以使用shuffle来矫正一下。

```java
public int findKthLargest1(int[] nums, int k){
        k = nums.length - k; //找第k小的
        int low = 0;
        int high = nums.length -1;
        while (low < high)
        {
            int cur = partition(nums, low, high);
            if(cur > k)
                high = cur - 1;
            else if(cur < k)
            {
                low = cur + 1;
            }
            else
                break;
        }
        return nums[k];
    }

    private int partition(int[] nums, int start, int end)
    {
        int piv = nums[end];
        int i = start - 1;
        int tmp;
        for(int j = start; j<end; j++)
        {
            if(nums[j] < piv)
            {
                i++;
                tmp = nums[i];
                nums[i] = nums[j];
                nums[j] = tmp;
            }
        }
        tmp = nums[i+1];
        nums[i+1] = nums[end];
        nums[end] = tmp;
        return i+1;
    }
```

## 236-Lowest Common Ancestor of a Binary Tree「二叉树」

[Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

>给定一个二叉树，以及两个结点，找到它们的最低公共结点。

```java
public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null)
            return null;
        else if (root == p)
            return p;
        else if (root == q)
            return q;
        else {
            TreeNode left = lowestCommonAncestor(root.left, p, q);
            TreeNode right = lowestCommonAncestor(root.right, p, q);
            if (left != null && right != null)
                return root;
            else if (left != null)
                return left;
            else return right;
        }
    }
```

## 238-Product of Array Except Self「数组」

[Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

> 给定一个数组，要求返回一个数组，每个元素等于原数组除对应元素外其它所有元素的乘积。

先用一个数组记录累积，`product[i]`记录着从`nums[0]`到`nums[i-1]`的乘积。然后再从数组右侧开始遍历，用一个元素记录累积，这样就可以把当前元素`nums[i]`空出来了。

```java
public int[] productExceptSelf(int[] nums) {
        int[] product = new int[nums.length];
        product[0] = 1;

        for(int i = 1; i<nums.length; i++)
        {
            product[i] = product[i-1] * nums[i-1];
        }
        int right = 1;
        for(int i = nums.length-1; i>=0; i--)
        {
            product[i] = product[i] * right;
            right = right * nums[i];
        }
        return product;
    }
```

## 240-Search a 2D Matrix II「查找」

[Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)

> 行，列分别有序的矩阵查找指定元素。

从右上角作为起点，先向下走，再向左走。$O(M+N)$的时间复杂度。

```java
public boolean searchMatrix(int[][] matrix, int target) {
        if(matrix.length==0)
            return false;
        int row = 0, col = matrix[0].length -1;
        while (row<matrix.length&&col>=0)
        {
            if(matrix[row][col] == target)
                return true;
            else if(matrix[row][col] > target)
                col--;
            else
                row++;
        }
        return false;
    }
```

## 279-Perfect Squares「DP」

[Perfect Squares](https://leetcode.com/problems/perfect-squares/)

>给定一个数n，找到最少完美平方数（1，4， 9， 16，……）的数量，使得它们和为n，可以重复利用。

定义$DP[i]$表示和为$i$的最少完美平方数的数量，令$j$表示任意小于$i$的完美平方数（$j^2<=i$），则有
$$
DP[i] = min(DP[i], DP[i-j^2])
$$

```java
public int numSquares(int n) {
        int[] dp = new int[n+1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        if(n==0)
            return 0;
        for(int i = 1; i<=n; i++)
            for(int j = 1; j*j<=i; j++)
            {
                dp[i] = Math.min(dp[i], dp[i-j*j] + 1);
            }
        return dp[n];
    }
```

## 283-Move Zeroes

[Move Zeroes](https://leetcode.com/problems/move-zeroes/)

>讲一个数组所有非0元素移到前面，0放最后。

```java
public void moveZeroes(int[] nums) {
        int index = 0;
        int i;
        for (i = 0; i < nums.length; i++) {
            if (nums[i] != 0)
                nums[index++] = nums[i];
        }
        Arrays.fill(nums, index, nums.length, 0);
    }
```

## 287-Find the Duplicate Number

[Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

>给定一个数组，n+1个整数，每个整数在1~n之间，但是有一个元素至少重复一次。找出这个元素。

注意，题目说至少重复一次，如果没有这个条件，还可以多一个解法，即先`1~n`的和，然后用这$n+1$个数的和减去前一个就是结果。

对于本道题而已，首先可以用哈希表。还有一种方法，借鉴了寻找链表的环结点的思想：

```java
public int findDuplicate(int[] nums) {
        if(nums.length==0)
            return 0;
        int slow = nums[0], fast = nums[nums[0]];
        while (slow != fast)
        {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        fast = 0;
        while (slow != fast)
        {
            slow = nums[slow];
            fast = nums[fast];
        }
        return fast;
    }
```

## 300-Longest Increasing Subsequence[DP]

[Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

>给定一个数组，求最长递增序列的长度（不一定连续）。

方法一采用DP的思想，令`dp[i]`表示以`i`为结尾的子序列的最大长度，初始化`dp[i]=1`。于是对于所有的`0<=j<i`有`dp[i] = max(dp[j]+1, dp[i]) `。时间复杂度$O(n^2)$

```java
public int lengthOfLIS(int[] nums) {
        if(nums.length==0)
            return 0;
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        int res = 1;
        for(int i = 1; i<nums.length; i++)
        {
            for(int j = 0; j<i; j++)
            {
                if(nums[i] > nums[j])
                    dp[i] = Math.max(dp[i], dp[j]+1);
            }
            res = Math.max(dp[i], res);
        }
        return res;
    }
```

方法二用一个数组end（下文同tails），记录这个最长的序列，每次二分查找。时间复杂度$O(NlogN)$。

Each time we only do one of the two:

```
(1) if x is larger than all tails, append it, increase the size by 1
(2) if tails[i-1] < x <= tails[i], update tails[i] （保证这个序列元素尽可能小）
```

```java
public int lengthOfLIS1(int[] nums){
        int[] end = new int[nums.length];
        int size = 0;
        for(int v: nums)
        {
            int i = 0, j=size;
            while (i!=j)
            {
                int m = (i+j)/2;
                if(end[m]<v)
                    i=m+1;
                else
                    j=m;
            }
            end[i] = v;
            if(i==size) size++;
        }
        return size;
    }
```

## 309-Best Time to Buy and Sell Stock with Cooldown「DP」

[Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

>同之前的股票买卖问题，额外增加一个限制为，每次卖完之后，必须休息一天。

采样动态规划的思想，如下图表示状态转移，一共三种状态s0、s1和s2。于是我们使用三个数组`s0[i]`、`s1[i]`和`s2[i]`分别表示第i天三个状态的最大利润。

![leetcode309.png](https://prime-blog.oss-cn-hangzhou.aliyuncs.com/leetcode/leetcode309.png)

```java
public int maxProfit(int[] prices) {
        if(prices.length <=1 )
            return 0;
        int res = Integer.MIN_VALUE;
        int[] s0 = new int[prices.length];
        int[] s1 = new int[prices.length];
        int[] s2 = new int[prices.length];
        s0[0] = 0;
        s1[0] = -prices[0];
        s2[0] = Integer.MIN_VALUE;
        for(int i = 1; i<prices.length; i++)
        {
            s0[i] = Math.max(s2[i-1], s0[i-1]);
            s1[i] = Math.max(s0[i-1]-prices[i], s1[i-1]);
            s2[i] = s1[i-1] + prices[i];
            res = Math.max(res, s0[i]);
            res = Math.max(res, s2[i]);
        }
        return res;
    }
```

显然，三个数组因为只用到一次，因此可以简化为三个变量保存即可。

## 322-Coin Change「DP」

[Coin Change](https://leetcode.com/problems/coin-change/)

>给定一组硬币，寻找最少的数量使得它们和为给定值。每种硬币无限制数目。

用`dp[i]`表示目标值为`i`的最少硬币数。

```java
public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount+1];
        Arrays.fill(dp, amount+1);
        dp[0] = 0;
        for(int i = 1; i<=amount; i++)
            for(int j = 0; j<coins.length; j++)
            {
                if(i>=coins[j])
                    dp[i] = Math.min(dp[i], dp[i-coins[j]]+1);
            }
        return dp[amount]==amount+1?-1:dp[amount];
    }
```

注意这里不能直接设置为int的最大值，会出现溢出问题。

##  337-House Robber III

[House Robber III](https://leetcode.com/problems/house-robber-iii/)

>相邻结点不能选，求最大值。

用`rob(root)`表示以`root`为根的子树的最大值，此外因为递归过程会有重复结点计算的问题，因此用hashmap保存中间结果。最优子结构和重叠子问题——标准的dp。

```java
public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }
    private HashMap<TreeNode, Integer> map = new HashMap<>();

    public int rob(TreeNode root) {
        if(root==null)
            return 0;
        else if(map.containsKey(root))
            return map.get(root);
        else
        {
            int val = 0;
            if(root.left!=null)
                val += rob(root.left.left) + rob(root.left.right);
            if(root.right!=null)
                val += rob(root.right.left) + rob(root.right.right);
            val = Math.max(val + root.val, rob(root.left) + rob(root.right));
            map.put(root, val);
            return val;
        }
    }
```

另一种解法。因为之前求解过程中在递归传递过程中没有保留是否“采用”当前结点的信息，故而可以修改一下。这样也可避免重复计算的问题。

```java
public int rob2(TreeNode root)
    {
        int[] res = subrob(root);
        return Math.max(res[0], res[1]);
    }
    private int[] subrob(TreeNode root)
    {
        if(root==null)
            return new int[2];
        int[] left = subrob(root.left);
        int[] right = subrob(root.right);

        int[] res = new int[2];
        res[0] = root.val + left[1] + right[1];  //选了root
        res[1] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]); //没选root
        return res;
    }
```

## 338-Counting Bits「位运算+dp」

[Counting Bits](https://leetcode.com/problems/counting-bits/)

>给定一个数num，对任意0<=i<=num的i，计算i中1的个数，返回结果数组。

标准位运算。

```java
public int[] countBits(int num) {
        int[] res = new int[num+1];
        for(int i = 1; i<=num; i++)
            res[i] = res[i>>1] + (i & 1); //这里使用i&1取最后一位
        return res;
    }
```

## 347-Top K Frequent Elements「哈希表/桶排序」

[Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

>找到一个数组中，topk频率最高的元素。

方法一：先用哈希表存储`num->frequency`的pair，然后用桶排序找topk。

方法二：先用哈希表存储`num->frequency`， 然后再用`treemap`存储`frequency->num_list`（或者其它语言类似结构，主要用于排序）。

方法三：先用哈希表存储`num->frequency`，然后放入优先队列。

这里方法一是最优的，实现如下：

```java
public List<Integer> topKFrequent(int[] nums, int k) {
        List[] bucket = new List[nums.length+1];
        Map<Integer, Integer> map = new HashMap<>();  // num -> frequency
        for(int v: nums)
            map.put(v, map.getOrDefault(v, 0)+1);

        for (int key: map.keySet())
        {
            if(bucket[map.get(key)] == null)
            {
                bucket[map.get(key)] = new ArrayList<>();
            }
            bucket[map.get(key)].add(key);
        }
        List<Integer> res = new ArrayList<>();
        for(int i = bucket.length-1; i>=0&&res.size()<k; i--)
        {
            if(bucket[i]!=null)
            {
              // 这里最终可能超过k个，但题目没卡，如果卡，可以最后返回res[:k]
                res.addAll(bucket[i]); 
              
            }
        }
        return res;
    }
```

## 394-Decode String「栈\字符串」

[Decode String](https://leetcode.com/problems/decode-string/)

>解码字符串。s = "3[a2[c]]", return "accaccacc".

**看到这种嵌套结构，不用想，要么用栈要么递归。**

遍历字符串，针对各种符号相应处理即可。

```java
public String decodeString(String s) {
        String res = ""; // 记录当前串，最终就是结果串
        Stack<Integer> numStack = new Stack<>();
        Stack<String> strStack = new Stack<>();
        int cur = 0;
        while (cur<s.length())
        {
            if(s.charAt(cur)>='0'&&s.charAt(cur)<='9')
            {
                int sum = 0;
                while (s.charAt(cur)>='0'&&s.charAt(cur)<='9')
                {
                    sum = sum*10 + s.charAt(cur) - '0';
                    cur++;
                }
                numStack.push(sum);
            }
            else if(s.charAt(cur) == '[') {
                strStack.push(res);
                res = "";
                cur++;
            }
            else if (s.charAt(cur)==']')
            {//对当前的字串处理
                int num = numStack.pop();
                StringBuilder tmp = new StringBuilder(strStack.pop()); // 获取外部的串
                for(int i = 0;i<num; i++)
                {
                    tmp.append(res);
                }
                res = tmp.toString(); //更新当前结果
                cur++;
            }
            else
            {
                res += s.charAt(cur++);
            }
        }
        return res;
    }
```

## 406-Queue Reconstruction by Height「找规律」

[Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/)

>队列排序。

1. 先找出个子最高的，按照第二个参数k排个序。
2. 找到次高的，按照k插入其中
3. 重复以上操作

```java
public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, (int[] o1, int[] o2)->{return o1[0]!=o2[0]?o2[0]-o1[0]:o1[1]-o2[1];});//从大到小排序
        List<int[]> res = new LinkedList<>();

        for (int[] p: people)
            res.add(p[1], p);

        return res.toArray(new int[people.length][2]);
    }
```



## 416-Partition Equal Subset Sum「DP」

[Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

>本质上是，给定一个数组，判断是否能找到一个子集，和为数组所有元素和的一半。

令`dp[i][j]`表示从数组`nums[0……i]`中是否能找到子集和为`j`。

```java
public boolean canPartition(int[] nums) {
        int target = 0;
        for(int n: nums)
            target+=n;
        if((target & 1) ==1||target == 0)
            return false;
        else
            target >>= 1;
        boolean[][] dp = new boolean[nums.length][target+1]; 
  // dp[i][j]表示下标为0……i的元素中，选择是否和等于j。
        if(nums[0] < target)  // first row
            dp[0][nums[0]] = true;

        for(int i = 0; i<dp.length; i++)
            dp[i][0] = true;

        for(int i = 1; i<nums.length; i++)
            for(int j = 1; j<=target; j++) // i和j的顺序这里替换也可以，但是一般还是以二维数组顺序
            {
                if(nums[i] > j)
                    dp[i][j] = dp[i-1][j];
                else
                    dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i]];
            }
        return dp[nums.length-1][target];
    }
```

