# # Javascript数据结构与算法之堆

### 堆的定义:
堆（Heap）是一个可以被看成近似完全二叉树的数组。树上的每一个结点对应数组的一个元素。除了最底层外，该树是完全充满的，而且是从左到右填充。—— 来自：《算法导论》

堆包括最大堆和最小堆：最大堆的每一个节点（除了根结点）的值不大于其父节点；最小堆的每一个节点（除了根结点）的值不小于其父节点。

### 堆的实现：
堆有以下几个方法：
* insert：把一个数值放进已经是堆结构的数组中，并保持堆结构，时间复杂度为*O(log n)*。
* extract：从最大堆中取出最大值或从最小堆中取出最小值，并将剩余的数组保持堆结构，时间复杂度为*O(log n)*。


show me code:
````js
const defaultCmp = (x, y) => x > y; // 默认是最大堆

const swap = (arr, i, j) => ([arr[i], arr[j]] = [arr[j], arr[i]]);

class Heap {
    /**
     * 默认是最大堆
     * @param {Function} cmp
     */
    constructor(cmp = defaultCmp) {
        this.container = [];
        this.cmp = cmp;
    }

    insert(data) {
        const { container, cmp } = this;

        container.push(data);
        let index = container.length - 1;
        while (index) {
            let parent = Math.floor((index - 1) / 2);
            if (!cmp(container[index], container[parent])) {
                return;
            }
            swap(container, index, parent);
            index = parent;
        }
    }

    extract() {
        const { container, cmp } = this;
        if (!container.length) {
            return null;
        }

        swap(container, 0, container.length - 1);
        const res = container.pop();
        const length = container.length;
        let index = 0,
            exchange = index * 2 + 1;

        while (exchange < length) {
            // 以最大堆的情况来说：如果有右节点，并且右节点的值大于左节点的值
            let right = index * 2 + 2;
            if (right < length && cmp(container[right], container[exchange])) {
                exchange = right;
            }
            if (!cmp(container[exchange], container[index])) {
                break;
            }
            swap(container, exchange, index);
            index = exchange;
            exchange = index * 2 + 1;
        }

        return res;
    }

    top() {
        if (this.container.length) return this.container[0];
        return null;
    }
}
````
### 队列的应用练习：
leetcode题目练习：

1. [最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)【简单】
2. [数据流中的第K大元素](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)【简单】
3. [数据流的中位数](https://leetcode-cn.com/problems/find-median-from-data-stream/)【困难】

#### 1. 用栈实现队列
##### 题目描述：
输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

##### 示例 1：
````
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
````
##### 示例 2：
````
输入：arr = [0,1,2,1], k = 1
输出：[0]
````
##### 限制：

* 0 <= k <= arr.length <= 10000
* 0 <= arr[i] <= 10000

##### 解题思路：

建堆，extract`k`个数出来

代码实现：

````js
/**
 * @param {number[]} arr
 * @param {number} k
 * @return {number[]}
 */
var getLeastNumbers = function(arr, k) {
    if(arr.length === k) return arr;
    if(k === 0 || arr.length === 0) return [];
    let heap = new Heap((x, y) => x < y); // 最小堆 Heap源码在上面
    for(let i = 0, len = arr.length; i < len; i++) {
        heap.insert(arr[i]);
    }

    let ret = [];
    while(ret.length < k) {
        ret.push(heap.extract());
    }
    return ret;
}
````

#### 2. 数据流中的第K大元素
##### 题目描述：
设计一个找到数据流中第K大元素的类（class）。注意是排序后的第K大元素，不是第K个不同的元素。

你的 `KthLargest` 类需要一个同时接收整数 `k` 和整数数组`nums` 的构造器，它包含数据流中的初始元素。每次调用 `KthLargest.add`，返回当前数据流中第K大的元素。

##### 示例:
````
int k = 3;
int[] arr = [4,5,8,2];
KthLargest kthLargest = new KthLargest(3, arr);
kthLargest.add(3);   // returns 4
kthLargest.add(5);   // returns 5
kthLargest.add(10);  // returns 5
kthLargest.add(9);   // returns 8
kthLargest.add(4);   // returns 8
````
##### 说明:
你可以假设 `nums` 的长度≥ `k-1` 且`k` ≥ 1。

##### 解题思路：
建立最小堆，长度为k，第k大的元素就是堆顶。

代码实现：
````js
/**
 * @param {number} k
 * @param {number[]} nums
 */
var KthLargest = function(k, nums) {
    this.data = new Heap((x, y) => x < y); // 最小堆 Heap源码在上面
    for(let i = 0, len = nums.length; i < len; i++) {
        if(this.data.length < k) {
            this.data.insert(nums[i]);
        } else {
            // 大于堆顶入堆
            if(nums[i] > this.data.top()) {
                this.data.extract(); // 出堆
                this.data.insert(nums[i]); // 入堆
            } else {
                continue;
            }
        }
    }
};

/** 
 * @param {number} val
 * @return {number}
 */
KthLargest.prototype.add = function(val) {
    if(this.data.top() < val) {
        this.data.extract(); // 出堆
        this.data.insert(val); // 入堆 保持堆的大小为k
    }
    return this.data.top();
};

/**
 * Your KthLargest object will be instantiated and called as such:
 * var obj = new KthLargest(k, nums)
 * var param_1 = obj.add(val)
 */
````

#### 3. 数据流中的中位数
##### 题目描述：
中位数是有序列表中间的数。如果列表长度是偶数，中位数则是中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

* void addNum(int num) - 从数据流中添加一个整数到数据结构中。
* double findMedian() - 返回目前所有元素的中位数。

##### 示例：
````
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3) 
findMedian() -> 2
````
##### 解题思路：
1. 建两个堆，一个最大堆，一个最小堆
2. add添加值到最小堆
3. 中位数: if minHeap.length > maxHeap.length 最小堆的堆顶 else 两个堆的堆顶相加/2

````js
/**
 * initialize your data structure here.
 */
var MedianFinder = function() {
    this.minHeap = new Heap((x, y) => x < y); // 最小堆
    this.maxHeap = new Heap(); // 最大堆
};

/** 
 * @param {number} num
 * @return {void}
 */
MedianFinder.prototype.addNum = function(num) {
    this.minHeap.insert(num);
    this.maxHeap.insert(this.minHeap.extract());
    if(this.minHeap.container.length < this.maxHeap.container.length) {
        this.minHeap.insert(this.maxHeap.extract());
        
    }
};

/**
 * @return {number}
 */
MedianFinder.prototype.findMedian = function() {
    // 两种情况 两者相加是单数 两者长度相差不会超过1
    if(this.minHeap.container.length > this.maxHeap.container.length) {
        return this.minHeap.top();
    } else { // 两者相加是双数
        return (this.minHeap.top() + this.maxHeap.top()) / 2;
    }
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * var obj = new MedianFinder()
 * obj.addNum(num)
 * var param_2 = obj.findMedian()
 */
````