# # Javascript数据结构与算法之二叉树

### 二叉树的定义:
树是一种非线性的数据结构，以分层的方式存储数据。二叉树是一种特殊的树，它的子节点个数不超过两个。

在使用 JavaScript 构建二叉树之前，需要给我们关于树的词典里再加两个新名词。一个父节点的两个子节点分别称为`左节点`和`右节点`。

定义节点：
````js
class Node {
    constructor(val) {
        this.val = val;
        this.left = this.right = null;
    }
}
````
### 二叉树的应用练习：
leetcode题目练习：

1. [二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)【中等】
2. [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)【中等】
3. [二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)【困难】

#### 1. 二叉树的前序遍历
##### 题目描述：
给定一个二叉树，返回它的 *前序* 遍历。

###### 示例:
````
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [1,2,3]
进阶: 递归算法很简单，你可以通过迭代算法完成吗？
````

##### 解题思路：

前序遍历、后序遍历、中序遍历只是父节点的打印顺序不同。

代码实现：

迭代：
````js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var preorderTraversal = function(root) {
    if(!root) return [];
    let stack = [root], res = [];
    while(stack.length) {
        let temp = stack.pop();
        res.push(temp.val);
        if(temp.right) stack.push(temp.right); // 先把右节点推入栈 这样每次就会先弹出左节点 处理完左节点再处理右节点
        if(temp.left) stack.push(temp.left);
    }
    return res;
};
````

递归：
````js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var preorderTraversal = function(root) {
    if(!root) return [];
    let res = [];
    res.push(root.val);
    res.push(...preorderTraversal(root.left));
    res.push(...preorderTraversal(root.right));
    return res;
};
````
#### 2. 二叉树的中序遍历
##### 题目描述：
给定一个二叉树，返回它的*中序*遍历。

###### 示例:
````
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
````
**进阶:** 递归算法很简单，你可以通过迭代算法完成吗？

##### 解题思路：

前序遍历、后序遍历、中序遍历只是父节点的打印顺序不同。

代码实现：

循环：
````js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    let stack = [], res = [], head = root;
    while(stack.length || head) {
        if(head) {
            stack.push(head);
            head = head.left; // 一直找到最左节点
        } else {
            head = stack.pop();
            res.push(head.val);
            head = head.right;
        }
    }
    return res;
};
````
递归：
````js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    if(!root) return [];
    let res = [];
    
    res.push(...inorderTraversal(root.left));
    res.push(root.val);
    res.push(...inorderTraversal(root.right));
    return res;
}
````
#### 3. 二叉树的后序遍历
##### 题目描述：
给定一个二叉树，返回它的 *后序* 遍历。

###### 示例:
````
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
````
**进阶:** 递归算法很简单，你可以通过迭代算法完成吗？

##### 解题思路：
跟前序遍历一样，只不过这里添加值从头部插入，并且先遍历右节点再遍历左节点

循环：
````js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var postorderTraversal = function(root) {
    if(!root) return [];
    let stack = [root], res = [];
    while(stack.length) {
        let temp = stack.pop();
        res.unshift(temp.val);
        if(temp.left) stack.push(temp.left); // 先把左节点推入栈 这样每次就会先弹出右节点 处理完右节点再处理左节点
        if(temp.right) stack.push(temp.right);
    }
    return res;
};
````
递归：
````js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var postorderTraversal = function(root) {
    if(!root) return [];
    let res = [];
    res.push(...postorderTraversal(root.left));
    res.push(...postorderTraversal(root.right));
    res.push(root.val);
    return res;
}
````