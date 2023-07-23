---
layout:     post
title:      刷题Binaru Tree and Complexity
subtitle:   
date:       2022-06-24
author:     KX
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - Binary Tree
    - DP
    - Complexity
---
# Binary Tree and Complexity
前几天面试TikTok的时候遇到了Binary Tree的题, 觉得应该能写出来,硬是没写出来. 而且面试还和笔试不一样, 狠狠尴尬了好几天

我在recursive function, backtracking 这方面都不太擅长, 复杂度分析也每次都是糊弄糊弄, 最近一起钻研了一下

## Template 题目 1
[236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

####Recursive 解法
referenced from 代码随想录
C++
```C++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == NULL) return root;
        if (root == p or root == q) return root;
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if (left != NULL and right != NULL) return root;
        if (left != NULL and right == NULL) return left;
        if (left == NULL and right != NULL) return right;
        return NULL;
    }
};
```

Python
```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root: return None
        if root == p or root == q: return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right: return root
        if left and not right: return left
        if not left and right: return right
        return None
```
Complexity:
Time complexity: worst case visite all nodes, O(N)
Space complexity: depth of the stack -> height of the tree, worst case O(N)

这个解法虽然已经比官方答案简单了, 从方法的角度讲很酷炫, 但还是写不出来呜呜, 而且recursive function debug很难, 更推荐下面的解法

#### Parent pointer (Hashmap)
```python
class Solution:

    def lowestCommonAncestor(self, root, p, q):
        """
        :type root: TreeNode
        :type p: TreeNode
        :type q: TreeNode
        :rtype: TreeNode
        """

        # Stack for tree traversal
        stack = [root]

        # Dictionary for parent pointers
        parent = {root: None}

        # Iterate until we find both the nodes p and q
        while p not in parent or q not in parent:

            node = stack.pop()

            # While traversing the tree, keep saving the parent pointers.
            if node.left:
                parent[node.left] = node
                stack.append(node.left)
            if node.right:
                parent[node.right] = node
                stack.append(node.right)

        # Ancestors set() for node p.
        ancestors = set()

        # Process all ancestors for node p using parent pointers.
        while p:
            ancestors.add(p)
            p = parent[p]

        # The first ancestor of q which appears in
        # p's ancestor set() is their lowest common ancestor.
        while q not in ancestors:
            q = parent[q]
        return q
```
Complexity Analysis

Time Complexity : O(N), where N is the number of nodes in the binary tree. In the worst case we might be visiting all the nodes of the binary tree.

Space Complexity : O(N). In the worst case space utilized by the stack, the parent pointer dictionary and the ancestor set, would be NNN each, since the height of a skewed binary tree could be NNN.

这个解法的有点是容易扩展, 例如计算二叉树中两点的路径, 
(2096. Step-By-Step Directions From a Binary Tree Node to Another)[https://leetcode.com/problems/step-by-step-directions-from-a-binary-tree-node-to-another/description/]
My Answer
```python
class Solution:
    def getDirections(self, root: Optional[TreeNode], startValue: int, destValue: int) -> str:
        parent = {}
        stack = [root]
        p = root
        q = root
        while stack:
            node = stack.pop()
            if node.val == startValue: p = node
            if node.val == destValue: q = node
            if node.left: 
                parent[node.left] = node
                stack.append(node.left)
            if node.right:
                parent[node.right] = node
                stack.append(node.right)
        ancester = set()
        ancester.add(p)
        start = p
        while p and p in parent:
            p = parent[p]
            ancester.add(p)
        pathQ = ""
        while q not in ancester:
            if q == parent[q].left: pathQ = "L" + pathQ
            else: pathQ = "R" + pathQ
            q = parent[q]
        commonRoot = q

        pathP = ""
        while start != commonRoot:
            start = parent[start]
            pathP += "U"


        return pathP + pathQ
```
看到submission区大部分提到DFS还没仔细看, 不管怎么说能想出一种解法已经很棒了!


还有一些情况下输入不是标准的树, 如
[1257. Smallest Common Region](https://leetcode.com/problems/smallest-common-region/)
You are given some lists of regions where the first region of each list includes all other regions in that list.

Naturally, if a region x contains another region y then x is bigger than y. Also, by definition, a region x contains itself.

Given two regions: region1 and region2, return the smallest region that contains both of them.

If you are given regions r1, r2, and r3 such that r1 includes r3, it is guaranteed there is no r2 such that r2 includes r3.

It is guaranteed the smallest region exists.

 

Example 1:

Input:
regions = [["Earth","North America","South America"],
["North America","United States","Canada"],
["United States","New York","Boston"],
["Canada","Ontario","Quebec"],
["South America","Brazil"]],
region1 = "Quebec",
region2 = "New York"
Output: "North America"
Example 2:

Input: regions = [["Earth", "North America", "South America"],["North America", "United States", "Canada"],["United States", "New York", "Boston"],["Canada", "Ontario", "Quebec"],["South America", "Brazil"]], region1 = "Canada", region2 = "South America"
Output: "Earth"

My answer
```python
class Solution:
    def findSmallestRegion(self, regions: List[List[str]], region1: str, region2: str) -> str:
        parentRegion = {}
        for region in regions:
            for x in range(1, len(region)):
                parentRegion[region[x]] = region[0]
        region1ParentReg = {region1}

        while region1 and region1 in parentRegion:
            region1 = parentRegion[region1]
            region1ParentReg.add(region1)
        
        while region2 and region2 not in region1ParentReg:
            region2 = parentRegion[region2]
        
        return region2
```

## Template 题目 2
解法一: recursive function
```python
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if not root:
            return True
        return self.compare(root.left, root.right)
        
    def compare(self, left, right):
        #首先排除空节点的情况
        if left == None and right != None: return False
        elif left != None and right == None: return False
        elif left == None and right == None: return True
        #排除了空节点，再排除数值不相同的情况
        elif left.val != right.val: return False
        
        #此时就是：左右节点都不为空，且数值相同的情况
        #此时才做递归，做下一层的判断
        outside = self.compare(left.left, right.right) #左子树：左、 右子树：右
        inside = self.compare(left.right, right.left) #左子树：右、 右子树：左
        isSame = outside and inside #左子树：中、 右子树：中 （逻辑处理）
        return isSame
```
我写了个简单写法, 但是怕之后忘了自己都看不懂, 所以先贴了逻辑一盘就清楚的写法
```python
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        def isSym(left, right):
            if left and right:
                left = 
                return (left.val == right.val) and isSym(left.left, right.right) and isSym(left.right, right.left)
            if not left and not right:
                return True
            return False
        if not root: return True
        return isSym(root.left, root.right)
```
解法二: 按照需要比较的顺序把他们压入queue或stack中一对一对取出比较
```python
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        if not root: return True
        stack = [root.left, root.right]
        while stack:
            nodeRight = stack.pop()
            nodeLeft = stack.pop()
            if not nodeRight and nodeLeft: return False
            if nodeRight and not nodeLeft: return False
            if not nodeRight and not nodeLeft: continue
            if nodeRight.val != nodeLeft.val: return False
            stack.append(nodeRight.right)
            stack.append(nodeLeft.left)
            stack.append(nodeRight.left)
            stack.append(nodeLeft.right)
        
        return True
```
## 894. All Possible Full Binary Trees
# To do
complexity











