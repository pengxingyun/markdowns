# Javascript数据结构与算法之回溯算法

### 回溯算法的定义:
回溯算法实际上一个类似枚举的搜索尝试过程，主要是在搜索尝试过程中寻找问题的解，当发现已不满足求解条件时，就 “回溯” 返回，尝试别的路径。

### 回溯算法的应用练习：
leetcode题目练习：

1. [单词搜索](https://leetcode-cn.com/problems/word-search/)【中等】
2. [电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)【中等】
3. [N皇后](https://leetcode-cn.com/problems/n-queens/)【困难】

#### 1. 单词搜索
##### 题目描述：
给定一个二维网格和一个单词，找出该单词是否存在于网格中。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

###### 示例:
````
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true
给定 word = "SEE", 返回 true
给定 word = "ABCB", 返回 false
````

###### 提示：

* board 和 word 中只包含大写和小写英文字母。
* 1 <= board.length <= 200
* 1 <= board[i].length <= 200
* 1 <= word.length <= 10^3

##### 解题思路：

1. 双层循环判断 word[0] === borad[i][j]
2. 调用find指针下移寻找直到最后一个字符word.length-1
代码实现：

````js
/**
 * @param {character[][]} board
 * @param {string} word
 * @return {boolean}
 */

var exist = function(board, word) {
    if(board.length === 0) return false;
    if(word.length === 0) return true;
    let row = board.length, col = board[0].length;
    let find = (i, j, cur) => {
        if(i < 0 || i >= row) return false;
        if(j < 0 || j >= col) return false;
        
        let letter = word[cur];
        if(board[i][j] !== letter) return false;
        if(cur === word.length - 1) return true;
        board[i][j] = null; // 剪枝 防止回来的时候用到当前节点
        let ret = find(i + 1, j, cur+1) || 
                  find(i - 1, j, cur + 1) ||
                  find(i, j + 1, cur + 1) ||
                  find(i, j - 1, cur + 1)
        board[i][j] = letter; // 搜索完赋值回来
        return ret;
    }
    
    for(let i = 0; i < row; i++) {
        for(let j = 0; j < col; j++) {
            if(board[i][j] === word[0] && find(i, j, 0)) return true; // 搜索成功直接返回
        }
    }
    return false;
}
````

#### 2. 电话号码的字母组合
##### 题目描述：
给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。
##### 示例:
````
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
````
##### 说明：
尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

##### 解题思路：
上面题目可以定义成这样：

已知map = ['', '', 'abc', 'def', 'ghi', 'jkl', 'mno', 'pqrs', 'tuv', 'wxyz'];

根据传入的下标字符串，返回电话号码的字母组合。

代码实现：
    
````js
/**
 * @param {string} digits
 * @return {string[]}
 */
var letterCombinations = function(digits) {
    if(!digits) return [];
    let map = ['', '', 'abc', 'def', 'ghi', 'jkl', 'mno', 'pqrs', 'tuv', 'wxyz'];
    let ret = [];
    /**
     * @param {string} str
     * @param {string} next_digits
     */
    let backtrack = (str, next_digits) => {
        if(!next_digits) { // 这里用的是剩下未安排完的电话号码组合 也可以用下标 index===digits.length
            ret.push(str);
        } else {
            let digit = next_digits[0];
            let letters = map[digit];
            for(let i = 0, len = letters.length; i < len; i++) {
                backtrack(str + letters[i], next_digits.slice(1)); // 每次删除第一位
            }
        }
    }
    backtrack('', digits);
    return ret;
}
````
#### 3. N皇后
##### 题目描述：
n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。
给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。

每一种解法包含一个明确的 n 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。

##### 示例:

````
输入: 4
输出: [
 [".Q..",  // 解法 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // 解法 2
  "Q...",
  "...Q",
  ".Q.."]
]
解释: 4 皇后问题存在两个不同的解法。
````

##### 提示:

**皇后**，是国际象棋中的棋子，意味着国王的妻子。皇后只做一件事，那就是“吃子”。当她遇见可以吃的棋子时，就迅速冲上去吃掉棋子。当然，她横、竖、斜都可走一到七步，可进可退。（引用自 百度百科 - 皇后 ）

##### 解题思路：
据题意可知，需要实现以下条件：
1. 同一列不能出现 `Q`
2. 对角线不能出现 `Q`

````js
/**
 * @param {number} n
 * @return {string[][]}
 */
var solveNQueens = function(n) {
    let ans = [];
    let backtrack = (row, tmp = []) => {
        if(row === n) {
            ans.push(tmp.map(item => {
                let arr = new Array(n).fill('.');
                arr[item] = 'Q';
                return arr.join(''); // 返回的是[['', '']] 得转成字符串返回
            }))
            return;
        }
        for(let i = 0; i < n; i++) {
            let cannotSet = tmp.some((ci, ri) => {
                return ci === i || // 不能等于已有的列
                       (ri - ci) === (row - i) || // 往右对角线
                       (ri + ci) === (row + i) // 往左对角线
            })
            if(cannotSet) continue;
            backtrack(row + 1, [...tmp, i]);
        }
    }
    backtrack(0);
    return ans;
};
````
