# Javascript ES6（二）

### 解构：更方便的数据访问
* 当使用解构来配合 `var` 、 `let` 或 `const` 来声明变量时，必须提供初始化器（即等号右边的值）。
* 当解构赋值表达式的右侧（ `=` 后面的表达式）的计算结果为 `null` 或 `undefined` 时，会抛出错误。因为任何读取 `null` 或 `undefined` 的企图都会导致“运行时”错误（runtime error ）。

#### 对象结构
##### 解构赋值
对象解构语法在赋值语句的左侧使用了对象字面量.
````js
let node = {
    type: "Identifier",
    name: "foo"
};
let { type, name } = node;
console.log(type); // "Identifier"
console.log(name); // "foo"
````
非初始化结构
````js
// 使用解构来分配不同的值
({ type, name } = node);
````
注意你必须用圆括号包裹解构赋值语句，这是因为暴露的花括号会被解析为代码块语句，而块语句不允许在赋值操作符（即等号）左侧出现。

传递值给函数的同时赋值
````js
let node = {
        type: "Identifier",
        name: "foo"
    },
    type = "Literal",
    name = 5;
function outputInfo(value) {
    console.log(value === node); // true
}
outputInfo({ type, name } = node);
console.log(type); // "Identifier"
console.log(name); // "foo"
````
把 `node` 传递进了 `outputInfo`，且 `type`、`name` 完成了赋值。

##### 默认值
````js
let { type, name, value = true } = node;
````
在 `node.value` 缺失、或者为 `undefined` 时，会使用默认值。

##### 赋值给不同的本地变量名

````js
// 相当于：let localType = node.type, localName = node.name;
let { type: localType, name: localName } = node;
````

##### 赋值给不同的本地变量名 + 默认值

````js
// 相当于：let localType = node.type, localName = node.name || "bar";
let { type: localType, name: localName = "bar" } = node;
````

##### 嵌套的对象解构
````js
// 提取 node.loc.start赋值到localStart
let { loc: { start: localStart }} = node;
````

#### 数组解构
数组解构时，解构作用在数组内部的位置上，而不是作用在对象的具名属性上。
````js
// 相当于 let firstColor = colors[0], secondColor = colors[1];
let [ firstColor, secondColor ] = colors;
````
你也可以在解构模式中忽略一些项，并且只给感兴趣的项提供变量名。
````js
// 相当于 let thirdColor = colors[2];
let [ , , thirdColor ] = colors;
````
数组解构赋值有一个非常独特的用例，能轻易地互换两个变量的值。
````js
[a, b] = [b, a];
````
##### 剩余项
````js
// let firstColor = colors[0], restColors = colors.slice(1);
let [ firstColor, ...restColors ] = colors;
````

##### 克隆数组
ES5
````js
// 在 ES5 中克隆数组
var colors = [ "red", "green", "blue" ];
var clonedColors = colors.concat();
console.log(clonedColors); //"[red,green,blue]"
````
ES6
````js
// 在 ES6 中克隆数组
var colors = [ "red", "green", "blue" ];
var [...clonedColors] = colors;
console.log(clonedColors); //"[red,green,blue]"
````

#### 参数结构
````js
function setCookie(name, value, { secure, path, domain, expires }) {
    // 设置 cookie 的代码
}
setCookie("type", "js", {
    secure: true,
    expires: 60000
});
````
##### 解构的参数是必需的
如果设置了解构参数，且解构参数没有设置默认值，那么解构参数必传。
````js
// 设置了解构参数
function setCookie(name, value, { secure, path, domain, expires } = {}) {
    // 设置 cookie 的代码
}
setCookie("type", "js", {
    secure: true,
    expires: 60000
});
````
设置了默认值可以不传。

##### 参数解构的默认值
你可以为参数解构提供可解构的默认值，就像在解构赋值时所做的那样，只需在其中每个参数后面添加等号并指定默认值即可。
````js
function setCookie(name, value,
    {
    secure = false,
    path = "/",
    domain = "example.com",
    expires = new Date(Date.now() + 360000000)
    } = {}
) {
    // ...
}
````

### 符号与符号属性
ES5 的对象属性名都是字符串，这容易造成属性名的冲突。ES6 引入了一种新的基本类型：符号（ Symbol ）。表示独一无二的值。这样就从根本上防止属性名的冲突。

#### 创建符号值
你可以使用全局 `Symbol` 函数来创建一个符号值, `Symbol` 函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述。
````js
    let s = Symbol('foo');
    console.log(s); // Symbol(foo)
````
如果 `Symbol` 的参数是一个对象，就会调用该对象的`toString`方法，将其转为字符串，然后才生成一个 `Symbol` 值。
````js
const obj = {
  toString() {
    return 'abc';
  }
};
const sym = Symbol(obj);
sym // Symbol(abc)
````
#### 使用符号值
作为属性名的`Symbol`, 你可以在任意能使用“需计算属性名”的场合使用符号。
````js
let mySymbol = Symbol();

// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
````

#### 符号值的转换
Symbol 值不能与其他类型的值进行运算，会报错。
#### 共享符号值
你或许想在不同的代码段中使用相同的符号值，例如：假设在应用中需要在两个不同的对象类型中使用同一个符号属性，用来表示一个唯一标识符。跨越文件或代码来追踪符号值是很困难并且易错的，为此， **ES6** 提供了“全局符号注册表”供你在任意时间点进行访问。

若你想创建共享符号值，应使用 `Symbol.for()` 方法而不是 `Symbol()` 方法。
````js
let uid = Symbol.for("uid");
let object = {};
object[uid] = "12345";
console.log(object[uid]); // "12345"
console.log(uid); // "Symbol(uid)"
````
`Symbol.for()` 方法首先会搜索全局符号注册表，看是否存在一个键值为 "uid" 的符号值。
若是，该方法会返回这个已存在的符号值；否则，会创建一个新的符号值，并使用该键值将
其记录到全局符号注册表中，然后返回这个新的符号值。

#### 检索符号属性
`Object.keys()` 与 `Object.getOwnPropertyNames()` 方法可以检索对象的所有属性名称，前者返回所有的可枚举属性名称，而后者则返回所有属性名称而无视其是否可枚举。然而两者都不能返回符号类型的属性，以保持它们在 **ES5** 中的功能不发生变化。而 **ES6** 新增了`Object.getOwnPropertySymbols()` 方法，以便让你可以检索对象的符号类型属性。

`Object.getOwnPropertySymbols()` 方法会返回一个数组，包含了对象自有属性名中的符号值。
````js
let uid = Symbol.for("uid");
let object = {
[uid]: "12345"
};
let symbols = Object.getOwnPropertySymbols(object);
console.log(symbols.length); // 1
console.log(symbols[0]); // "Symbol(uid)"
console.log(object[symbols[0]]); // "12345"
````

#### 使用知名符号暴露内部方法
除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。

* Symbol.hasInstance ：供 `instanceof` 运算符使用的一个方法，用于判断对象继承关系。
  ````js
  obj instanceof Array
  ```` 
  等价于:
  ````js
  Array[Symbol.hasInstance](obj)
  ````
  当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。
  ````js
  // 当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。
   let test = {
      [Symbol.hasInstance]() {
         console.log('test-----hasInstance')
      }
    }
    1 instanceof test // test-----hasInstance
  ````
  
* Symbol.isConcatSpreadable ：一个布尔类型值，在集合对象作为参数传递给`Array.prototype.concat()` 方法时，指示是否要将该集合的元素扁平化。
  ````js
  let colors1 = [ "red", "green" ],
  colors2 = colors1.concat([ "blue", "black" ]);
  console.log(colors2.length); // 4
  console.log(colors2); // ["red","green","blue","black"]
  ````
  数组默认是要展开的，类数组则相反。
  ````js
  let obj = {length: 2, 0: 'c', 1: 'd'};
  ['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']
  obj[Symbol.isConcatSpreadable] = true;
  ['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']
  ````
* Symbol.species ：用于产生派生对象的构造器。
  它主要的用途是，有些类库是在基类的基础上修改的，那么子类使用继承的方法时，作者可能希望返回基类的实例，而不是子类的实例。
  ````js
  class MyArray extends Array {
      // Symbol.species改变了实例的指向 不设置就是MyArray
      static get [Symbol.species]() { return Array; }
  }
    
  const a = new MyArray();
  const b = a.map(x => x);
    
  b instanceof MyArray // false
  b instanceof Array // true
  ````
* Symbol.match ：供 `String.prototype.match()` 函数使用的一个方法，用于比较字符串。
* Symbol.replace ：供 `String.prototype.replace()` 函数使用的一个方法，用于替换子字符串。
* Symbol.search ：供 `String.prototype.search()` 函数使用的一个方法，用于定位子字符串。
* Symbol.split ：供 `String.prototype.split()` 函数使用的一个方法，用于分割字符串。
  ````js
  let MyStringSymbol = {
      [Symbol.match](string) {
        console.log('Symbol.match', string);
      },
      [Symbol.replace](string) {
        console.log('Symbol.replace', string);
      },
      [Symbol.search](string) {
        console.log('Symbol.search', string);
      },
      [Symbol.split](string) {
        console.log('Symbol.split', string);
      },
  }
  'abc'.match(MyStringSymbol); // Symbol.match abc
  'abc'.replace(MyStringSymbol, 'a') // Symbol.replace abc
  'abc'.search(MyStringSymbol) // Symbol.search abc
  'abc'.split(MyStringSymbol) // Symbol.split abc
  ````
* Symbol.iterator ：返回迭代器的一个方法。
  对象进行`for...of`循环时，会调用`Symbol.iterator`方法，返回该对象的默认遍历器
  ````js
    class Collection {
      *[Symbol.iterator]() {
        let i = 0;
        while(this[i] !== undefined) {
          yield this[i];
          ++i;
        }
      }
    }

    let myCollection = new Collection();
    myCollection[0] = 1;
    myCollection[1] = 2;
    
    for(let value of myCollection) {
      console.log(value);
    }
    // 1
    // 2
  ````
* Symbol.toPrimitive ：返回对象所对应的基本类型值的一个方法。
  该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。
  ````js
    function Temperature(degrees) {
        this.degrees = degrees;
    }
    // 只要该对象进行试图基本类型转换就会触发该方法
    Temperature.prototype[Symbol.toPrimitive] = function(hint) {
        switch (hint) {
            case "string":
                return this.degrees + "\u00b0"; // 温度符号
            case "number":
                return this.degrees;
            case "default":
                return this.degrees + " degrees";
        }
    };
    let freezing = new Temperature(32);
    console.log(freezing + "!"); // "32 degrees!"
    console.log(freezing / 2); // 16
    console.log(String(freezing)); // "32°"
  ````
* Symbol.toStringTag ：供 `String.prototype.toString()` 函数使用的一个方法，用于创建对象的描述信息。
    这个属性可以用来定制`[object Object]`或`[object Array]`中`object`后面的那个字符串。
    ````js
    ({[Symbol.toStringTag]: 'foo'}).toString() // "[object foo]"
    ````
* Symbol.unscopables ：一个对象，该对象的属性指示了哪些属性名不允许被包含在 `with` 语句中。
    ````js
    // 没有 unscopables 时
    class MyClass {
      foo() { return 1; }
    }
    
    var foo = function () { return 2; };
    
    with (MyClass.prototype) {
      foo(); // 1
    }
    
    // 有 unscopables 时
    class MyClass {
      foo() { return 1; }
      get [Symbol.unscopables]() {
        return { foo: true }; // 排除了MyClass.prototype的foo
      }
    }
    
    var foo = function () { return 2; };
    
    with (MyClass.prototype) {
      foo(); // 2 这里直接指向全局了
    }
    ````
    上面代码通过指定`Symbol.unscopables`属性，使得`with`语法块不会在当前作用域寻找`foo`属性，即`foo`将指向外层作用域的变量。
    
### Set与Map
**Set**的作用主要用于检查某个值在列表中是否存在，**Map** 多数被用来提取数据，而
不是仅检查键的存在性。

#### Set
set是一个无重复值的列表
````js
let set = new Set(); // ()里面无值()可省略 let set = new Set;
// Set 不会使用强制类型转换来判断值是否重复 所以1和‘1’是不同的两个值
set.add(1);
set.add('1');
set.size; // 2
// 使用数组初始化Set
let set = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
console.log(set.size); // 5
// 判断有没有值
set.has(2) // true
// 删除值
set.delete(2); // 删除2
set.clear(); // 清空列表
// 循环
let set = new Set([1,2]);
set.forEach(value => console.log(value)); // 1, 2
// 将 Set 转换为数组
let set = new Set([1,2]) // 将数组转换为Set
let arr = [...set] // 将 Set 转换为数组
````
#### WeakSet
**WeakSet** 的成员只能是对象，而不能是其他类型的值。
**WeakSet** 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 **WeakSet** 之中。
**WeakSet**主要实现了如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存。
````js
let first = {};
let wk = new WeakSet([first]);
wk.has(first); // true
first = null; // 移除对于键的引用 同时wk也移除了
wk.has(first); // false
````
wk没有`size`，不可循环遍历。

#### Map
**ES6** 的 **Map** 类型是键值对的有序列表，而键和值都可以是任意类型。
````js
let map = new Map(); // ()里面无值()可省略 let map = new Map;
map.set(1, 1);
map.set('1', '1'); // 由于是通过Object.is()判断 1和‘1’是不同的键
console.log(map.size); // 2
map.has('1') // true
map.delete(1); // 删除1
map.clear(); // 清空map

// 初始化传入 以二维数组的形式
let map = new Map([['1', '1'], [1, 1]]);
// 循环
map.forEach(function(value, key, ownerMap) {
    console.log(key + " " + value);
    console.log(ownerMap === map);
});
// '1' '1'
// true
// 1 1
// true
````

#### WeakMap
**WeakMap**只能用来存储键为对象的值
````js
let first = {};
let wm = new WeakMap([[first, 1]])
wm.has(first); // true
first = null; // 移除对于键的引用 同时wm也移除了
wm.has(first); // false
````

### 迭代器与生成器
迭代器**Iterator**接口主要是用于`for...of`消费。还有扩展运算符`...`
原生具备 Iterator 接口的数据结构如下。
* Array
* Map
* Set
* String
* TypedArray
* 函数的 arguments 对象
* NodeList 对象

这说明上面的数据结构天生就支持`for...of`。如果想要原生未具备 `Iterator` 接口的数据结构支持`for...of`遍历，就得自己实现迭代器了。

生成器是一个特殊的函数，可以在被调用时自动创建一个迭代器。生成器的定义用一个星号（ `*` ）来表示，使用 `yield` 关键字能指明在每次成功的 `next()` 方法调用时应当返回什么值。

下面是一个例子：
````js
// let createIterator = function *() {} 生成器函数表达式
// 函数声明
function *createIterator() {
    yield 1;
    yield 2;
    yield 3;
}

// 生成器能像正规函数那样被调用，但会返回一个迭代器
let iterator = createIterator();
console.log(iterator.next().value); // 1
console.log(iterator.next().value); // 2
console.log(iterator.next().value); // 3

let obj = {};
// 直接挂载到obj[Symbol.iterator] 使得obj可以被for...of
obj[Symbol.iterator] = createIterator;
for(let item of obj){
    console.log(item);
}
// 1
// 2
// 3
````
> 不能将箭头函数创建为生成器。

#### 访问默认迭代器
````js
let values = [1, 2, 3];
let iterator = values[Symbol.iterator]();
console.log(iterator.next()); // "{ value: 1, done: false }"
console.log(iterator.next()); // "{ value: 2, done: false }"
console.log(iterator.next()); // "{ value: 3, done: false }"
console.log(iterator.next()); // "{ value: undefined, done: true }"
````

#### 创建可迭代对象
````js
obj = {
    items: [1,2,3],
    *[Symbol.iterator]() {
        for(let item of this.items) {
            yield item;
        }
    }
}

for(let item of obj) {
    console.log(item);
}

// 1
// 2
// 3
[...obj] // [1,2,3]

````

#### 集合的迭代器
`ES6` 具有三种集合对象类型：数组、 `Map` 与 `Set`。这三种类型都拥有如下的迭代器，有助于探索它们的内容：
* entries() ：返回一个包含键值对的迭代器；
* values() ：返回一个包含集合中的值的迭代器；
* keys() ：返回一个包含集合中的键的迭代器。

````js
let arr = [1,2,3];
let set = new Set([10, 20, 30]);
let map = new Map([['a', 'a'], ['b', 'b'], ['c', 'c']]);

// 与使用 arr.values() 相同
for (let value of arr) {
console.log(value);
}
// 与使用 set.values() 相同
for (let value of set) {
console.log(value);
}
// 与使用 map.entries() 相同
for (let [key, value] of map) { // 解构
console.log(key, value);
}

// 1 2 3
// 10 20 30
// a a
// b b
// c c
````

#### 传递参数给迭代器
````js
function *createIterator() {
let first = yield 1;
let second = yield first + 2; // 4 + 2
// return 9; 如果有return后面的将不会执行 9将会当成最后一个值被返回
yield second + 3; // 5 + 3
}
let iterator = createIterator();
console.log(iterator.next()); // "{ value: 1, done: false }"
console.log(iterator.next(4)); // "{ value: 6, done: false }" first = 4;
console.log(iterator.next(5)); // "{ value: 8, done: false }" second = 5;
console.log(iterator.next()); // "{ value: undefined, done: true }"
````

#### 生成器委托
````js
function *createNumberIterator() {
    yield 1;
    yield 2;
    return 3;
}
function *createRepeatingIterator(count) {
    for (let i=0; i < count; i++) {
        yield "repeat";
    }
}
function *createCombinedIterator() {
    let result = yield *createNumberIterator(); // 这里不会输出return的值 不输出3
    yield result;
    yield *createRepeatingIterator(result); // result被传入了createRepeatingIterator
}
var iterator = createCombinedIterator();
console.log(iterator.next()); // "{ value: 1, done: false }"
console.log(iterator.next()); // "{ value: 2, done: false }"
console.log(iterator.next()); // "{ value: 3, done: false }"
console.log(iterator.next()); // "{ value: "repeat", done: false }"
console.log(iterator.next()); // "{ value: "repeat", done: false }"
console.log(iterator.next()); // "{ value: "repeat", done: false }"
console.log(iterator.next()); // "{ value: undefined, done: true }"
````