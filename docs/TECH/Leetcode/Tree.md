# 二叉树面试冲刺指南：Tree、BST 与 AVL 核心套路

复习二叉树是算法面试中最核心、性价比最高的环节之一。因为树的题目往往结构性极强，一旦掌握了核心套路，就能举一反三。

二叉树的题目本质上都在考察**遍历（Traversal）**和**递归（Recursion / Divide and Conquer）**。下面为你梳理了常见的题型套路、做题方法，并配套了经典的 LeetCode 题目。

---

## 一、 基础遍历与视图 (Basic Traversals & Views)

这是所有树型题目的基石。你需要熟练掌握**递归（Recursion）**和**迭代（Iteration，借助 Stack 或 Queue）**两种写法。

### 💡 套路与做题方法
* **深度优先搜索 (DFS)：** 包含前序（根左右）、中序（左根右）、后序（左右根）。
    * *前序*：适合自顶向下传递信息。
    * *后序*：适合自底向上的分治法（先收集左右子树信息，再在当前节点处理）。
* **广度优先搜索 (BFS)：** 层序遍历。
    * *套路*：使用队列（Queue），在每层开始时记录 `size = queue.length`，通过一个 `for` 循环把当前层一次性处理完，非常适合求最短路径、层级属性。

### 🎯 配套题目
* [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/) *(前序)*
* [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) *(中序)*
* [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) *(后序)*
* [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) *(层序 BFS 模板题)*
* [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/) *(BFS 变形：每层的最后一个节点)*

---

## 二、 树的属性与分治法 (Tree Properties & Divide and Conquer)

这类题目通常要求你计算树的深度、判断对称性、寻找最近公共祖先等。

### 💡 套路与做题方法
* **灵魂三问：** 写递归前问自己三个问题：
    1. 当前节点需要做什么？
    2. 我需要向左右子树索要什么信息？（通常是后序遍历）
    3. 我需要向我的父节点返回什么信息？
* **全局变量法：** 如果递归过程中需要记录最大值（如最大路径和、树的直径），可以在类中定义一个全局变量，在遍历每个节点时不断更新它。

### 🎯 配套题目
* [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) *(最基础的分治：`max(left, right) + 1`)*
* [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/) *(同时遍历两棵树，判断 `t1.left` 和 `t2.right`)*
* [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) *(经典分治：左右子树若各找到一个目标，当前节点就是 LCA)*
* [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/) *(后序遍历 + 全局变量更新)*
* [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) *(Hard，树形DP的入门，套路同上)*

---

## 三、 二叉搜索树特性 (Binary Search Tree - BST)

BST 的核心特性是：**左子树所有节点 < 根节点 < 右子树所有节点**。

### 💡 套路与做题方法
* **黄金法则：** **BST 的中序遍历（Inorder）是一个严格递增的有序数组。** 凡是涉及到“判断合法性”、“第 K 大/小的元素”，直接套用中序遍历。
* **二分搜索思想：** 利用 BST 特性，搜索时不需要遍历整棵树。如果 `val < root.val`，只去左子树找；反之去右子树。这种时间复杂度在树平衡时是 $O(\log N)$。

### 🎯 配套题目
* [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) *(易错题：注意不仅要大于左孩子，还要大于左子树的所有节点，需传入上下界区间)*
* [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) *(中序遍历，数到第 K 个停下)*
* [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) *(利用值的大小关系，如果 p 和 q 都小于 root，向左走，否则向右走)*
* [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/) *(寻找到空节点插入即可)*
* [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/) *(稍难：删除节点后需要找右子树的最小值来顶替)*

---

## 四、 树的构建与序列化 (Tree Construction & Serialization)

这类题目要求你从数组或其他数据结构还原出一棵二叉树。

### 💡 套路与做题方法
* **找根节点 + 划分左右子树：** 无论是前序还是后序数组，都能迅速定位“根节点”（前序的第一个，后序的最后一个）。
* 拿着根节点的值去中序数组中找到它的位置，位置左边就是左子树的节点数量，右边就是右子树的节点数量。据此划分子数组，递归构建。

### 🎯 配套题目
* [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) *(经典构造题)*
* [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
* [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) *(Hard，通常用前序遍历或层序遍历将树转为字符串，空节点记录为 "null")*

---

## 五、 平衡二叉树与 AVL (Balanced Tree & AVL)

在面试中，从零手写完整的 AVL 树（包括左旋、右旋操作）非常罕见，因为代码量太大。面试更看重你对“平衡”概念的理解以及如何把不平衡的树变平衡。

### 💡 套路与做题方法
* **平衡因子的概念：** 对于任意节点，其左右子树的高度差绝对值不超过 1。
* **数组转平衡树：** 找数组的中点作为根节点，左半部分递归构建左子树，右半部分递归构建右子树，这样天然就是平衡的。

### 🎯 配套题目
* [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/) *(自底向上计算高度，一旦发现某节点左右子树高度差大于 1，直接返回 -1 代表不平衡)*
* [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/) *(找中点，递归构建平衡树)*
* [1382. Balance a Binary Search Tree](https://leetcode.com/problems/balance-a-binary-search-tree/) *(核心套路组合：先对原 BST 做中序遍历拿到有序数组，再按 108 题的方法用有序数组构建平衡的 BST)*

---

## 📅 复习修仙建议

> 🚀 **推荐路线**：先刷透**基础的遍历** $\rightarrow$ 接着重点攻克**分治法（递归的灵魂三问）** $\rightarrow$ 最后再集中处理 **BST 的特性题**。
