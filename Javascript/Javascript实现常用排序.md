# Javascript实现常用排序
### 冒泡排序
原理：每次循环只比较相邻两个数，把较小的值移到后面。
````js
function bubbleSort(arr) {
    for(let i = 0, len = arr.length; i < len; i++){
        for(let j = 0; j < len - i - 1; j++) { // 控制最后一个索引len - i - 1
            if(arr[j] < arr[j+1]) { // 每次循环都把当前最小值移到后面
                [arr[j], arr[j+1]] = [arr[j+1], arr[j]];
            }
        }
    }
    return arr;
}
````
#### 时间复杂度分析：
* 时间复杂度**O(n<sup>2</sup>)**：其中`n`是数组长度。最优情况是**O(n)**，最坏情况是**O(n<sup>2</sup>)**。
* 空间复杂度**O(1)**：在原数组上操作。

### 选择排序
原理：往后查找，找到当前最大值放到当前位置。
````js
function selectSort(arr) {
   for(let i = 0, len = arr.length; i < len - 1; i++){
        let index = i;
        for(let j = i; j < len; j++) {
            if(arr[j] < arr[index]) {
                index = j;
            }
        }
        [arr[index], arr[i]] = [arr[i], arr[index]];
    }
    return arr;
}
````
#### 时间复杂度分析：
* 时间复杂度**O(n<sup>2</sup>)**：其中`n`是数组长度。最优情况是**O(n<sup>2</sup>)**，最坏情况是**O(n<sup>2</sup>)**。
* 空间复杂度**O(1)**：在原数组上操作。

### 插入排序
原理：每次找到现在下标以前的插入位置。
````js
function insertSort(arr) {
    for(let i = 1, len = arr.length; i < len; i++) {
        let prev_index = i - 1;
        let current = arr[i];
        while(prev_index >= 0 && arr[prev_index] < current) { // 当前值与前面下标值比较
            arr[prev_index + 1] = arr[prev_index]; // 每次往后移
            prev_index--;
        }
        arr[prev_index + 1] = current; // arr[prev_index + 1]是undefined
    }
    return arr;
}
````
#### 时间复杂度分析：
* 时间复杂度**O(n<sup>2</sup>)**：其中`n`是数组长度。最优情况是**O(n)**，最坏情况是**O(n<sup>2</sup>)**。
* 空间复杂度**O(1)**：在原数组上操作。

### 归并排序
原理：归并排序也叫分治排序，把一个大问题转化分解成若干个子问题，当每个子问题都解决了以后，大的问题也就随之解决。

对两个有序数组进行合并
````js
/**
* 合并两个有序数组
* @param {array} arr1
* @param {array} arr2
*/
function mergeArray(arr1, arr2) {
    let merge_arr = [];
    let index_1 = 0, len1 = arr1.length;
    let index_2 = 0, len2 = arr2.length;
    while(index_1 < len1 && index_2 < len2) {
        // 哪个数组的头部元素小,就合并谁,然后更新头的位置
        if(arr1[index_1]<=arr2[index_2]){
            merge_arr.push(arr1[index_1]);
            index_1++;
        }else{
            merge_arr.push(arr2[index_2]);
            index_2++;
        }
    }
    // 第一个数组没比较完
    if(index_1 < len1) {
        merge_arr.push(...arr1.slice(index_1));
    }
    // 第二个数组没比较完
    if(index_2 < len2) {
        merge_arr.push(...arr2.slice(index_2));
    }
    return merge_arr;
}
````

````js
/**
* 归并排序
*/
function mergeSort(arr, start = 0, end = arr.length - 1) {
    if(!arr.length) return [];
    // 只要2个以上元素就继续分
    if(start < end) {
        let middle = Math.floor((start + end) / 2);
        return mergeArray(mergeSort(arr, start, middle), mergeSort(arr, middle+1, end))
    }
    return [arr[end]]
}
````
#### 时间复杂度分析：
* 时间复杂度**O(n log n)**：其中`n`是数组长度。最优情况是**O(n log n)**，最坏情况是**O(n log n)**。
* 空间复杂度**O(n)**。

### 快速排序
原理：
(1) 选择一个基准元素，将列表分隔成两个子序列;
(2) 对列表重新排序，将所有小于基准值的元素放在基准值的前面，所有大于基准值的元
素放在基准值的后面;
(3) 分别对较小元素的子序列和较大元素的子序列重复步骤 1 和 2。
````js
function quickSort(arr) {
    if(!arr.length) return [];
    let left = [], right = [], middle = arr[0];
    for(let i = 1, len = arr.length; i < len; i++) {
        if(arr[i] < middle) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }
    return [...left, middle, ...right];
}
````
#### 时间复杂度分析：
* 时间复杂度**O(n log n)**：其中`n`是数组长度。最优情况是**O(n log n)**，最坏情况是**O(n<sup>2</sup>)**。
* 空间复杂度**O(log n)**。

### 计数排序
这种排序适用于在知道最大值且又有大量重复数据的情况。
````js
function countingSort(arr, maxValue){
    let bucket = new Array(maxValue + 1).fill(0), ret = [];
    for(let i = 0, len = arr.length; i < len; i++) {
        bucket[arr[i]]++;
    }
    for(let i = 0; i < maxValue + 1; i++) {
        ret.push(...new Array(bucket[i]).fill(i));
    }
    return ret;
}

countingSort([1,1,1,1,1,6,6,6,6,0,9,4,5,4,7,8], 8)
// [0, 1, 1, 1, 1, 1, 4, 4, 5, 6, 6, 6, 6, 7, 8]
````

#### 时间复杂度分析：
* 时间复杂度**O(n + k)**：其中`n`是数组长度， k是`桶`的个数。最优情况是**O(n + k)**，最坏情况是**O(n + k)**。
* 空间复杂度**O(k)**：k是`桶`的个数。