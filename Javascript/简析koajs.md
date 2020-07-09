# 简析koajs

### 看下原生和koa起服务的区别：
原生：
````js
const http = require('http')

let server = http.createServer((req, res) => {
    res.end('hello world');
})

server.listen(3000, function() {
    console.log('listen to on * : 3000');
})
````
koa:
````js
const Koa = require('koa');
const app = new Koa();

app.use(ctx => {
  ctx.body = 'hello world';
});

app.listen(3000, function() {
    console.log('listen to on * : 3000');
})
````

以上会输出一样的结果。

不同的是koa把`req`, `res`都集成到`ctx`返回了。

#### 先实现ctx集成

./application.js
````js
const http = require('http')
class Koa {
    constructor() {
        this.callback = null;
    }
    /**
    * Use the given middleware `fn`.
    * @param {function} callback
    */
    use(callback) {
        this.callback = callback;
    }
    /**
     * engine server
     * @param  {...any} args 
     */
    listen(...args) {
        const server = http.createServer((req, res) => {
            let ctx = this.createContext(req, res);
            this.callback(ctx);
            res.end(ctx.body);
        })
        server.listen(...args);
    }
    /**
     * mixin req and res for ctx
     * @param {*} req 
     * @param {*} res 
     */
    createContext(req, res) {
        let ctx = Object.create(null);
        ctx.request = Object.create(req);
        ctx.response = Object.create(res);
        
        ctx.req = req;
        ctx.res = res;
        return ctx;
    }
}

module.exports = Koa;
````

这就已经足够支持上面代码输出了。

````js
const Koa = require('./application.js');
const app = new Koa();

app.use(ctx => {
  ctx.body = 'hello world';
});

app.listen(3000, function() {
    console.log('listen to on * : 3000');
})
````
### 实现koa use中间件功能

````js
const Koa = require('koa')
const app = new Koa()

app.use(async function (ctx, next) {
    console.log('>> one');
    await next();
    console.log('<< one');
});

app.use(async function (ctx, next) {
    console.log('>> two');
    ctx.body = 'two';
    await next();
    console.log('<< two');
});

app.use(async function (ctx, next) {
    console.log('>> three');
    await next();
    console.log('<< three');
});
````
上面这个代码输出顺序是：
````
>> one
>> two
>> three
<< three
<< two
<< one
````
这个就是koa的洋葱圈模型。从外到内，再从里到外的执行顺序，如果不next()，后面的中间件将不会执行。

实现：

./application.js
````js
const http = require('http');

class Koa {
    constructor() {
        this.middlewares = [];
    }
    use(fn) {
        this.middlewares.push(fn);
        return this; // 支持链式调用
    }
    callback() {
        let fn = this.compose(this.middlewares);
        const handleRequest = ((req, res) => {
            // 混合req res到ctx
            let ctx = this.createContext(req, res);
            return this.handleRequest(ctx, fn);
        })
        return handleRequest;
    }
    handleRequest(ctx, fnMiddleware) {
        const handleResponse = () => this.respond(ctx);
        return fnMiddleware(ctx).then(handleResponse);
    }
    /**
     * engine server
     * @param  {...any} args 
     */
    listen(...args) {
        const server = http.createServer(this.callback())
        server.listen(...args);
    }
    /**
     * mixin req and res for ctx
     * @param {*} req 
     * @param {*} res 
     */
    createContext(req, res) {
        let ctx = Object.create(null);
        ctx.request = Object.create(req);
        ctx.response = Object.create(res);

        ctx.req = req;
        ctx.res = res;

        return ctx;
    }
    compose(middlewares) {
        return function(ctx) {
            return dispatch(0);
            function dispatch(i) {
                if(!middlewares[i]) return;
                let fn = middlewares[i];
                if(!fn) return Promise.resolve()
                try {
                    return Promise.resolve(fn(ctx, function next() {return dispatch(i + 1)}));
                } catch(err) {
                    return Promise.reject(err);
                }
            }
        }
    }
    /**
     * 简单输出ctx.body
     * @param {*} ctx 
     */
    respond(ctx) {
        const res = ctx.res;
        res.end(ctx.body);
    }
}

module.exports = Koa;
````

[完整代码地址](https://github.com/pengxingyun/kkoa)