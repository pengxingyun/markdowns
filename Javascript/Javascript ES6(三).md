# Jascript ES6（三）
### JS的类
**ES6** 的类起初是作为 **ES5** 传统继承模型的语法糖，但添加了许多特性来减少错误。
ES5的实现类：
````js
function Person(name) {
    this.name = name;
}
// 实例方法
Person.prototype.sayName = function() {
    console.log(this.name);
}
// 静态方法
Person.create = function(name) {
    return new Person(name);
}

// 派生类继承
function Programmer(name, type) {
    Person.call(this, name);
    this.type = type;
}
Programmer.prototype = Object.create(Person.prototype, {
    constructor : {
        value: Programmer
    }
});
// 重写并屏蔽Person.prototype.sayName
Programmer.prototype.sayName = function() {
    console.log('re---', this.name);
}
Programmer.prototype.showType = function() {
    console.log(this.type);
}
````
ES6的实现类：
````js
class Person {
    constructor(name) {
        this.name = name;
    }
    // 实例方法
    sayName() {
        console.log(this.name);
    }
    // 静态方法
    static create(name) {
        return new Person(name);
    }
}

// 派生类继承
class Programmer extends Person {
    constructor(name, type) {
        super(name);
        this.type = type;
    }
    showType() {
        console.log(this.type);
    }
    // 重写并屏蔽Person.prototype.sayName
    sayName() {
        console.log('re---', this.name);
    }
}
````
以上代码实现的效果是一致的，测试代码：
````js
let person = new Person('tom');
person.sayName(); // tom
Person.create('jerry'); // Person {name: jerry}
let h5er = new Programmer('fn', 'h5');
h5er.sayName(); // re---fn
h5er.showType(); // h5
````

#### 为何要使用类的语法
尽管类与自定义类型之间有相似性，但仍然要记住一些重要的区别：
1. 类声明不会被提升，这与函数定义不同。类声明的行为与 `let` 相似，因此在程序的执行到达声明处之前，类会存在于暂时性死区内。
2. 类声明中的所有代码会自动运行在严格模式下，并且也无法退出严格模式。
3. 类的所有方法都是不可枚举的，这是对于自定义类型的显著变化，后者必须用`Object.defineProperty()` 才能将方法改变为不可枚举。
4. 类的所有方法内部都没有 `[[Construct]]` ，因此使用 `new` 来调用它们会抛出错误。
5. 调用类构造器时不使用 `new` ，会抛出错误。
6. 试图在类的方法内部重写类名，会抛出错误。（不能在内部给类重新复制， 外部可以）
7. **es5**继承无法继承静态方法。

### 增强的数组功能
#### Array.of
````js
// 参数里面传入的数据都会变成arr的元素
let arr = Array.of(1) // [1] 不同的是new Array(1)会创建一个长度为1的数组 [empty]
````
#### Array.from
`Array.from`作用是将类数组对象转换为数组
````js
let obj = {
    '0': 0,
    '1': 1,
    length: 2 
} // {0: 0, 1: 1, length: 2}
let arr = Array.from(obj) // [0, 1]
````
`Array.from`可以接受函数实行进一步的数组转换。
````js
function translate() {
    return Array.from(arguments, (value) => value + 1);
}
let numbers = translate(1, 2, 3);
console.log(numbers); // 2,3,4
````
#### find, findIndex
````js
let numbers = [25, 30, 35, 40, 45];
console.log(numbers.find(n => n > 33)); // 35 找第一个>33的值
console.log(numbers.findIndex(n => n > 33)); // 2 找第一个>33的下标
````
`find()` 与 `findIndex()` 方法在查找满足特定条件的数组元素时非常有用。但若想查找特定
值，则使用 `indexOf()` 与 `lastIndexOf()` 方法会是更好的选择。

#### fill
`fill()` 方法能使用特定值填充数组中的一个或多个元素。

全部填充
````js
let numbers = [1, 2, 3, 4];
numbers.fill(1);
console.log(numbers.toString()); // 1,1,1,1
````
部分填充
````js
let numbers = [1, 2, 3, 4];
numbers.fill(1, 2);
console.log(numbers.toString()); // 1,2,1,1
numbers.fill(0, 1, 3);
console.log(numbers.toString()); // 1,0,0,1
````
#### copyWithin
`copyWithin()` 方法与 `fill()` 类似，可以一次性修改数组的多个元素。
````js
let numbers = [1, 2, 3, 4];
// 从索引 2 的位置开始粘贴
// 从数组索引 0 的位置开始复制数据
numbers.copyWithin(2, 0);
// 从索引 2 的位置开始粘贴
// 从数组索引 0 的位置开始复制数据
// 在遇到索引 1 时停止复制
// numbers.copyWithin(2, 0, 1);
console.log(numbers.toString()); // 1,2,1,2
````

### Promise
#### 创建未决的Promise
````js
// Node.js 范例
let fs = require("fs");
function readFile(filename) {
    return new Promise(function(resolve, reject) {
        // 触发异步操作
        fs.readFile(filename, { encoding: "utf8" }, function(err, contents) {
            // 检查错误
            if (err) {
                reject(err);
                return;
            }
            // 读取成功
            resolve(contents);
        });
    });
}
let promise = readFile("example.txt");
// 同时监听完成与拒绝
promise.then(function(contents) {
    // 完成
    console.log(contents);
}, function(err) {
    // 拒绝
    console.error(err.message);
});

// 上面第二种写法
// promise.then(function(contents) {
//     // 完成
//     console.log(contents);
// }).catch(function(err) {
//     // 拒绝
//     console.error(err.message);
// })
````
#### 创建已决的Promise
````js
let p1 = Promise.resolve(42);
let p2 = Promise.reject(43);

p1.then(res => {
    console.log(res); // 42
})
p2.catch(err => {
    console.log(err); // 43
})
````

#### 非 Promise 的 Thenable
当一个对象拥有一个能接受 `resolve` 与 `reject` 参数的 `then()` 方法，该对象就会被认为是一个非 Promise 的 thenable。
````js
let thenable = {
    then: function(resolve, reject) {
        resolve(42);
    }
};
let p1 = Promise.resolve(thenable);
p1.then(function(value) {
    console.log(value); // 42
});
````
#### 串联的Promise
````js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});
p1.then(res => {
    console.log(res); // 42
    return res;
}).then(res => {
    console.log('finish'); // 上面就算不返回这个也能执行
    console.log(res); // 42
})
````

#### Promise.all
执行多个promise以数组形式返回值
````js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});
let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});
let p3 = new Promise(function(resolve, reject) {
    resolve(44);
});
let p4 = Promise.all([p1, p2, p3]);
p4.then(function(value) {
    console.log(Array.isArray(value)); // true
    console.log(value[0]); // 42
    console.log(value[1]); // 43
    console.log(value[2]); // 44
});
````
如果有一个错误，后面的将不会执行
````js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});
let p2 = new Promise(function(resolve, reject) {
    reject(43);
});
let p3 = new Promise(function(resolve, reject) {
    resolve(44);
});
let p4 = Promise.all([p1, p2, p3]);
p4.catch(function(value) {
    console.log(Array.isArray(value)) // false
    console.log(value); // 43
});
````
#### Promise.race
多个promise返回最先返回的值
````js
let p1 = Promise.resolve(42);
let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});
let p3 = new Promise(function(resolve, reject) {
    resolve(44);
});
let p4 = Promise.race([p1, p2, p3]);
p4.then(function(value) {
    console.log(value); // 42
});
````
如果最先返回的是拒绝的值
````js
let p1 = new Promise(function(resolve, reject) {
    setTimeout(() => {
        resolve(42);
    })
});
let p2 = Promise.reject(43);
let p3 = new Promise(function(resolve, reject) {
    setTimeout(() => {
        resolve(44);
    })
});
let p4 = Promise.race([p1, p2, p3]);
p4.then(function(res) {
    console.log(res); // 未返回
}).catch(function(value) {
    console.log(value); // 43
});
````
p2是最先reject回来的，所以即使p1和p2执行完结果也会被忽略。

### Proxy和Reflect
`Proxy` 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。
下面是 Proxy 支持的拦截操作一览，一共 13 种。

* **get(target, propKey, receiver)**：拦截对象属性的读取，比如 `proxy.foo` 和 `proxy['foo']` 。
* **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v` 或 `proxy['foo'] = v`，返回一个布尔值。
* **has(target, propKey)**：拦截 `propKey in proxy` 的操作，返回一个布尔值。
* **deleteProperty(target, propKey)**：拦截 `delete proxy[propKey]` 的操作，返回一个布尔值。
* **ownKeys(target)**：拦截 `Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in` 循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()` 的返回结果仅包括目标对象自身的可遍历属性。
* **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
* **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc)`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
* **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
* **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。
* **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
* **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
* **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。
* **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。

````js
let proxy = new Proxy({}, {
    /**
    * 获取proxy.xxx 如果xxx不存在throw TypeError
    * @param {Object} target 将会被读取属性的对象（即代理的目标对象）
    * @param {String} propKey 需要读取的属性的键
    * @param {Object} receiver 操作发生的对象（通常是代理对象）
    */
    get(target, propKey, receiver) {
        if(!(propKey in receiver)) {
            throw new TypeError("Property " + propKey + " doesn't exist.");
        }
        return Reflect.get(target, propKey, receiver);
    },
    /**
    * 设置proxy.xxx
    * @param {Object} target 将会被读取属性的对象（即代理的目标对象）
    * @param {String} propKey 需要读取的属性的键
    * @param {String} value 将被写入属性的值
    * @param {Object} receiver 操作发生的对象（通常是代理对象）
    */
    set(target, propKey, value, receiver) {
        console.log('set---', propKey, '----', value);
        // 设置属性
        return Reflect.set(target, propKey, value, receiver);
    },
    /**
    * 函数会在使用 in 运算符的情况下被调用
    * @param {Object} target 将会被读取属性的对象（即代理的目标对象）
    * @param {String} propKey 需要检查的属性的键
    */
    has(target, propKey) {
        if(target.hasOwnProperty(propKey)) {
            return Reflect.has(target, propKey);
        } else {
            return false;
        }
    },
    /**
    * delete proxy[propKey]会触发
    * @param {Object} target 需要删除属性的对象
    * @param {String} propKey 需要删除的属性的键
    */
    deleteProperty(target, propKey) {
        if(target.hasOwnProperty(propKey)) {
            console.log('delete----', propKey);
            return Reflect.deleteProperty(target, propKey);
        } else {
            return false;
        }
    },
    /**
    * 拦截 `Object.getOwnPropertyNames(proxy)`、
    * `Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、
    * `for...in` 循环，返回一个数组。
    * 该方法返回目标对象所有自身的属性的属性名，
    * 而`Object.keys()` 的返回结果仅包括目标对象自身的可遍历属性。
    * @param {Object} target 需要删除属性的对象
    */
    ownKeys(target) {
        // 返回不以_开头的键
        return Reflect.ownKeys(target).filter(key => {
            return typeof key !== "string" || key[0] !== "_";
        });
    },
    getOwnPropertyDescriptor(target, PropKey) {
        console.log('getOwnPropertyDescriptor...');
        return Reflect.getOwnPropertyDescriptor(target, PropKey);
    },
    defineProperty(target, key, descriptor) {
        console.log('defineProperty...');
        return Reflect.defineProperty(target, key, descriptor);
    },
    isExtensible(target) {
        console.log('isExtensible...');
        return Reflect.isExtensible(target);
    },
    preventExtensions(target) {
        console.log('preventExtensions...');
        return Reflect.preventExtensions(target);
    },
    getPrototypeOf(target) {
        console.log('getPrototypeOf...');
        return null;
    },
    setPrototypeOf(target, proto) {
        console.log('setPrototypeOf...');
        return false;
    }
})
// 测试代码
proxy.name = 'abc'
// set--- name ---- abc
// getOwnPropertyDescriptor...
// defineProperty...
proxy.name
// "abc"
Object.getPrototypeOf(proxy)
// getPrototypeOf...
proxy._name = 'aaa'
// set--- _name ---- abc
// getOwnPropertyDescriptor...
// defineProperty...
Reflect.ownKeys(proxy)
// ["name"] _name未打印
````

对函数进行拦截
````js
let proxy = new Proxy(function(){}, {
    apply(target, thisArg, argumentList) {
        console.log('fn run....apply');
        return Reflect.apply(target, thisArg, argumentList);
    },
    construct(target, argumentList) {
        console.log('new Class----construct');
        return Reflect.construct(target, argumentList);
    }
})

new proxy() // new Class----construct
proxy() // fn run....apply
````

#### 可被撤销的代理
````js
let target = {
    name: "target"
};
let { proxy, revoke } = Proxy.revocable(target, {});
console.log(proxy.name); // "target"
revoke();
// 抛出错误
console.log(proxy.name);
````

### 模块封装
./example.js
````js
// 导出数据
export var color = "red";
export let name = "Nicholas";
export const magicNumber = 7;
// 导出函数
export function sum(num1, num2) {
return num1 + num1;
}
// 导出类
export class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }
}
// 此函数为模块私有
function subtract(num1, num2) {
    return num1 - num2;
}
// 定义一个函数……
function multiply(num1, num2) {
    return num1 * num2;
}
// ……稍后将其导出
export { multiply };
````

./index.js 导出./example.js export内容 两种写法

````js
// （一）完全导入一个模块
import * as m from './example.js';

// 2 "red" Rectangle {length: 3, width: 4}
console.log(m.multiply(1, 2), m.color, new m.Rectangle(3, 4));

// （二）导入多个绑定
import {multiply, color, Rectangle} from './example.js';

// 2 "red" Rectangle {length: 3, width: 4}
console.log(multiply(1, 2), color, new Rectangle(3, 4));
````

#### 重命名导出与导入
导出前重命名 ./example.js
````js
function sum(num1, num2) {
    return num1 + num2;
}
export { sum as add }; // 导出sum为add 相当于 export {add: sum}
````
导入重命名 ./index.js
````js
import { add as sum } from "./example.js";
````
#### 模块的默认值
./example.js
````js
export let color = "red"; // 单独绑定导出模块
export default function(num1, num2) { // 默认导出模块
    return num1 + num2;
}
````
./index.js
````js
import sum, { color } from "./example.js"; // 默认模块、单独模块一起导出
// 等价于上一句
// import { default as sum, color } from "./example.js";
console.log(sum(1, 2)); // 3
console.log(color); // "red"
````

由于模块必须用与脚本不同的方式运行，浏览器就引入了 `<script type="module">` ，以表示资源文件或内联代码需要作为模块来执行。

./index.html
````js
<script type="module" src="./index.js"></script>
````