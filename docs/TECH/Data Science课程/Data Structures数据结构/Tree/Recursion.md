# 二叉树递归分治三要素

在算法领域，二叉树的递归算法通常被称为**“树形分治”**。无论是基础的树遍历，还是 Hard 级别的复杂路径问题，其底层逻辑都遵循相同的控制流与数据流。本文将深度拆解二叉树递归的最高心法——**三要素框架**，并归纳三大经典解题流派。

---

## 核心心法：树形分治三要素

面对任何二叉树的递归、DFS、分治或树形 DP 题目，在动笔写代码前，应首先在脑海中（或草稿纸上）明确以下三个要素：

### 1. 明确函数“输入”与“输出”的语义
这是写递归最关键的基础。你必须给这个函数赋予一个极其明确、严格的**“行为承诺”**：
* **输入（Input）**：通常是当前处理的子树根节点 `node`。有时会附带自顶向下传递的限制边界（如二叉搜索树验证中的 `low, high`）。
* **输出（Output / 返回值）**：**它代表的数学或拓扑含义是什么？**
    * *计算深度*：输出必须是以当前节点为根的子树的最大高度（`int`）。
    * *寻找祖先*：输出是该子树中是否包含目标节点。若包含则返回目标或 LCA，否则返回 `None`（`TreeNode`）。
    * *合法验证*：输出是以当前节点为根的子树是否合法（`bool`）。

!!! warning "铁律"
    整个函数内部的所有 `return` 语句，吐出来的数据类型和语义**必须 100% 严格一致**。绝不能在 A 出口返回数量计数，而在 B 出口返回节点对象。

### 2. 确定 Base Case（安全出口）
递归是深度优先的，必须有触底反弹的边界，否则会导致栈溢出（Stack Overflow）。
* **寻找边界**：通常是树的尽头（`if not node:`），或者是提前撞到目标（`if node == p or node == q:`）。
* **赋予初始值**：Base Case 的返回值必须**严格符合要素一中对“输出”的定义**。
    * 如果输出定义为*高度*，Base Case 就要返回 `0`。
    * 如果输出定义为*有效节点*，Base Case 就要返回 `None` 或 `root`。
    * 如果输出定义为*是否合法*，Base Case 就要返回 `True`。

### 3. 编写当前层的合并逻辑（利用子问题返回值）
在此步骤中，应采用**“高管思维”**，不要试图用肉脑去模拟递归一进一出的执行顺序，这会导致大脑认知过载。
* **完全信任子问题**：直接写下 `left_return = func(node.left)` 和 `right_return = func(node.right)`。此时，默认左、右两个子递归（下属）已经完美地把报表算好并递到了你的办公桌上。
* **分类决策（总）**：你坐在当前 `node` 的办公室里，**只利用 `left_return`、`right_return` 以及当前 `node.val` 这三个已知条件**，通过 `if-else` 组合出一套决策，然后把当前层的最终结果 `return` 提交给上一层。

---

## 二叉树解题三大经典流派

根据“三要素”的组合方式，LeetCode 上的二叉树题目可归纳为以下三大经典流派：

### 流派一：纯粹的分治合并流（Bottom-Up）
* **核心特征**：当前层的结果完全依赖于下层返回值的拓扑组合。数据流完全由下至上。
* **代表题目**：[LeetCode 236. 二叉树的最近公共祖先]
* **代码骨架**：

```python
def lowestCommonAncestor(self, root, p, q):
    # 1. Base Case: 安全出口
    if root == None or root == p or root == q:
        return root
    
    # 2. 索要子问题结果（完全信任）
    left_return = self.lowestCommonAncestor(root.left, p, q)
    right_return = self.lowestCommonAncestor(root.right, p, q)

    # 3. 当前层合并决策逻辑
    if left_return == None:
        return right_return
    elif right_return == None:
        return left_return
    elif left_return and right_return:
        return root

```

### 流派二：分治 + 全局旁路流（Bottom-Up with Global State）

* **核心特征**：函数的返回值严格用于给上层“铺路”（传递连续路径或深度），但在后序遍历的位置，会**顺手**利用子问题的返回值去更新一个全局的计数器或最大值（旁路收集）。
* **代表题目**：[LeetCode 543. 二叉树的直径]、[LeetCode 124. 二叉树中的最大路径和]
* **代码骨架**：

```python
class Solution(object):
    def diameterOfBinaryTree(self, root):
        self.max_diameter = 0 # 全局状态池
        
        def depth(node):
            if not node: return 0 # Base Case
            
            # 索要子问题结果
            left = depth(node.left)
            right = depth(node.right)
            
            # 旁路触发：利用返回值更新全局最大值
            self.max_diameter = max(self.max_diameter, left + right)
            
            # 履行契约：返回当前子树的最大深度给上层
            return max(left, right) + 1
        
        depth(root)
        return self.max_diameter

```

### 流派三：自顶向下边界流（Top-Down Context Passing）

* **核心特征**：上层的信息（如取值范围、祖先状态、路径和）需要作为参数，像接力棒一样一层层传给子节点，用于在当前层做校验。
* **代表题目**：[LeetCode 98. 验证二叉搜索树]
* **代码骨架**：

```python
def isValidBST(self, root):
    
    # 输入不仅有 node，还有上层传下来的隐形框框 (low, high)
    def validate(node, low, high):
        if not node: 
            return True # Base Case
            
        # 校验当前层是否越界
        if node.val <= low or node.val >= high: 
            return False
            
        # 向下推进时，动态收紧左右子树的框框（环境上下文传递）
        return validate(node.left, low, node.val) and \
               validate(node.right, node.val, high)
    
    return validate(root, float('-inf'), float('inf'))

```

---

## 总结：架构师视角

理清“三要素”后，写递归就像在做架构设计：

1. **定义接口 (`API`)**：明确输入什么，输出什么，严格遵守类型契约。
2. **设置保底机制 (`Fallback`)**：通过 Base Case 锁死边界。
3. **编写核心业务 (`Merge`)**：只在当前层做聚合。

通过将复杂的控制流解耦为单层的逻辑合并，你将不再是迷失在指针和调用栈里的执行者，而是掌控全局的代码架构师。

```

```
