# Javascript ES6（一）

### 块级作用域
#### let 声明
`let` 声明的语法与 `var` 的语法一致。你基本上可以用 `let` 来代替 `var` 进行变量声明，但会将变量的作用域限制在当前代码块中。

1. 和 `var` 不一样的是 `let` 不会变量提升。
2. 禁止重复声明:
   如果一个标识符已经在代码块内部被定义，那么在此代码块内使用同一个标识符进行 `let`声明就会导致抛出错误。 `var` 不会有这个限制。
   
#### const 声明
使用 `const` 声明的变量会被认为是常量，意味着它们的值在被设置完成后就不能再被改变。仅针对值类型，如果是引用类型，它所包含的值是可以修改的。
1. 禁止重复声明：
    前面无论是`var`定义过，`let`定义过都会报错。
    
#### 块级绑定

##### 暂时性死区
使用 `let` 或 `const` 声明的变量，若试图在定义位置之前使用它，则会出现暂时性死区。不同作用域不受此影响。
````js
console.log(typeof value); // "undefined"
if(condition){
    console.log(typeof value); // 引用错误
    let value = 'val';
}
````
##### 循环内的`let`

在每次迭代中，都会创建一个新的同名变量并对其进行初始化。
````js
var funcs = [];
for (let i = 0; i < 10; i++) {
    funcs.push(function() {
        console.log(i);
    });
}
funcs.forEach(function(func) {
    func(); // 从 0 到 9 依次输出
})
````
在循环中 `let` 声明每次都创建了一个新的 `i` 变量，因此在循环内部创建的函数获得了各自的 `i` 副本，而每个 `i` 副本的值都在每次循环迭代声明变量的时候被确定了。

要理解上面那句话只要把`let i = 0;` 放到循环外定义就知道了。

比如下面结果会输出10个10：
````js
var funcs = [];
let i = 0; // 相当于切断了i的块级绑定
for (; i < 10; i++) {
    funcs.push(function() {
        console.log(i);
    });
}
funcs.forEach(function(func) {
    func(); // 10个10
})
````

##### 循环内的 `const`
1. 不能在`for`循环中用`const`定义`i`，因为`i++`会对`i`进行修改。
2. 而另一方面， `const` 变量在 `for-in` 或 `for-of` 循环中使用时，与 `let` 变量效果相同。 `const` 能够在 `for-in` 与 `for-of` 循环内工作，是因为循环为每次迭代创建了一个新的变量绑定，而不是试图去修改已绑定的变量的值。

##### 全局块级绑定
`let`和`const`定义在全局的变量不会被添加到全局对象上。这也就意味着你不能使用 `let` 或 `const` 来覆盖一个全局变量，你只能将其屏蔽。

#### 最佳实践
块级绑定当前的最佳实践就是：在默认情况下使用 `const` ，而只在你知道变量值需要被更改
的情况下才使用 `let` 。这在代码中能确保基本层次的不可变性，有助于防止某些类型的错
误。

### 字符串
* `includes()` 方法，在给定文本存在于字符串中的任意位置时会返回 `true` ，否则返回`false`；支持第二个参数，从下标index开始查找。
* `startsWith()` 方法，在给定文本出现在字符串起始处时返回 `true` ，否则返回 `false` ；支持第二个参数，从下标index开始查找。
* `endsWith()` 方法，在给定文本出现在字符串结尾处时返回 `true` ，否则返回 `false` 。支持第二个参数，从下标string.length - index开始查找。
* `repeat()` 方法，它接受一个参数作为字符串的重复次数，返回一个将初始字符串重复指定次数的新字符串。

### 函数
#### 参数默认值
对未明确指定的参数采用默认值传入。
````js
function makeRequest(url, timeout = 2000, callback = function(){}) {
// 函数的剩余部分
}
makeRequest('/foo'); // ==> makeRequest('/foo', 2000, function(){})
````

`arguments`只存储明确指定的参数，比如上面例子里，`arguments = ['/foo']`。

你可以执行一个函数来产生参数的默认值。
````js
function getValue() {
    return 5;
}
function add(first, second = getValue()) {
    return first + second;
}
console.log(add(1, 1)); // 2
console.log(add(1)); // 6
````
注意这里的`getValue()`只有在`second`未传入时才会调用。而在 `getValue()` 的函数声明初次被解析时并不会进行调用。

也可以把前一个值当成默认参数。
````js
function add(first, second = first) {
    return first + second;
}
console.log(add(1, 1)); // 2
console.log(add(1)); // 2
````
把前一个值传入函数产生当前默认值。
````js
function getValue(value) {
    return value + 5;
}
function add(first, second = getValue(first)) {
    return first + second;
}
console.log(add(1, 1)); // 2
console.log(add(1)); // 7
````

引用其他参数来为参数进行默认赋值时，仅允许引用前方的参数，因此前面的参数不能访问后面的参数。这是参数默认值的暂时性死区（TDZ）概念。变量定义前不能引用。
````js
function add(first = second, second) {
    return first + second;
}
console.log(add(1, 1)); // 2
console.log(add(undefined, 1)); // 抛出错误
````

#### 剩余参数
剩余参数（ rest parameter ）由三个点（ ... ）与一个紧跟着的具名参数指定，它会是包
含传递给函数的其余参数的一个数组，名称中的“剩余”也由此而来。
````js
function add(...nums) {
    return nums.reduce((a, b) => a + b);
}

add(1,2,3) // 6
````

函数的 `length` 属性用于指示具名参数的数量，而剩余参数对其毫无影响。比如上面`add.length = 0`
剩余参数和arguments的值是一一对应的。比如上例`nums[0] === arguments[0]`, `nums[1] === arguments[1]`。
##### 剩余参数的限制条件
1. 函数只能有一个剩余参数，并且它必须被放在最后。否则会报 `Rest parameter must be last formal parameter`（语法错误：不能在剩余参数后使用具名参数）。
2. 剩余参数不能在对象字面量的 `setter` 属性中使用。因为对象字面量的 `setter` 被限定只能使用单个参数；而剩余参数按照定义是不限制参数数量的。

#### 扩展运算符
剩余参数允许你把多个独立的参数合并到一个数组中；而扩展运算符则允许将一个数组分割，并将各个项作为分离的参数传给函数。这也就是说可以用到的地方就是把数组分割成项。比如:

`Math.max()`比较数组内最大值。
````js
let arr = [1,2,3,8]
Math.max(...arr); // 8
````

拷贝数组
````js
let arr = [1,2,3,8]
arr1 = arr
arr2 = [...arr]

arr1[0] = 9
console.log(arr[0]) // 9
console.log(arr2[0]) // 1
````

#### 明确函数的双重用途
JS 为函数提供了两个不同的内部方法： `[[Call]]` 与 `[[Construct]]` 。当函数未使用 `new`进行调用时， `[[call]]` 方法会被执行，运行的是代码中显示的函数体。而当函数使用 `new`进行调用时， `[[Construct]]` 方法则会被执行，负责创建一个被称为新目标的新的对象，并且使用该新目标作为 `this` 去执行函数体。拥有 `[[Construct]]` 方法的函数被称为构造器。

##### 在 ES5 中判断函数如何被调用
在 ES5 中判断函数是不是使用了 `new` 来调用（即作为构造器），最流行的方式是使用
`instanceof`，例如：
````js
function Person(name) {
    if (this instanceof Person) {
        this.name = name; // 使用 new
    } else {
        throw new Error("You must use new with Person.")
    }
}
var person = new Person("Nicholas");
var notAPerson = Person("Nicholas"); // 抛出错误
````
可惜的是，该方法并不绝对可靠，因为在不使用 `new` 的情况下 `this` 仍然可能
是 `Person` 的实例

````js
function Person(name) {
    if (this instanceof Person) {
        this.name = name; // 使用 new
    } else {
        throw new Error("You must use new with Person.")
    }
}
var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael"); // 奏效了！
````

##### new.target 元属性
当函数的 `[[Construct]]` 方法被调用时， `new.target` 会被填入 `new` 运算符的作用目标，该目标通常是新创建的对象实例的构造器，并且会成为函数体内部的 `this` 值。而若 `[[Call]]` 被执行， `new.target` 的值则会是`undefined`

安全地判断函数是否被使用 `new` 进行了调用.
````js
function Person(name) {
    if (typeof new.target !== "undefined") {
        this.name = name; // 使用 new
    } else {
        throw new Error("You must use new with Person.")
    }
}
var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael"); // 出错！
````
也可以检查 `new.target` 是否被使用特定构造器进行了调用。
````js
function Person(name) {
    if (new.target === Person) {
        this.name = name; // 使用 new
    } else {
        throw new Error("You must use new with Person.")
    }
}
function AnotherPerson(name) {
    Person.call(this, name);
}
var person = new Person("Nicholas");
var anotherPerson = new AnotherPerson("Nicholas"); // 出错！
````

#### 箭头函数
箭头函数正如名称所示那样，使用一个“箭头”（ `=>` ）来定义，但它的行为在很多重要方面与传统的 JS 函数不同：
* 没有 `this` 、 `super` 、 `arguments` ，也没有 `new.target` 绑定
* 不能被使用 `new` 调用： 箭头函数没有 `[[Construct]]` 方法。
* 没有原型
* 不能更改 `this`
* 没有 `arguments` 对象
* 不允许重复的具名参数

总结箭头函数不能定义构造函数，不能更改 `this`，没有 `arguments` 对象。其他表现跟普通函数一样。

##### 没有this绑定
**JS** 最常见的错误领域之一就是在函数内的 `this` 绑定。
````js
var PageHandler = {
    id: "123456",
    init: function() {
        document.addEventListener("click", function(event) {
            this.doSomething(event.type); // 错误 这里this指向的是document
        }, false);
    },
    doSomething: function(type) {
        console.log("Handling " + type + " for " + this.id);
    }
};
````

你可以明确使用 `bind()` 方法将函数的 `this` 值绑定到 `PageHandler` 上，以修正这段代码: 
````js
var PageHandler = {
    id: "123456",
    init: function() {
        // 这里的this是PageHandler
        document.addEventListener("click", (function(event) {
            this.doSomething(event.type); // 成功执行 this被指向到PageHandler
        }).bind(this), false);
    },
    doSomething: function(type) {
        console.log("Handling " + type + " for " + this.id);
    }
};
````
箭头函数：
````js
var PageHandler = {
    id: "123456",
    init: function() {
        document.addEventListener("click",
        event => this.doSomething(event.type), false);
    },
    doSomething: function(type) {
        console.log("Handling " + type + " for " + this.id);
    }
}
````
箭头函数没有 `this` 绑定，意味着箭头函数内部的 `this` 值只能通过查找作用域链来确定。如果箭头函数被包含在一个非箭头函数内，那么 `this` 值就会与该函数的相等；否则，
`this` 值就会是全局对象。

同样，由于箭头函数的 `this` 值由包含它的函数决定，因此不能使用 `call()` 、 `apply()` 或 `bind()` 方法来改变其 `this` 值。

只是不能改变，还是可以调用。
````js
var sum = (num1, num2) => num1 + num2;
console.log(sum.call(null, 1, 2)); // 3
console.log(sum.apply(null, [1, 2])); // 3
var boundSum = sum.bind(null, 1, 2);
console.log(boundSum()); // 3
````

##### 尾调用优化
尾调用（ **tail call** ）指的是调用函数的语句是另一个函数的最后语句。

当满足以下条件时，尾调用优化会清除当前栈帧并再次利用它，而不是为尾调用创建新的栈帧：
1. 尾调用不能引用当前栈帧中的变量（意味着该函数不能是闭包）；
2. 进行尾调用的函数在尾调用返回结果后不能做额外操作；
3. 尾调用的结果作为当前函数的返回值。

作为一个例子，下面代码满足了全部三个条件，因此能被轻易地优化：
````js
function doSomething() {
    // 被优化
    return doSomethingElse();
}
````

尾调用优化允许某些函数的调用被优化，以保持更小的调用栈、使用更少的内存，并防止堆
栈溢出。当能进行安全优化时，它会由引擎自动应用。不过你可以考虑重写递归函数，以便
能够利用这种优化。

### 扩展的对象功能
#### 对象字面量语法的扩展
##### 属性初始化器的速记法
在 ES5 及更早版本中，对象字面量是“键/值对”的简单集合。
````js
function createPerson(name, age) {
    return {
        name: name,
        age: age
    };
}
````
在 ES6 中，当对象的一个属性名称与本地变量名相同时，你可以简单书写名称而省略冒号与值。
````js
function createPerson(name, age) {
    return {
        name,
        age
    };
}
````

##### 方法简写
在 ES5 及更早版本中，你必须指定一个名称, 并用完整的函数定义来为对象添加方法。
````js
var person = {
    name: "Nicholas",
    sayName: function() {
        console.log(this.name);
    }
};
````
ES6
````js
var person = {
    name: "Nicholas",
    sayName() {
        console.log(this.name);
    }
};
````
这种写法具备 ES5 的 `sayName()` 方法的所有特征。而有一点区别是：方法简写能使用 `super` 。

#### 新的方法
##### Object.is()
比较二者类型相同并且值也相等。

````js
console.log(+0 == -0); // true
console.log(+0 === -0); // true
console.log(Object.is(+0, -0)); // false
console.log(NaN == NaN); // false
console.log(NaN === NaN); // false
console.log(Object.is(NaN, NaN)); // true
console.log(5 == 5); // true
console.log(5 == "5"); // true
console.log(5 === 5); // true
console.log(5 === "5"); // false
console.log(Object.is(5, 5)); // true
console.log(Object.is(5, "5")); // false
````
在许多情况下， `Object.is()` 的结果与 `===` 运算符是相同的，仅有的例外是：它会认为 `+0` 与 `-0` 不相等，而且 `NaN` 等于 `NaN` 。

##### Object.assign()
合并多个对象项到第一个对象。
````js
var receiver = {};
Object.assign(receiver,
    {
        type: "js",
        name: "file.js"
    },
    {
        type: "css"
    }
);
console.log(receiver.type); // "css"
console.log(receiver.name); // "file.js"
````
对象有多个同项时，后一项会覆盖前一项。

##### 重复的对象字面量属性
````js
var person = {
    name: "Nicholas",
    name: "Greg" // 在 ES6 严格模式中不会出错 ES5会
};
console.log(person.name); // "Greg"
````

##### 自有属性的枚举顺序
ES5 并没有定义对象属性的枚举顺序，而是把该问题留给了 JS 引擎厂商。而 ES6 则严格定义了对象自有属性在被枚举时返回的顺序。
这对 `Object.getOwnPropertyNames()` 与 `Reflect.ownKeys` 如何返回属性造成了影响，还同样影响了 `Object.assign()` 处理属性的顺序。

**note**: `for-in` 循环的枚举顺序仍未被明确规定，因为并非所有的 JS 引擎都采用相同的方式。而 `Object.keys()` 和 `JSON.stringify()` 也使用了与 `for-in` 一样的枚举顺序。

自有属性枚举时基本顺序如下：
1. 所有的数字类型键，按升序排列。
2. 所有的字符串类型键，按被添加到对象的顺序排列。
3. 所有的符号类型键，也按添加顺序排列。

````js
var obj = {
    a: 1,
    0: 1,
    c: 1,
    2: 1,
    b: 1,
    1: 1
};
obj.d = 1;
console.log(Object.getOwnPropertyNames(obj).join("")); // "012acbd"
````

##### 修改对象的原型
ES5 添加了 `Object.getPrototypeOf()` 方法来从任意指定对象中获取其原型，但仍然缺少在初始化之后更改对象原型的标准方法。
ES6 通过添加 `Object.setPrototypeOf()` 方法而改变了这种假定，此方法允许你修改任意指定对象的原型。它接受两个参数：需要被修改原型的对象，以及将会成为前者原型的对象。
````js
let person = {
    getGreeting() {
        return "Hello";
    }
};
let dog = {
    getGreeting() {
        return "Woof";
    }
};
// 原型为 person
let friend = Object.create(person);
console.log(friend.getGreeting()); // "Hello"
console.log(Object.getPrototypeOf(friend) === person); // true
// 将原型设置为 dog
Object.setPrototypeOf(friend, dog);
console.log(friend.getGreeting()); // "Woof"
console.log(Object.getPrototypeOf(friend) === dog); // true
````

##### 正式的“方法”定义
在 ES6 之前，“方法”的概念从未被正式定义，它此前仅指对象的函数属性（而非数据属
性）。 ES6 则正式做出了定义：方法是一个拥有 `[[HomeObject]]` 内部属性的函数，此内部属性指向该方法所属的对象。
````js
let person = {
    // 方法
    getGreeting() {
        return "Hello";
    }
};

// 并非方法
function shareGreeting() {
    return "Hi!";
}
````

对象里面的叫方法。