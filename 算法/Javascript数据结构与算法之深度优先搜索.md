# Javascript数据结构与算法之深度优先搜索

### 深度优先搜索的定义:
深度优先搜索算法（英语：Depth-First-Search，DFS）包括从一条路径的起始顶点开始追溯，直到到达最后一个顶点，然后回溯， 继续追溯下一条路径，直到到达最后的顶点，如此往复，直到没有路径为止。这不是在搜索特定的路径，而是通过搜索来查看在图中有哪些路径可以选择。

### 深度优先搜索的实现：
模版:
````js
function dfs(s, target) {
    this.marked[v] = true;
    for each(var w in this.adj[v]) {
       if (!this.marked[w]) {
          this.dfs(w);
       }
    }
}
````
### 深度优先搜索的应用练习：
leetcode题目练习：

1. [图像渲染](https://leetcode-cn.com/problems/flood-fill/)【简单】
2. [钥匙和房间](https://leetcode-cn.com/problems/keys-and-rooms/)【中等】
3. [岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)【中等】

#### 1. 图像渲染
##### 题目描述：
有一幅以二维整数数组表示的图画，每一个整数表示该图画的像素值大小，数值在 0 到 65535 之间。

给你一个坐标 `(sr, sc)` 表示图像渲染开始的像素值（行 ，列）和一个新的颜色值`newColor`，让你重新上色这幅图像。

为了完成上色工作，从初始坐标开始，记录初始坐标的上下左右四个方向上像素值与初始坐标相同的相连像素点，接着再记录这四个方向上符合条件的像素点与他们对应四个方向上像素值与初始坐标相同的相连像素点，……，重复该过程。将所有有记录的像素点的颜色值改为新的颜色值。

最后返回经过上色渲染后的图像。

###### 示例 1:

````
输入: 
image = [[1,1,1],[1,1,0],[1,0,1]]
sr = 1, sc = 1, newColor = 2
输出: [[2,2,2],[2,2,0],[2,0,1]]
解析: 
在图像的正中间，(坐标(sr,sc)=(1,1)),
在路径上所有符合条件的像素点的颜色都被更改成2。
注意，右下角的像素没有更改为2，
因为它不是在上下左右四个方向上与初始点相连的像素点。
````

###### 注意:

* image 和 image[0] 的长度在范围 [1, 50] 内。
* 给出的初始点将满足 0 <= sr < image.length 和 0 <= sc < image[0].length。
* image[i][j] 和 newColor 表示的颜色值在范围 [0, 65535]内。

##### 解题思路：

直接套用模版

代码实现：

````js
/**
 * @param {number[][]} image
 * @param {number} sr
 * @param {number} sc
 * @param {number} newColor
 * @return {number[][]}
 */
var floodFill = function(image, sr, sc, newColor) {
    let row = image.length, col = image[0].length;
    let oldColor = image[sr][sc];
    let dfs = (i, j) => {
        if(i < 0 || i >= row || j < 0 || j >= col || image[i][j] !== oldColor) { // 递归必须得有返回
            return;
        }
        image[i][j] = newColor;
        dfs(i - 1, j);
        dfs(i + 1, j);
        dfs(i, j - 1);
        dfs(i, j + 1);
    }
    if(image[sr][sc] === newColor) return image;
    dfs(sr, sc);
    return image;
};
````

#### 2. 钥匙和房间
##### 题目描述：
有 N 个房间，开始时你位于 0 号房间。每个房间有不同的号码：0，1，2，...，N-1，并且房间里可能有一些钥匙能使你进入下一个房间。

在形式上，对于每个房间 `i` 都有一个钥匙列表 `rooms[i]`，每个钥匙 `rooms[i][j]` 由 `[0,1，...，N-1]` 中的一个整数表示，其中 `N = rooms.length`。 钥匙 `rooms[i][j] = v` 可以打开编号为 `v` 的房间。

最初，除 `0` 号房间外的其余所有房间都被锁住。

你可以自由地在房间之间来回走动。

如果能进入每个房间返回 `true`，否则返回 `false`。

##### 示例 1：
````
输入: [[1],[2],[3],[]]
输出: true
解释:  
我们从 0 号房间开始，拿到钥匙 1。
之后我们去 1 号房间，拿到钥匙 2。
然后我们去 2 号房间，拿到钥匙 3。
最后我们去了 3 号房间。
由于我们能够进入每个房间，我们返回 true。
````
##### 示例 2：
````
输入：[[1,3],[3,0,1],[2],[0]]
输出：false
解释：我们不能进入 2 号房间。
提示：

1 <= rooms.length <= 1000
0 <= rooms[i].length <= 1000
所有房间中的钥匙数量总计不超过 3000。
````

##### 解题思路：
根据visited的数量确定是否可全部访问

````js
/**
 * @param {number[][]} rooms
 * @return {boolean}
 */
var canVisitAllRooms = function(rooms) {
    let visited = new Set(); // 记录访问的房间数量
    let dfs = (cur) => {
        visited.add(cur);
        let tmp = rooms[cur];
        for(let i = 0, len = tmp.length; i < len; i++) {
            if(!visited.has(tmp[i])) {
                dfs(tmp[i]);
            }
        }
    }
    dfs(0);
    return visited.size === rooms.length; // 访问的数量等于房间的数量 即全部可访问

};
````
#### 3. 岛屿数量
##### 题目描述：
给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

##### 示例 1:
````
输入:
[
['1','1','1','1','0'],
['1','1','0','1','0'],
['1','1','0','0','0'],
['0','0','0','0','0']
]
输出: 1
````
##### 示例 2:
````
输入:
[
['1','1','0','0','0'],
['1','1','0','0','0'],
['0','0','1','0','0'],
['0','0','0','1','1']
]
输出: 3
解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。
````

##### 解题思路：
1. dfs返回1和0
2. ret+=dfs返回的值

````js
/**
 * @param {character[][]} grid
 * @return {number}
 */
var numIslands = function(grid) {
    if(!grid.length || !grid[0].length) return grid;
    let row = grid.length, col = grid[0].length;
    let ret = 0;
    let dfs = (i, j) => {
    	if(grid[i][j] == '0') {
    		return 0;
    	}

    	grid[i][j] = 0;
    	if(i+1 < grid.length && grid[i + 1][j] === '1') {dfs(i+1, j)}
		if(i-1 >= 0 && grid[i - 1][j] === '1') {dfs(i-1, j)}
		if(j+1 < grid[0].length && grid[i][j+1] === '1') {dfs(i, j+1)}
		if(j-1 >= 0 && grid[i][j-1] === '1') {dfs(i, j-1)}

		return 1;
    }
    for(let i = 0; i < row; i++) {
        for(let j = 0; j < col; j++) {
            ret+=dfs(i,j);
        }
    }
    return ret;
};
````
