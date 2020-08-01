# Javascript装饰器模式
装饰器模式：在不改变原对象的基础上，通过对其进行包装拓展（添加属性或者方法）使原有对象可以满足用户的更复杂需求。

### 实现
````js
function fn1() {
    console.log('fn1 by call');
}

function fn2() {
    console.log('fn2 by call');
}

function fn3() {
    console.log('fn3 by call');
}
````

现在需要一个对调用方法名字的打印。

````js
function fn1() {
    console.log(fn1.name);
    console.log('fn1 by call');
}

function fn2() {
    console.log(fn2.name);
    console.log('fn2 by call');
}

function fn3() {
    console.log(fn3.name);
    console.log('fn3 by call');
}

f1();
// fn1
// fn1 by call 
f2();
// fn2
// fn2 by call 
f3();
// fn3
// fn3 by call 
````

更简单的写法：

````js
// 装饰器：方法执行前包装一层
function decorator(fn) {
    console.log(fn.name);
    fn();
}

decorator(fn1);
decorator(fn2);
decorator(fn3);
// fn1
// fn1 by call
// fn2
// fn2 by call
// fn3
// fn3 by call
````

这是一种可以在不了解原有功能的基础上对功能拓展模式，这是对原有功能的一种增强与拓展。

### 装饰类

````js
class MyClass {

}
/**
* 对target添加一个静态属性
* @param {class} 需要操作的类
*/
function testable(target) {
    target.isTestable = true;
}

testable(MyClass);
console.log(MyClass.isTestable); // true
````

### 装饰类的属性

````js
class MyClass {
    constructor(name) {
        this.name = name;
    }
    getName() {
        return this.name;
    }
}
function readonly(target, name, descriptor = {}){
    descriptor.writable = false;
    Object.defineProperty(target, name, descriptor);
}

let cls = new MyClass('abc'); // abc
// 定义属性只读
readonly(MyClass.prototype, 'name'); 
let cls = new MyClass('bca'); // 抛出错误 this.name不可修改
````