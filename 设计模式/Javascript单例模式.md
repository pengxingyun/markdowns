# Javascript单例模式
**单例模式**：保证⼀个类仅有⼀个实例，并提供⼀个访问它的全局访问点。实现的⽅法为先判断实例存在与否，如果存在则直接返回，如果不存在就创建了再返回，这就确保了⼀个类只有⼀个实例对象。

### 实现
````js
let getSingle = function(fn) { // 闭包实现 result不会被销毁
    let result;
    return function() {
        return result || (result = fn.apply(this, arguments));
    }
}

let cb = function(name) {
    let o = {
        name: name
    }
    return o;
}

let demo = getSingle( cb );

let d = demo('abc'); // {name: 'abc'}
let d1 = demo('efg'); // {name: 'abc'} 返回的一直是上面闭包中的result
console.log(d.name);
````

常用于实现单一对象，比如弹框，toast等。保证全局唯一性。

