# Javascript之享元模式
**享元模式**：运用共享技术有效地支持大量的细粒度的对象，避免对象间拥有相同内容造成多余的开销。

试着实现分页功能：
如果用显示/隐藏的方式创建所有的元素放在页面，会造成很多冗余的 `dom` 放在页面上。可以只创建一页的数量，每次分页只是改变页面元素内容。

````js
let created = []; // 一页的元素
function FlyWeight() {
    // 创建dom
    let create = () => {
        let dom = document.createElement('div');
        document.body.appendChild(dom);
        created.push(dom);
        return dom;
    }
    return {
        getDiv: function() {
            // 不够数量就创建
            if(created.length < 5) {
                return create();
            } else { // 够数量之后一直操作的都是数组里面的元素
                let div = created.shift();
                created.push(div);
                return div;
            }
        }
    }
}
````
第一次初始化之后，以后操作的都是数组的元素，避免太多 `dom` 影响页面性能表现。

````js
let article = [
    'aaa',
    'bbb',
    'ccc',
    'ddd',
    'eee',
    'fff',
    'ggg',
    'hhh',
    'iii',
    'jjj',
    'kkk',
    'lll',
    'mmm',
    'nnn',
    'ooo'
]
let paper = 0,
    num = 5,
    len = article.length;

// 初始化数组 初始化之后操作的都是这个数组的元素
 for(let i = 0; i < num; i++) {
     FlyWeight().getDiv().innerHTML = article[i];
 }

 // 添加下一页按钮
 let next = document.createElement('div');
 next.id = 'next';
 next.innerText = '下一页';
 document.body.appendChild(next);

// 点击下一页
 next.onclick = function(e) {
    let n = ++paper * num % len;

    for(let i = 0; i < num; i++) {
        if(article[n + i]) {
            FlyWeight().getDiv().innerHTML = article[n + i];
        } else if(article[n + i - len]) { // 不够拿前面的补
            FlyWeight().getDiv().innerHTML = article[n + i - len];
        } else {
            FlyWeight().getDiv().innerHTML = '';
        }
    }
}
````

完整代码：
````js
let article = [
    'aaa',
    'bbb',
    'ccc',
    'ddd',
    'eee',
    'fff',
    'ggg',
    'hhh',
    'iii',
    'jjj',
    'kkk',
    'lll',
    'mmm',
    'nnn',
    'ooo'
];
let created = []; // 一页的元素
let paper = 0,
    num = 5,
    len = article.length;
function FlyWeight() {
    // 创建dom
    let create = () => {
        let dom = document.createElement('div');
        document.body.appendChild(dom);
        created.push(dom);
        return dom;
    }
    return {
        getDiv: function() {
            // 不够数量就创建
            if(created.length < num) {
                return create();
            } else { // 够数量之后一直操作的都是数组里面的元素
                let div = created.shift();
                created.push(div);
                return div;
            }
        }
    }
}

// 初始化数组 初始化之后操作的都是这个数组的元素
for(let i = 0; i < num; i++) {
    FlyWeight().getDiv().innerHTML = article[i];
}
// 添加下一页按钮
let next = document.createElement('div');
next.id = 'next';
next.innerText = '下一页';
document.body.appendChild(next);

// 点击下一页
next.onclick = function(e) {
   let n = ++paper * num % len;

   for(let i = 0; i < num; i++) {
       if(article[n + i]) {
           FlyWeight().getDiv().innerHTML = article[n + i];
       } else if(article[n + i - len]) {
           FlyWeight().getDiv().innerHTML = article[n + i - len];
       } else {
           FlyWeight().getDiv().innerHTML = '';
       }
   }
}
````

享元模式的应用目的是为了提高程序的执行效率与系统的性能。因此在大型系统开发中应用是比较广泛的，百分之一的效率提成有时可以发生质的改变。有时系统内存存在大量对象，会造成大量内存占用，所以应用享元模式来减少内存消耗是很有必要的。