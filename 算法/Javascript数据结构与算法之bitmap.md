# Javascript数据结构与算法之bitmap

#### 一个小问题:
已知有n个整数，这些整数的范围是[0, 100]，请你设计一种数据结构，使用数组存储这些数据，并提供两种方法，分别是addMember 和 isExist。

* addMember 加入一个整数 member
* isExist 判断member是否存在

##### 简单实现：
````js
class Data {
    constructor() {
        this.data = [];
    }
    // 加入一个整数 member
    addMember(member) {
        this.data.push(member);
    }
    // 判断member是否存在
    isExist(member) {
        return !!~this.data.indexOf(member); // ~作用就是把-1变成0, !!转成boolean类型
    }
}

let data = new Data();
data.addMember(3);
data.addMember(5);
data.addMember(9);
data.isExist(4); // false
data.isExist(5); // true
````
#### 更好的实现：
上面的isExist调用，每次都是O(n)的时间复杂度。是否还可以实现更少的时间复杂度？

由于题意说明这些数在100以内， 我们可以定义一个100长度的数组存储输入的项，这样就可以实现O(1)的时间复杂度。

show me code:
````js
class Data {
    constructor(size) {
        this.data = new Array(size);
    }
    // 加入一个整数 member
    addMember(member) {
        this.data[member] = 1; // 根据索引位置1还是undefined判断当前元素是否存在
    }
    // 判断member是否存在
    isExist(member) {
        return !!this.data[member]; // !!转成boolean类型
    }
}

let data = new Data();
data.addMember(3);
data.addMember(5);
data.addMember(9);
data.isExist(4); // false
data.isExist(5); // true
````

#### 更节省空间的算法
上面实现的算法，已经很快，但是却面临一个新的问题，如果数据非常多，多达1个亿，每个整数是4个字节，那么一亿个整数数就是4亿个字节，1024字节是1kb，1024kb是1M,4亿个字节就381M的内存空间。

如果没有这么多内存该怎么办？我们需要一种数据压缩的算法，用很少的空间来表示这一亿个数的存在与否。这就会用到bitmap了。

* 数据范围是0到100，那么只需要4个整数就可以表示4*32个数的存在与否，创建一个大小为4的数组
* 执行addMember时，先用member/32，确定member在数组里的索引（arr_index），然后用member%32，确定在整数的哪个二进制位行操作(bit_index)，最后执行bit_arr[arr_index] = bit_arr[arr_index] | 1<<bit_index;

* 执行isExist时，先用member/32，确定member在数组里的索引（arr_index），然后用member%32，确定在整数的哪个二进制位行操作(bit_index)，最后执行bit_arr[arr_index] & 1<<bit_index，如果结果不为0，就说明memeber存在


````js
class Bitmap {
    constructor(size) {
        this.bit_arr = new Array(size).fill(0);
    }
    addMember(member) {
        // 确定member在数组里的索引
        let arr_index = Math.floor(member / 32); // 向上取整
        let bit_index = member % 32; // 确定在整数的哪个二进制位行操作
        this.bit_arr[arr_index] = this.bit_arr[arr_index] | 1<<bit_index; // 赋值
    }
    isExist(member) {
        // 确定member在数组里的索引
        let arr_index = Math.floor(member / 32); // 向上取整
        let bit_index = member % 32; // 确定在整数的哪个二进制位行操作
        return !!(this.bit_arr[arr_index] & 1<<bit_index); // 获取是否存在item
    }
}

let bitmap = new Bitmap(4);
bitmap.addMember(3); // [8, 0, 0, 0]
bitmap.addMember(34); // [8, 4, 0, 0]
bitmap.addMember(64); // [8, 4, 1, 0]
bitmap.isExist(34); // true
bitmap.isExist(35); // false
````

这种数据结构基于位做映射，能够用很少的内存存储数据，和数组不同，它只能存储表示某个数是否存在，可以用于大数据去重，大数据排序，两个集合取交集。

#### bitmap局限
BitMap在处理大数据时才有优势，而且要求数据集紧凑，如果要处理的数只有3个，1， 1000， 100000，那么空间利用率太低了，最大的值决定了BitMap要用多少内存。