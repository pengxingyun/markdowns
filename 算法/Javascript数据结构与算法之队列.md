# Javascript数据结构与算法之队列

### 队列的定义:
队列是一种特殊的线性表，仅能够在队列头部进行操作，有着先进先出(后进后出)的特性。即FIFO(first in first out)。

简单来说队列就是列表。

### 队列的实现：
队列有以下几个方法：
* enqueue 从队列尾部添加一个元素
* dequeue 从队列头部删除一个元素
* head 返回头部的元素，注意，不是删除
* size 返回队列大小
* clear 清空队列
* isEmpty 判断队列是否为空
* tail 返回队列尾节点

show me code:
````js
class Queue {
    constructor() {
        this.data = [];
    }
    enqueue(x) {
        this.data.push(x);
    }
    dequeue() {
        return this.data.shift();
    }
    head() {
        return this.data[0];
    }
    tail() {
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
### 队列的应用练习：
leetcode题目练习：

1. [用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)【简单】
2. [用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)【简单】
3. [滑动窗口的最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)【困难】

#### 1. 用栈实现队列
##### 题目描述：
使用栈实现队列的下列操作：

* push(x) -- 将一个元素放入队列的尾部。
* pop() -- 从队列首部移除元素。
* peek() -- 返回队列首部的元素。
* empty() -- 返回队列是否为空。

##### 示例：
````js
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);  
queue.peek();  // 返回 1
queue.pop();   // 返回 1
queue.empty(); // 返回 false
````

##### 解题思路：

用两个栈实现队列，保证添加只能添加到队列尾部，删除只能删除队列头部。
删除的时候删除stack1的数据，添加的时候添加到stack2。这就用栈实现了队列。

代码实现：

````js
/**
 * Initialize your data structure here.
 */
var MyQueue = function() {
    this.stack1 = [];
    this.stack2 = [];
};

/**
 * Push element x to the back of queue. 
 * @param {number} x
 * @return {void}
 */
MyQueue.prototype.push = function(x) {
    this.stack2.push(x);
};

/**
 * Removes the element from in front of queue and returns that element.
 * @return {number}
 */
MyQueue.prototype.pop = function() {
    if(this.stack1.length) {
        return this.stack1.pop();
    } else {
        if(!this.stack2.length) return false; // 两个栈都没数据返回false
        while(this.stack2.length) {
            this.stack1.push(this.stack2.pop()); // 数据都装载到stack1 装载完之后stack1栈顶就是队列首部
        }
        return this.stack1.pop();
    }
};

/**
 * Get the front element.
 * @return {number}
 */
MyQueue.prototype.peek = function() {
    if(this.stack1.length) {
        return this.stack1[this.stack1.length - 1];
    } else {
        if(!this.stack2.length) return false; // 两个栈都没数据返回false
        while(this.stack2.length) {
            this.stack1.push(this.stack2.pop()); // 数据都装载到stack1 装载完之后stack1栈顶就是队列首部
        }
        return this.stack1[this.stack1.length - 1];
    }
};

/**
 * Returns whether the queue is empty.
 * @return {boolean}
 */
MyQueue.prototype.empty = function() {
    return !this.stack1.length && !this.stack2.length;
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * var obj = new MyQueue()
 * obj.push(x)
 * var param_2 = obj.pop()
 * var param_3 = obj.peek()
 * var param_4 = obj.empty()
 */
````

#### 2. 用队列实现栈
##### 题目描述：
使用队列实现栈的下列操作：

* push(x) -- 元素 x 入栈
* pop() -- 移除栈顶元素
* top() -- 获取栈顶元素
* empty() -- 返回栈是否为空

##### 解题思路：
用队列实现栈，数据添加到queue2, 需要pop或者top的时候把队列1弹出到只剩一个元素，添加到另一个队列。操作队列1剩下的那个元素。这个就是栈顶。

代码实现：
````js
/**
 * Initialize your data structure here.
 */
var MyStack = function() {
    this.queue1 = [];
    this.queue2 = [];
};

/**
 * Push element x onto stack. 
 * @param {number} x
 * @return {void}
 */
MyStack.prototype.push = function(x) {
    this.queue2.push(x);
};

/**
 * Removes the element on top of the stack and returns that element.
 * @return {number}
 */
MyStack.prototype.pop = function() {
    if(!this.queue2.length && !this.queue1.length) return -1;
    if(!this.queue2.length) {
        while(this.queue1.length > 1) {
            this.queue2.push(this.queue1.shift());
        }
        return this.queue1.shift();
    } else {
        while(this.queue2.length > 1) {
            this.queue1.push(this.queue2.shift());
        }
        return this.queue2.shift();
    }
    
};

/**
 * Get the top element.
 * @return {number}
 */
MyStack.prototype.top = function() {
    if(!this.queue2.length && !this.queue1.length) return -1;
    if(!this.queue2.length) {
        while(this.queue1.length > 1) {
            this.queue2.push(this.queue1.shift());
        }
        // 这里最后还是要弹出添加到this.queue2 
        // 不然下次操作的时候这一个就会变成栈底元素（现在是栈顶）
        let val = this.queue1[0];
        this.queue2.push(this.queue1.shift());
        return val;
    } else {
        while(this.queue2.length > 1) {
            this.queue1.push(this.queue2.shift());
        }
        return this.queue2[0];
    }
};

/**
 * Returns whether the stack is empty.
 * @return {boolean}
 */
MyStack.prototype.empty = function() {
    return !this.queue1.length && !this.queue2.length;
};

/**
 * Your MyStack object will be instantiated and called as such:
 * var obj = new MyStack()
 * obj.push(x)
 * var param_2 = obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.empty()
 */
````

#### 3. 滑动窗口的最大值
##### 题目描述：
给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。

##### 进阶：

你能在线性时间复杂度内解决此题吗？
##### 示例:
````
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
````

`提示`: 你可以假设 k 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

##### 解题思路：
1. 暴力解法：按题意写，每次比较k个数，滑动窗口至尾部
    代码实现：
````js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var maxSlidingWindow = function(nums, k) {
    if(!nums.length || !k) return [];
    // 暴力
    let i = 0, len = nums.length, res = [];
    while(i < len - k + 1) {
        res.push(Math.max.apply(null, nums.slice(i, i + k)));
        i++;
    }
    return res;
};
````
不过这个复杂度是O(Nk), 其实还有线性的解。


1. 双向队列：每次队列里面头部存的都是最大值。队列是一个降序排列数组。
    1. 获取0-k的最大值
    2. 从下标k开始循环，如果下标超过了当前窗口 queue.shift()
    3. 队列尾部和当前值比较，形成降序排列队列
    4. 最大值推入数组

````js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var maxSlidingWindow = function(nums, k) {
    let queue = [], res = [];
    for(let i = 0; i < k; i++) { // 获取0-k的最大值
        while(queue[queue.length - 1] < nums[i]) queue.pop();
        queue.push(nums[i]);
    }
    res.push(queue[0]);
    for(let i = k, len = nums.length; i < len; i++) {// 从下标k开始循环
        if(queue[0] === nums[i - k]) queue.shift(); // 如果下标超过了当前窗口
        while(queue[queue.length - 1] < nums[i]) queue.pop();// 降序排列
        queue.push(nums[i]);
        res.push(queue[0]); // 最大值推入结果
    }
    return res;
};
````