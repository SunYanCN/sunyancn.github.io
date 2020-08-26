---
title: leetcode226—翻转二叉树
tags: 算法
abbrlink: 26095
date: 2019-08-12 09:31:52
---
![](https://i.loli.net/2019/08/12/nb9EH4gKTdricY8.png)
<!--more-->
```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

def invertTree(root: TreeNode) -> TreeNode:
    if not root:
        return 
    root.right, root.left = invertTree(root.left), invertTree(root.right)
    return root

if __name__ == "__main__":
    root = TreeNode(1)
    root.left = TreeNode(2)
    root.right = TreeNode(3)

    def print_tree(root):
        if root:
            print(root.val)
            print(root.left.val)
            print(root.right.val)

    print_tree(root)
    
    print("\n")

    root = invertTree(root)
    print_tree(root)


```

