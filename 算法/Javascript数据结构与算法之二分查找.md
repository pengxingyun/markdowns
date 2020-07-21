# # Javascript数据结构与算法之二分查找

### 二分查找的定义:
如果你要查找的数据是有序的，二分查找算法比顺序查找算法更高效。

要理解二分查找算 法的原理，可以想象一下你在玩一个猜数字游戏，这个数字位于 1~100 之间，而要猜的数 字是由你的朋友来选定的。游戏规则是，你每猜一个数字，你的朋友将会做出以下三种回 应中的一种:<br/>
(1) 猜对了; <br/>
(2) 猜大了; <br/>
(3) 猜小了。<br/>
根据以上规则，第一次猜 50 将会是最佳策略。如果猜的值太大，就猜 25。如果太小，就 应该猜 75。每一次猜测，都应该选择当前最小值和最大值的中间点(取决于你上次猜测 的结果是太大还是太小)。然后将这个中间值作为下次要猜的数字。只要你采用这个策略， 就可以用最少的次数猜出这个数字。

### 二分查找的应用练习：
leetcode题目练习：

1. [猜数字大小](https://leetcode-cn.com/problems/guess-number-higher-or-lower/)【简单】
2. [第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)【简单】
3. [在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)【中等】

#### 1. 猜数字大小
##### 题目描述：
我们正在玩一个猜数字游戏。 游戏规则如下：
我从 **1** 到 *n* 选择一个数字。 你需要猜我选择了哪个数字。
每次你猜错了，我会告诉你这个数字是大了还是小了。
你调用一个预先定义好的接口 `guess(int num)`，它会返回 3 个可能的结果（`-1`，`1` 或 `0`）：
````
-1 : 我的数字比较小
 1 : 我的数字比较大
 0 : 恭喜！你猜对了！
````

##### 示例 :
````
输入: n = 10, pick = 6
输出: 6
````

##### 解题思路：
二分查找

代码实现：
````js
/** 
 * Forward declaration of guess API.
 * @param {number} num   your guess
 * @return 	            -1 if num is lower than the guess number
 *			             1 if num is higher than the guess number
 *                       otherwise return 0
 * var guess = function(num) {}
 */

/**
 * @param {number} n
 * @return {number}
 */
var guessNumber = function(n) {
    let l = 0, r = n;
    while(l <= r) {
        let m = l + ((r - l) >> 1);
        if(guess(m) === -1) {
            r = m - 1;
        } else if(guess(m) === 1) {
            l = m + 1;
        } else {
            return m;
        }
    }
};
````

#### 2. 第一个错误的版本
##### 题目描述：
你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 `n` 个版本 `[1, 2, ..., n]`，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用 `bool isBadVersion(version)` 接口来判断版本号 `version` 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

##### 示例:
````
给定 n = 5，并且 version = 4 是第一个错误的版本。

调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true

所以，4 是第一个错误的版本。 
````
##### 解题思路：
判断isBadVersion()

true: r = m;

false: l = m + 1;

代码实现：
````js
/**
 * Definition for isBadVersion()
 * 
 * @param {integer} version number
 * @return {boolean} whether the version is bad
 * isBadVersion = function(version) {
 *     ...
 * };
 */

/**
 * @param {function} isBadVersion()
 * @return {function}
 */
var solution = function(isBadVersion) {
    /**
     * @param {integer} n Total versions
     * @return {integer} The first bad version
     */
    return function(n) {
        let l = 1, r = n;
        while(l < r) {
            let m = l + ((r - l) >> 1);
            if(isBadVersion(m)) {
                r = m;
            } else {
                l = m + 1;
            }
        }
        return l;
    };
};
````

#### 3. 在排序数组中查找元素的第一个和最后一个位置
##### 题目描述：
给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 *O(log n)* 级别。

如果数组中不存在目标值，返回 `[-1, -1]`。

##### 示例 1:
````
输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
````
##### 示例 2:
````
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
````

##### 解题思路：

代码实现：
````js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function(nums, target) {
     let res = [-1, -1]

     let compute = (nums,target, left = true) => {
         let l = 0, r = nums.length
         
         while(l < r) {
            
             let mid = l + ((r - l) >> 1);
             if(nums[mid] > target || (left && nums[mid] == target)) {
                 r = mid
             } else {
                 l = mid + 1    
             }
         }

         return l 
     }

     let leftinx = compute(nums, target)
     
     if(leftinx === nums.length || nums[leftinx] !== target) return res // target太大，做边界一直右移到末端也没值；target太小，右边界左移到第一个元素也没找到；

     res[0] = leftinx;
     res[1] = compute(nums, target, false) - 1

     return res
};
````