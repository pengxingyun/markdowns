# Javascript数据结构与算法之链表

### 链表的定义:
链表是物理存储单元上非连续的、非顺序的存储结构，由一系列节点组成。

节点包含两部分，一部分是存储数据元素的数据域，一部分是存储指向下一个节点的指针域。如以下定义：
````js
class Node {
    constructor(val) {
        this.val = val;
        this.next = null;
    }
}
````

### 链表的实现：
链表有以下几个方法：
* append， 添加一个新的元素
* insert，在指定位置插入一个元素
* remove，删除指定位置的节点
* indexOf，返回指定元素的索引
* get，返回指定索引位置的元素
* head，返回首节点
* tail，返回尾节点
* length，返回链表长度
* isEmpty，判断链表是否为空
* clear，清空链表
* print，打印整个链表

show me code:

````js
class Node {
    constructor(val) {
        this.val = val;
        this.next = null;
    }
}
class LinkList {
    constructor() {
        this.head = null;
        this.tail = null;
        this.length = 0;
    }
    // 添加一个新的元素
    append(x) {
        let node = new Node(val);
        if(!this.head) {
            this.head = node;
            return;
        }
        this.tail.next = node;
        this.tail = node;
        this.length++;
    }
    // 在指定位置插入一个元素
    insert(index, val) {
        if(index > this.length) {
            return;
        }
        if(index <= 0) {
            this.addAtHead(val);
        } else if(index==this.length) {
            this.addAtTail(val);
        } else {
            let head = this.head;
            while(--index > 0) { // 找前一个元素
                head = head.next;
            }
            let temp = new Node(val);
            temp.next = head.next;
            head.next = temp;
            this.length++;
        }
    }
    // 删除指定位置的节点
    remove(index) {
        if(index < 0 || index > this.length - 1 || !this.length) {
            return null;
        } else {
            if(index === 0) { // 删第一个
                this.head = this.head.next;
            } else {
                let head = this.head;
                let myIndex = index;
                while(--index > 0) {
                    head = head.next;
                }
                if(myIndex==(this.length-1)){
                    this.tail=head;
                }
                head.next=head.next.next;
                this.length--;
            }
        }
    }
    // 返回指定元素的索引
    indexOf(val) {
        let index = 0, head = this.head;
        while(head && head.val !== val) {
            index++;
            head = head.next;
        }
        return head ? index : -1;
    }
    // 返回指定索引位置的元素
    get(index) {
        if(index < 0 || index >= this.length) return -1;
        let head = this.head;
        while(index-- > 0) {
            head = head.next;
        }
        return head ? head.val : head;
    }
    // 返回首节点
    head() {
        return this.head;
    }
    // 返回尾节点
    tail() {
        return this.tail;
    }
    // 返回链表长度
    length() {
        return this.length;
    }
    // 判断链表是否为空
    isEmpty() {
        return this.length === 0;
    }
    // 打印整个链表
    print() {
        let head = this.head;
        while(head) {
            console.log(head.val);
            head = head.next;
        }
    }
}
````
### 队列的应用练习：
leetcode题目练习：

1. [设计链表](https://leetcode-cn.com/problems/design-linked-list/)【简单】
2. [反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)【简单】
3. [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)【简单】

#### 1. 设计链表
##### 题目描述：
设计链表的实现。您可以选择使用单链表或双链表。单链表中的节点应该具有两个属性：`val` 和 `next`。`val` 是当前节点的值，`next` 是指向下一个节点的指针/引用。如果要使用双向链表，则还需要一个属性 `prev` 以指示链表中的上一个节点。假设链表中的所有节点都是 0-index 的。

在链表类中实现这些功能：

* get(index)：获取链表中第 index 个节点的值。如果索引无效，则返回-1。
* addAtHead(val)：在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。
* addAtTail(val)：将值为 val 的节点追加到链表的最后一个元素。
* addAtIndex(index,val)：在链表中的第 index 个节点之前添加值为 val  的节点。如果 index 等于链表的长度，则该节点将附加到链表的末尾。如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。
* deleteAtIndex(index)：如果索引 index 有效，则删除链表中的第 index 个节点。
 

##### 示例：
````
MyLinkedList linkedList = new MyLinkedList();
linkedList.addAtHead(1);
linkedList.addAtTail(3);
linkedList.addAtIndex(1,2);   //链表变为1-> 2-> 3
linkedList.get(1);            //返回2
linkedList.deleteAtIndex(1);  //现在链表是1-> 3
linkedList.get(1);            //返回3
````

##### 解题思路：

代码实现：

````js
class Node  {
    constructor(val) {
        this.val = val;
        this.next = null;
    }
}
/**
 * Initialize your data structure here.
 */
var MyLinkedList = function() {
    this.head = null;
    this.tail = null;
    this.length = 0;
};

/**
 * Get the value of the index-th node in the linked list. If the index is invalid, return -1. 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function(index) {
    if(index < 0 || index >= this.length) return -1;
    let head = this.head;
    while(index-- > 0) {
        head = head.next;
    }
    return head ? head.val : head;
};

/**
 * Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function(val) {
    let first = new Node(val);
    if(this.head===null) {
        this.tail = first;
    } else {
        first.next = this.head;
    }
    this.head = first;
    this.length++;
    
};

/**
 * Append a node of value val to the last element of the linked list. 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function(val) {
    let node = new Node(val);
    if(!this.head) {
        this.head = node;
        return;
    }
    this.tail.next = node;
    this.tail = node;
    this.length++;
};

/**
 * Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function(index, val) {
    if(index > this.length) {
        return;
    }
    if(index <= 0) {
        this.addAtHead(val);
    } else if(index==this.length) {
        this.addAtTail(val);
    } else {
        let head = this.head;
        while(--index > 0) { // 找前一个元素
            head = head.next;
        }
        let temp = new Node(val);
        temp.next = head.next;
        head.next = temp;
        this.length++;
    }
    
};

/**
 * Delete the index-th node in the linked list, if the index is valid. 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function(index) {
    if(index < 0 || index > this.length - 1 || !this.length) {
        return null;
    } else {
        if(index === 0) { // 删第一个
            this.head = this.head.next;
        } else {
            let head = this.head;
            let myIndex = index;
            while(--index > 0) {
                head = head.next;
            }
            if(myIndex==(this.length-1)){
                this.tail=head;
            }
            head.next=head.next.next;
            this.length--;
        }
    }
};

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * var obj = new MyLinkedList()
 * var param_1 = obj.get(index)
 * obj.addAtHead(val)
 * obj.addAtTail(val)
 * obj.addAtIndex(index,val)
 * obj.deleteAtIndex(index)
 */
````

#### 2. 反转链表
##### 题目描述：
反转一个单链表。

##### 示例:
````
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
进阶:
你可以迭代或递归地反转链表。你能否用两种方法解决这道题？
````

##### 解题思路：
1. 迭代 
    1. 建虚拟头节点prev
    2. 双指针prev指向上一节点, curr指向当前节点
    3. 循环到curr=null结束, 返回prev

    代码实现：
    ````js
    /**
     * Definition for singly-linked list.
     * function ListNode(val) {
     *     this.val = val;
     *     this.next = null;
     * }
     */
    /**
     * @param {ListNode} head
     * @return {ListNode}
     */
    var reverseList = function(head) {
        if(!head) return head;
        let prev = null, curr = head;
        while(curr) {
            let next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
    ````

2. 递归

    ````js
    /**
     * Definition for singly-linked list.
     * function ListNode(val) {
     *     this.val = val;
     *     this.next = null;
     * }
     */
    /**
     * @param {ListNode} head
     * @return {ListNode}
     */
    var reverseList = function(head) {
        if(head === null || head.next === null) {
            return head;
        }
        let next_node = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return next_node;
    }
    ````

#### 3. 合并两个有序链表
##### 题目描述：
将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

##### 示例：
````
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
````
##### 解题思路：
1. 建虚拟头节点curr
2. 比较两节点值，获取小值加入新节点，同时当前指针下移
3. 循环至一个链表指针到底，curr.next再挂到未循环完的链表上
````js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
    // 迭代
    if(l1 === null) {
        return l2;
    }
    if(l2 === null) {
        return l1;
    }
    let first = new ListNode(null);
    let curr = first;
    
    while(l1 && l2) {
        if(l1.val > l2.val) {
            curr.next = l2;
            l2 = l2.next;
        } else {
            curr.next = l1;
            l1 = l1.next;
        }
        curr = curr.next;
    }
    // 这里还得扣上未循环完的链表节点
    if(l1) curr.next = l1;
    if(l2) curr.next = l2;
    return first.next;
}
````