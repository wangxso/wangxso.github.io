title: 【剑指 Offer】 07. 重建二叉树
date: 2021-03-02 06:41:30
categories: LeetCode
tags: []
---
https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/
## 思路
#### 首先你要知道已知前序和中序如何手动构建一棵二叉树。
#### step 1 : 从前序序列中找到根结点即前序序列的第一个值
#### step 2 : 然后在中序序列中找到前序序列值所在的位置i，i的左边即为左子树，右边即为右子树
#### step 3 : 然后一步步构建直到所有的值都在树上为止

具体代码实现如下:
```java
package swordToOffer;

import common.TreeNode;
import sun.reflect.generics.tree.Tree;

import java.util.HashMap;
import java.util.Map;

public class BuildTree {
    int preorder[];
    Map<Integer, Integer> inMap = new HashMap<>();
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        // 遍历存储中序的值和位置
        for (int i = 0; i < inorder.length; i++) {
            inMap.put(inorder[i], i);
        }
        // root为根结点在前序遍历的位置, left为左边界, right 为右边界
        return myBuild(0, 0, inorder.length - 1);
    }

    public TreeNode myBuild(int root, int left, int right) {
        // 首先判断左边是否超过了右边，即递归结束标志
        if (left > right) return null;
        // 创建新的(子树)根结点
        TreeNode node = new TreeNode(preorder[root]);
        // 找到根结点在中序遍历序列的位置
        int i = inMap.get(preorder[root]);
        // 左子树的根结点位置只需前序序列前移一位即可比如 3 -> 9
        // 左边界不变，右边界变成找到根结点的左边一个
        node.left = myBuild(root + 1,left, i-1);//左子树遍历
        // 右子树根结点的位置要跳过左子树的个数即 i - left + 1, 比如中序序列是 9 3 15 20 7 ，前序序列是 3 9 20 15 7,
        //                                                                  ^
        //                                                                  i = 1
        // 要找到右子树的根结点必须跳过9，即 root + i - left + 1 = 0 + 1 - 0 + 1 = 2 即前序序列中从3向前移动2格到20
        node.right = myBuild(root + i - left + 1, i + 1, right);// 有子树遍历
        return node;
    }
}```
