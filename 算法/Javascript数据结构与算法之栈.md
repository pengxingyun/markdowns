# Javascript数据结构与算法之栈

### 栈的定义:
栈是一种特殊的线性表，仅能够在栈顶进行操作，有着先进后出(后进先出)的特性。即LIFO(last in first out)。

简单来说就是每次只能操作栈顶的数据，添加只能添加到栈顶，弹出也只能弹出栈顶。

### 栈的实现：
栈有以下几个方法：
* push 添加一个元素到栈顶
* pop 弹出栈顶元素
* top 返回栈顶元素，注意，不是弹出
* isEmpty 判断栈是否为空
* size 返回栈里元素的个数
* clear 清空栈

show me code:
````js
class Stack {
    constructor() {
        this.data = [];
    }
    push(x) {
        this.data.push(x);
    }
    pop() {
        return this.data.pop();
    }
    top() {
        return this.data[this.data.length - 1];
    }
    isEmpty() {
        return !this.data.length;
    }
    size() {
        return this.data.length;
    }
    clear() {
        this.data.length = 0;
    }
}
````
### 栈的应用练习：
leetcode题目练习：

1. [实现一个有min方法的栈](https://leetcode-cn.com/problems/min-stack-lcci/)【简单】
2. [逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)【简单】
3. [每日温度](https://leetcode-cn.com/problems/daily-temperatures/)【中等】

#### 1. 实现一个有min方法的栈
##### 题目描述：
请设计一个栈，除了常规栈支持的pop与push函数以外，还支持min函数，该函数返回栈元素中的最小值。执行push、pop和min操作的时间复杂度必须为O(1)。


##### 示例：
````js
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
````

##### 解题思路：

题目要求实现O(1)的时间复杂度，就不能等调用min的时候再去获取最小值，必须在添加数据的同时把当前最小值找出来。

只能牺牲空间换时间：定义一个辅助栈，每次压入辅助栈的都是最小值。这样就能保证栈顶的元素就是当前最小元素。

代码实现：

````js
/**
 * initialize your data structure here.
 */
var MinStack = function() {
    this.stack = [];
    this.helpStack = []; // 这个存每次数据操作时当前栈的最小值
};

/** 
 * @param {number} x
 * @return {void}
 */
MinStack.prototype.push = function(x) {
    let length = this.stack.length;
    if(!length || this.helpStack[length - 1] > x) {
        this.helpStack.push(x); // 第一次添加的时候当前值就是最小值
    } else {
        this.helpStack.push(this.helpStack[length - 1]);
    }
    this.stack.push(x);
};

/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
    this.helpStack.pop();
    return this.stack.pop();
};

/**
 * @return {number}
 */
MinStack.prototype.top = function() {
    return this.stack[this.stack.length - 1];
};

/**
 * @return {number}
 */
MinStack.prototype.getMin = function() {
    return this.helpStack[this.stack.length - 1];
};

/**
 * Your MinStack object will be instantiated and called as such:
 * var obj = new MinStack()
 * obj.push(x)
 * obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.getMin()
 */
````

#### 2. 逆波兰表达式求值
##### 题目描述：
根据 逆波兰表示法，求表达式的值。

有效的运算符包括 +, -, *, / 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

 

##### 说明：

整数除法只保留整数部分。
给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。
 
##### 示例 1：
````
输入: ["2", "1", "+", "3", "*"]
输出: 9
解释: 该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9
````
##### 示例 2：

````
输入: ["4", "13", "5", "/", "+"]
输出: 6
解释: 该算式转化为常见的中缀算术表达式为：(4 + (13 / 5)) = 6
````

##### 逆波兰表达式：

逆波兰表达式是一种后缀表达式，所谓后缀就是指算符写在后面。

平常使用的算式则是一种中缀表达式，如 ( 1 + 2 ) * ( 3 + 4 ) 。
该算式的逆波兰表达式写法为 ( ( 1 2 + ) ( 3 4 + ) * ) 。
逆波兰表达式主要有以下两个优点：

去掉括号后表达式无歧义，上式即便写成 1 2 + 3 4 + * 也可以依据次序计算出正确结果。
适合用栈操作运算：遇到数字则入栈；遇到算符则取出栈顶两个数字进行计算，并将结果压入栈中。

##### 解题思路：
说明已经把解题方法都讲清楚了：遇到数字则入栈；遇到算符则取出栈顶两个数字进行计算，并将结果压入栈中。

代码实现：
````js
/**
 * @param {string[]} tokens
 * @return {number}
 */
var evalRPN = function(tokens) {
    let stack = [], set = new Set(['+', '-', '*', '/']); // 操作符先预存
    for(let i = 0， len = tokens.length; i < len; i++) {
        if(set.has(tokens[i])) {
            let a = stack.pop(), b = stack.pop();
            let res = parseInt(eval(`(${b})${tokens[i]}(${a})`));
            stack.push(res);
        } else {
            stack.push(tokens[i]);
        }
    }
    return stack.pop();
};
````

#### 3. 每日温度
##### 题目描述：
请根据每日 气温 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。

例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

`提示`: 气温列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数。

##### 示例 1：
````
输入: [73, 74, 75, 71, 69, 72, 76, 73]
输出: [1, 1, 4, 2, 1, 1, 0, 0]
````

##### 解题思路：
题目要求从当前往后找到比当前数字大的第一个数字。这里可以用`单调栈`从后往前找。

每次循环从栈里找比当前值大的值，没有找到就一直弹，弹出去的值后面已经不需要再判断了。因为当前值都已经是最大值了。

代码实现：
````js
/**
 * @param {number[]} T
 * @return {number[]}
 */
var dailyTemperatures = function(T) {
    let stack = [], res = new Array(T.length);
    for(let i = T.length - 1; i >= 0; i--) {
        // 弹出值
        while(stack.length && T[stack[stack.length - 1]] <= T[i]) { 
            stack.pop();
        }
        res[i] = stack[stack.length - 1] ? stack[stack.length - 1] - i : 0;
        stack.push(i); // 每次循环把当前下标加入栈
    }
    return res;
};
````