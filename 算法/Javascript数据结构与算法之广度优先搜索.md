# Javascript数据结构与算法之广度优先搜索

### 广度优先搜索的定义:
广度优先搜索从第一个顶点开始，尝试访问尽可能靠近它的顶点。本质上，这种搜索在**图**上是逐层移动的，首先检查最靠近第一个顶点的层，再逐渐向下移动到离起始顶点最远的层。

广度优先搜索算法使用了抽象的队列而不是数组来对已访问过的顶点进行排序。其算法的工作原理如下:

1. 查找与当前顶点相邻的未访问顶点，将其添加到已访问顶点列表及队列中; 
2. 从图中取出下一个顶点 v，添加到已访问的顶点列表;
3. 将所有与 v 相邻的未访问顶点添加到队列。

### 广度优先搜索的实现：
模版:
````js
function bfs(s, target) {
    let queue = [];
    queue.push(s);
    while(queue.length) {
        let size = queue.length;
        while(size-- > 0) {
            let tmp = queue.shift(); // 队列首部出列
            if(temp === target) { // 等于target直接返回
                return true;
            }
            for (Node next : the neighbors of tmp) { // 伪代码 表示当前节点的下一层节点
                queue.push(next); // 进队列
            }
        }
    }
}
````
### 广度优先搜索的应用练习：
leetcode题目练习：

1. [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)【中等】
2. [腐烂的橘子](https://leetcode-cn.com/problems/rotting-oranges/)【中等】
3. [跳跃游戏IV](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)【简单】

#### 1. 二叉树的层序遍历
##### 题目描述：
给你一个二叉树，请你返回其按**层序遍历**得到的节点值。 （即逐层地，从左到右访问所有节点）。

##### 示例：

二叉树：[3,9,20,null,null,15,7],
````
    3
   / \
  9  20
    /  \
   15   7
````
返回其层次遍历结果：
````
[
  [3],
  [9,20],
  [15,7]
]
````
##### 解题思路：

直接套用模版

代码实现：

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
 * @return {number[][]}
 */
var levelOrder = function(root) {
    if(root === null) return [];
    let queue = [], ret = [];
    queue.push(root);
    while(queue.length) {
        let size = queue.length;
        let arr = [];
        while(size-- > 0) {
            let tmp = queue.shift();
            arr.push(tmp.val);
            if(tmp.left) queue.push(tmp.left); // 左节点进队
            if(tmp.right) queue.push(tmp.right); // 右节点进队
        }
        ret.push([...arr]);
    }
    return ret;
};
````

#### 2. 腐烂的橘子
##### 题目描述：
在给定的网格中，每个单元格可以有以下三个值之一：

* 值 0 代表空单元格；
* 值 1 代表新鲜橘子；
* 值 2 代表腐烂的橘子。

每分钟，任何与腐烂的橘子（在 4 个正方向上）相邻的新鲜橘子都会腐烂。

返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1。

##### 示例1:
````
输入：[[2,1,1],[1,1,0],[0,1,1]]
输出：4
````
##### 示例2:
````
输入：[[2,1,1],[0,1,1],[1,0,1]]
输出：-1
解释：左下角的橘子（第 2 行， 第 0 列）永远不会腐烂，因为腐烂只会发生在 4 个正向上。
````
##### 示例3:
````
输入：[[0,2]]
输出：0
解释：因为 0 分钟时已经没有新鲜橘子了，所以答案就是 0 。
````
##### 提示：
* 1 <= grid.length <= 10
* 1 <= grid[0].length <= 10
* grid[i][j] 仅为 0、1 或 2

##### 解题思路：
1. 找出所有腐烂橘子 得到所有橘子的数量 visited统计腐烂橘子数量
2. bfs 统计时间
3. visited.size不等于count说明没有腐烂所有橘子

````js
/**
 * @param {number[][]} grid
 * @return {number}
 */
var orangesRotting = function(grid) {
    // 1：找出所有腐烂橘子 得到所有橘子的数量 visited统计腐烂橘子数量
    // 2: bfs 统计时间
    // 3. visited.size不等于count说明没有腐烂所有橘子
    let cols = grid.length, rows = grid[0].length, visited = new Set(), queue = [], count = 0, ans = 0;
    for(let i = 0; i < cols; i++) {
        for(let j = 0; j < rows; j++) {
            if(grid[i][j] === 2) {
                queue.push([i, j]); // 找腐烂橘子的位置
                visited.add(`${i}-${j}`);
            }
            if(grid[i][j]) count++; // 有多少橘子
        }
    }

    if(!queue.length && !count) return 0; // 没有橘子
    if(!queue.length) return -1; // 没有腐烂橘子
    let dir = [[-1, 0], [1, 0], [0, 1], [0, -1]]; // 左右上下
    while(queue.length) {
        let size = queue.length;
        while(size-- > 0) {
            let [x,y] = queue.shift();
            for(let i = 0; i < 4; i++) {
                let next_x = x + dir[i][0], next_y = y + dir[i][1];
                if(next_x >= 0 && next_y >= 0 && next_x < cols && next_y < rows 
                && grid[next_x][next_y] === 1 && !visited.has(`${next_x}-${next_y}`)) { // 这里visited用于剪枝
                    queue.push([next_x, next_y]);
                    visited.add(`${next_x}-${next_y}`);
                }
            }
        }
        ans++;
    }
    return visited.size === count ? ans - 1 : -1; // 第一次不算时间
};
````
#### 3. 跳跃游戏 IV
##### 题目描述：
给你一个整数数组 arr ，你一开始在数组的第一个元素处（下标为 0）。

每一步，你可以从下标 i 跳到下标：

* i + 1 满足：i + 1 < arr.length
* i - 1 满足：i - 1 >= 0
* j 满足：arr[i] == arr[j] 且 i != j

请你返回到达数组最后一个元素的下标处所需的**最少操作次数**。

*注意*：任何时候你都不能跳到数组外面。

 

##### 示例 1：
````
输入：arr = [100,-23,-23,404,100,23,23,23,3,404]
输出：3
解释：那你需要跳跃 3 次，下标依次为 0 --> 4 --> 3 --> 9 。下标 9 为数组的最后一个元素的下标。
````
##### 示例 2：
````
输入：arr = [7]
输出：0
解释：一开始就在最后一个元素处，所以你不需要跳跃。
````
##### 示例 3：
````
输入：arr = [7,6,9,6,9,6,9,7]
输出：1
解释：你可以直接从下标 0 处跳到下标 7 处，也就是数组的最后一个元素处。
````
##### 示例 4：
````
输入：arr = [6,1,9]
输出：2
````
##### 示例 5：
````
输入：arr = [11,22,7,7,7,7,7,7,7,22,13]
输出：3
````

##### 提示：
* 1 <= arr.length <= 5 * 10^4
* -10^8 <= arr[i] <= 10^8

##### 解题思路：
1. 计数同值，如果连续同值，只要第一个和最后一个的下标，避免一直重复读取，影响执行时间复杂度。
2. +1下标加入队列，-1下标加入队列，还有同值下标。
3. 返回ans

````js
/**
 * @param {number[]} arr
 * @return {number}
 */
const minJumps = function(arr) {
    if(arr.length === 1) return 0;
    let map = new Map(), 
        queue = [0], 
        visited = new Set([0]),  // 剪枝
        ans = 0;
    let count = 0; // 计数同值 如果连续同值 只要第一个和最后一个的下标
    for(let i = 0; i < arr.length; i++) {
        if(arr[i] === arr[i + 1]) {
            if(count > 0) {
                continue;
            } else {
                count++;
                map.set(arr[i], map.has(arr[i]) ? map.get(arr[i]).add(i) : new Set([i]));
            }
        } else {
            count = 0;
            map.set(arr[i], map.has(arr[i]) ? map.get(arr[i]).add(i) : new Set([i]));
        }
        
    }

    while(queue.length) {
        let size = queue.length;
        while(size-- > 0) {
            let temp = queue.shift();
            if(temp === arr.length -1) return ans; // 已经到最后一个了
            let a = temp + 1;
            let b = temp - 1;
            // 同值 或者下标是arr.length - 2
            if(arr[temp] === arr[arr.length - 1] || a === arr.length - 1) return ans + 1;
            if(a < arr.length && !visited.has(a)) { // 加一
                queue.push(a);
                visited.add(a);
            }
            if(b >= 0 && !visited.has(b)) { // 减一
                queue.push(b);
                visited.add(b);
            }
            let set = map.get(arr[temp]);
            (set.size > 1) && set.forEach(item => { // 同值加入队列
                if(!visited.has(item)) {
                    queue.push(item);
                    visited.add(item);
                }
            });
        }
        ans++;
    }
};
````
