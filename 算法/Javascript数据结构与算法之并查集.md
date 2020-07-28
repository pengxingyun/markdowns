# Javascript数据结构与算法之并查集

### 并查集的定义:
在一些应用问题中，你需要`将n个不同的元素划分成多个不相交的集合`，并查集是一种非常简单但是非常有效的集合。它支持下面3种操作：

* union(root1, root2) 把集合root2合并入集合root1中，要求是root1和root2互不相交。
* find(x) 搜索x所在的集合，返回该集合的名字。
* init， 将s个元素初始化为s个只有一个元素的子集合。

### 并查集的实现：

show me code:

````js
class UFSets {
    constructor(length) {
        // init初始化
        this.parent = new Array(length);
        for(let i = 0; i < length; i++) {
            this.parent[i] = i;
        }
    }
    find(index) {
        // 搜索x所在的集合，返回该集合的名字
        while (this.parent[index] != index) {
            index = this.parent[index];
        }
        return this.parent[index];
    }
    union(x, y) {
        // 把集合root2合并入集合root1中，要求是root1和root2互不相交
        let xParent = this.find(x);
        let yParent = this.find(y);
        if(xParent!==yParent){
            this.parent[xParent] = yParent;
        }
    }
}
````
### 并查集的应用练习：
leetcode题目练习：

1. [朋友圈](https://leetcode-cn.com/problems/friend-circles/)【中等】
2. [等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)【中等】

#### 1. 朋友圈
##### 题目描述：
班上有 N 名学生。其中有些人是朋友，有些则不是。他们的友谊具有是传递性。如果已知 A 是 B 的朋友，B 是 C 的朋友，那么我们可以认为 A 也是 C 的朋友。所谓的朋友圈，是指所有朋友的集合。

给定一个 N * N 的矩阵 M，表示班级中学生之间的朋友关系。如果M[i][j] = 1，表示已知第 i 个和 j 个学生互为朋友关系，否则为不知道。你必须输出所有学生中的已知的朋友圈总数。

##### 示例 1:
````
输入: 
[[1,1,0],
 [1,1,0],
 [0,0,1]]
输出: 2 
说明：已知学生0和学生1互为朋友，他们在一个朋友圈。
第2个学生自己在一个朋友圈。所以返回2。
````
##### 示例 2:
````
输入: 
[[1,1,0],
 [1,1,1],
 [0,1,1]]
输出: 1
说明：已知学生0和学生1互为朋友，学生1和学生2互为朋友，所以学生0和学生2也是朋友，所以他们三个在一个朋友圈，返回1。
````

##### 解题思路：

找出这些人里面的交集数量，刚好可以用上`UFSets`

代码实现：

````js
/**
 * @param {number[][]} M
 * @return {number}
 */
let findCircleNum = (M) => {
    let len = M.length;
    let sets = new UFSets(len);
    
    for(let i = 0; i < len; i++) {
        for(let j = 0; j < len; j++) {
            if(M[i][j] === 1 && i !== j) {
                sets.union(i, j); // 合并朋友圈
            }
        }
    }
    let count = 0;
    for(let i = 0; i < len; i++) {
        if(sets.parent[i] === i) { // 等于初始化值的算一个
            count++;
        }
    } 
    return count;
}

class UFSets {
    constructor(length) {
        this.parent = new Array(length);
        for(let i = 0; i < length; i++) {
            this.parent[i] = i;
        }
    }
    find(index) {
        while (this.parent[index] != index) {
            index = this.parent[index];
        }
        return this.parent[index];
    }
    union(x, y) {
        let xParent = this.find(x);
        let yParent = this.find(y);
        if(xParent!==yParent){
            this.parent[xParent] = yParent;
        }
    }
}
````

#### 2. 等式方程的可满足性
##### 题目描述：
给定一个由表示变量之间关系的字符串方程组成的数组，每个字符串方程 equations[i] 的长度为 4，并采用两种不同的形式之一："a==b" 或 "a!=b"。在这里，a 和 b 是小写字母（不一定不同），表示单字母变量名。

只有当可以将整数分配给变量名，以便满足所有给定的方程时才返回 true，否则返回 false。 

##### 示例 1：
````
输入：["a==b","b!=a"]
输出：false
解释：如果我们指定，a = 1 且 b = 1，那么可以满足第一个方程，但无法满足第二个方程。没有办法分配变量同时满足这两个方程。
````
##### 示例 2：
````
输出：["b==a","a==b"]
输入：true
解释：我们可以指定 a = 1 且 b = 1 以满足满足这两个方程。
````
##### 示例 3：
````
输入：["a==b","b==c","a==c"]
输出：true
````
##### 示例 4：
````
输入：["a==b","b!=c","c==a"]
输出：false
````
##### 示例 5：
````
输入：["c==c","b==d","x!=z"]
输出：true
````

##### 提示：

1. 1 <= equations.length <= 500
2. equations[i].length == 4
3. equations[i][0] 和 equations[i][3] 是小写字母
4. equations[i][1] 要么是 '='，要么是 '!'
5. equations[i][2] 是 '='

##### 解题思路：
1. 先把===的赋值
2. 比较!==的等式方程

代码实现：
````js
/**
 * @param {string[]} equations
 * @return {boolean}
 */
var equationsPossible = function(equations) {
    let ufset = new UFSets(26);
    for(let i = 0; i < equations.length; i++) {
        let item = equations[i];

        if(item[1] === '=') {
            ufset.union(item[0].charCodeAt() - 97, item[3].charCodeAt() - 97);
        }
    }
    for(let i = 0; i < equations.length; i++) {
        let item = equations[i];

        if(item[1] === '!') {
            if(ufset.find(item[0].charCodeAt() - 97) === ufset.find(item[3].charCodeAt() - 97)) {
                return false;
            }
        }
    }
    return true;
};
class UFSets {
    constructor(length) {
        this.parent = new Array(length);
        for(let i = 0; i < length; i++) {
            this.parent[i] = i;
        }
    }
    find(index) {
        while (this.parent[index] != index) {
            index = this.parent[index];
        }
        return this.parent[index];
    }
    union(x, y) {
        let xParent = this.find(x);
        let yParent = this.find(y);
        if(xParent!==yParent){
            this.parent[xParent] = yParent;
        }
    }
}
````