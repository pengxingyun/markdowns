# web前端大厂10道经典面试题汇总

## web前端大厂10道经典面试题汇总

#### 1. 写一个js函数，实现对一个数字每3位加一个逗号，如输入100000， 输出100,000（不考虑负数，小数）

解题思路：有API就用API
````js
function format(num) {
    return num.toLocaleString();
}
    
format(123456.789) // 123,456.789
````    
#### 2. 给定一个字符串，找出其中无重复字符的最长子字符串长度

解题思路：**slide window**
````js
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    if(!s) return 0; // 边界条件 '' 返回0
    if(s.length === 1) return 1; // 边界条件 'a' 返回1
    let count = 0, start = 0, map = new Map();
    for(let i = 0; i < s.length; i++) {
        if(map.has(s[i])) {
            count = Math.max(i - start, count); // 取最大的长度值
            start = Math.max(map.get(s[i]) + 1, start); // 最大的开始下标
        }
        map.set(s[i], i); // 不管怎样都要把当前值存下标
    }
    return Math.max(s.length - start, count); // 这里也得做处理 拿最大值
};
````
#### 3. 实现超出整数存储范围的两个大正整数相加

解题思路： 
1.把两个数组前面补0补到一样长度 
2.从后到前逐个遍历相加 
3.最后有进位要进位+1
````js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
var addStrings = function(num1, num2) {
    let maxLen = num1.length > num2.length ? num1.length : num2.length;
    num1 = num1.padStart(maxLen, '0');
    num2 = num2.padStart(maxLen, '0');
    let f = 0; // 进位
    let res = ''; // 最后返回的结果
    for(let i = maxLen - 1; i >= 0; i--) { // 循环最大的长度
        let t = (num1[i] - 0) + (num2[i] - 0) + f;
        f = t > 9 ? 1 : 0;
        res = t % 10 + res;
    }
    if(f) return 1 + res;
    return res;
};
````  
#### 4. 任意二维数组的全排列组合
解题思路：回溯 + 递归
````js
/**
 * @param {number[][]} nums
 * @return {number[][]}
 */
var permute = function(nums) {
    if(!nums.length) return [];
    let res = [];
    let backtrack = (temp, index) => {
        // 执行到最后一个元素
        if(index === nums.length) {
            res.push(temp);
        } else {
            let item = nums[index];
            for(let i = 0, len = item.length; i < len; i++) {
                backtrack([...temp, item[i]], index + 1);
            }
        }
    }
    backtrack([], 0)
    return res;
}

permute([[1,2], [2,3,4]]) // [[1,2],[1,3],[1,4],[2,2],[2,3],[2,4]]
````
#### 5. 公司最近新研发了一种产品，共生产了n件。有m个客户想购买此产品，已知每个顾客出价。为了确保公平，公司决定要以一个固定的价格出售产品。每一个出价不低于要价的客户将会得到产品（每人只买一个），余下的将会被拒绝购买。请你找出能让公司利润最大化的售价。

````js
/**
 * @param {number} n
 * @param {array} m
 * @return {number}
 */
var getMaxRateVal = function(n, m) {
    m.sort((a,b) => b - a); // 降序排序
    if(n <= m.length) { // 供不应求
        return m[n - 1];    
    } else {
        return m[m.length - 1];
    }
}
````

#### 6. 计算出字符串中出现次数最多的字符是什么，出现了多少次？
解题思路：map
````js
/**
 * @param {string} str
 * @return {array}
 */
var getMaxCountChar = function (str) {
    let map = new Map(), max = -Infinity, res = '';
    for(let i = 0, len = str.length; i < len; i++) {
        map.set(str[i], map.get(str[i]) ? map.get(str[i]) + 1 : 1);
    }
    for(let [key, val] of map) {
        if(val > max) {
            max = val;
            str = key;
        }
    }
    return [str, max];
}
getMaxCountChar('abca') // ['a', 2]
````
#### 7. "123456789876543212345678987654321..."的第n位是什么？
解题思路：找规律
1. 1234567898765432 为一个节 16位
2. n = n % 16
3. 找前面16位中的n - 1位 注意：n === 0 返回最后一位 即2

````js
/**
 * @param {number} n
 * @return {array}
 */
var findNChar = function(n) {
    if(!n) return 0;
    let str = '1234567898765432';
    n %= 16;
    return n === 0 ? '2' : str[n - 1];
}
````
#### 8. 请编写一个 JavaScript 函数 parseQueryString，它的用途是把 URL 参数解析为一个对象—淘宝面试题
````js
/**
 * @param {string} str
 * @return {object}
 */
var parseQueryString = function(str) {
    let paramStr = str.split('?')[1], res = {};
    paramStr && paramStr.split('&').forEach(item => {
        let tem = item.split('=');
        res[tem[0]] = tem[1];
    })
    return res;
}
````
#### 9. 如果给定的字符串是回文，返回true，反之，返回false。回文：如果一个字符串忽略标点符号、大小写和空格，正着读和反着读一模一样，那么这个字符串就是palindrome(回文)。
````js
/**
 * @param {string} s
 * @return {boolean}
 */
var palindrome = function(s) {
    // 先把多余的替换掉
    s = s.replace(/[^a-z|A-Z|0-9]/g, '').toLowerCase();
    for(let i = 0, j = s.length - 1; i < j; i++, j--) {
        if(s[i] !== s[j]) {
            return false;
        }
    }
    return true;
}
````

#### 10. 确保字符串的每个单词首字母都大写，其余部分小写。
````js
/**
 * @param {string} str
 * @return {array}
 */
var toFormatString = function(str) {
    let arr = str.toLowerCase().split(' ');
    for(let i = 0; i < arr.length; i++) {
        console.log(arr[i]);
        arr[i] = arr[i][0].toUpperCase() + arr[i].slice(1);
    }
    return arr.join(' ');
}
````




