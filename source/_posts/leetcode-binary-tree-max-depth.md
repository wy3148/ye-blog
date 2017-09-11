---
title: leetcode-binary-tree-max-depth
date: 2017-08-25 22:14:54
tags: java
categories: leetcode binary tree
---

Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.



```java

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxDepth(TreeNode root) {
        
        if(root == null) return 0;
        
        int left = this.maxDepth(root.left);
        int right = this.maxDepth(root.right);
        
        if(left > right){
            return left + 1;
        }else{
            return right+1;
        }
    }
}
```
