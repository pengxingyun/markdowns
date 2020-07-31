# Javascript之观察者模式

**观察者模式**又被称为**发布-订阅模式**，是设计模式中的一种行为型模式；

**观察者模式**定义了一种一对多的对象依赖关系，当被依赖的对象的状态发生了改变，所有依赖它的对象都会得到通知。

最常用的是定义元素的点击事件：
````js
document.body.addEventListener('click', () => {
    console.log('hello world!!!');
})
````
这就是观察者模式， 对 `document.body` 的点击事件进行观察。

简单实现：
````js
let Observer = (function(){
    // 防止消息队列暴漏而被篡改
    let _messages = {}; // 静态私有变量
    return {
        // 注册信息接口
        regist: function(type, fn) {
            _messages[type] = fn;
        },
        // 发布信息接口
        fire: function(type) {
            _messages[type] && _messages[type]();
        },
        // 移除信息接口
        remove: function(type) {
            console.log('delete-----', type);
            delete _messages[type];
        }
    }
})();
````

测试`Observer`
````js
function fn1() {
    console.log('fn1');
    // 注册fn1
    Observer.regist('fn1', () => {
        console.log('fn1 is called');
    })
}

function fn2() {
    console.log('fn2');
    // 注册fn2
    Observer.regist('fn2', () => {
        console.log('fn2 is called');
    })
}

fn1(); // fn1
fn2(); // fn2
Observer.fire('fn1'); // fn1 is called
// 移除fn1
Observer.remove('fn1'); // delete----- fn1
Observer.fire('fn2'); // fn2 is called
// 移除fn2
Observer.remove('fn2'); // delete----- fn2
````

简单实现`$emit`，`$on`，`$off`

````js
class Event {
    constructor() {
        this._callbacks = {};
    }
    $off(name) {
        delete this._callbacks[name];
    }
    $on(name, fn) {
        (this._callbacks[name] || (this._callbacks[name] = [])).push(fn)
    }
    $emit(name, args) {
        let cbs = this._callbacks[name];
        if(!cbs) return cbs;
        cbs.forEach(cb => {
            cb.call(this, args);
        })
    }
}

let event = new Event();
event.$on('event1', function(arg) {
    console.log('event1', this, arg);
})

event.$on('event1', function(arg) {
    console.log('event1 again', this, arg);
})

event.$on('event2', function(arg) {
    console.log('event2', this, arg);
})

event.$emit('event1', {name: 'abc'})
// event1 Event {_callbacks: {…}}_callbacks: {event1: Array(2)}__proto__: Object {name: "abc"}
// event1 again Event {_callbacks: {…}} {name: "abc"}
event.$off('event1'); // 解除event1观察
event.$emit('event1', {name: 'abc'}); // undefined
event.$emit('event2', {name: '123'});
// event2 Event {_callbacks: {…}} {name: "123"}
````
