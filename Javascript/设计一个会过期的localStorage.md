# 设计一个会过期的localStorage

预计实现一个有过期功能的localStorage,需实现：
1. 存数据时同时存入过期时间
2. 获取数据同时判断数据是否过期，过期会删除数据
3. 获取、存入、删除同时支持回调

### 搭框架
````js
class BaseLocalStorage {
    constructor(preId, timeSign) {
        // 定义本地数据库前缀
        this.preId = preId;
        // 拼接符
        this.timeSign = timeSign || '|-|';
    }
    // 根据状态码判断是否成功
    get status() {
        return {
            SUCCESS: 0, // 成功
            FAILURE: 1, // 失败
            OVERFLOW: 2, // 溢出
            TIMEOUT: 3 // 过期
        }
    }
    // 保存本地存储链接
    get storage() {
        return localStorage || window.localStorage;
    }
    // 获取本地存储数据库数据真实字段
    getKey(key) {
        return this.preId + key;
    }
    // 添加（修改）数据
    set(key, value, callback, time) {}
    // 获取数据
    get(key, callback) {}
    // 删除数据
    remove(key, callback) {}
}
````
### 实现 `set`
````js
/**
* 添加（修改）数据
* @params {string} key 数据字段标识
* @params {string} value 值
* @params {function} callback 回调函数
* @params {time} 最大有效有效时间
*/
set(key, value, callback, time) {
    // 默认成功
    let status = this.status.SUCCESS;
    key = this.getKey(key);
    try {
        time = new Date(time).getTime() || time.getTime();
    } catch(e) {
        // 默认有效时间一个月
        time = new Date().getTime() + 1000 * 60 * 60 * 24 * 31;
    }
    try {
        this.storage.setItem(key, time + this.timeSign + value);
    } catch (e) {
        status = this.status.OVERFLOW;
    }
    // 执行设置成功回调
    callback && callback.call(this, status, key, value);
}
````
### 实现 `get`

````js
/**
* 获取数据
* @params {string} key 数据字段标识
* @params {function} callback 回调函数
*/
get(key, callback) {
    // 默认成功
    let status = this.status.SUCCESS,
        value = null,
        timeSignLen = this.timeSign.length,
        result;

    key = this.getKey(key);
    // 判断传入key是否有问题
    try {
        value = this.storage.getItem(key);
    } catch(e) {
        result = {
            status: this.status.FAILURE,
            value: null
        }
        callback && callback.call(this, result.status, result.value);
        return result;
    }
    // 判断是否有内容
    if(value) {
        let index = value.indexOf(this.timeSign);
        let time = +value.slice(0, index); // 获取时间戳
        // 没过期
        if(new Date(time).getTime() > new Date().getTime() || time == 0) {
            value = value.slice(index + timeSignLen);
        } else { // 过期
            value = null,
            status = this.status.TIMEOUT;
            this.remove(key); // 同时删除
        }
    } else {
        status = this.status.FAILURE;
    }

    result = {
        status: status,
        value: value
    }
    callback && callback.call(this, result.status, result.value);
    return result;
}
````

### 实现 `remove`
````js
/**
* 删除数据
* @params {string} key 数据字段标识
* @params {function} callback 回调函数
*/
remove(key, callback) {
    // 默认失败
    let status = this.status.FAILURE,
        value = null;
    key = this.getKey(key);

    try {
        value = this.storage.getItem(key);
    } catch(e) {}
    if(value) {
        this.storage.removeItem(key);
        status = this.status.SUCCESS;
        callback && callback.call(this, status, status > 0 ? null :
            value.slice(value.indexOf(this.timeSign) + this.timeSign.length))
    }
}
````

测试代码：
````js
let BLS = new BaseLocalStorage('BLS_', '|-|');
BLS.set('a', 'abc', (status, key, value) => {
    console.log('状态码----', status); // 状态---- 0
    console.log(`set ${key} for`, value); // set BLS_a for abc
})
BLS.get('a', (status, value) => {
    console.log('状态码----', status); // 状态---- 0
    console.log(`value === `, value); // value ===  abc
})

BLS.remove('a', (status, value) => {
    console.log('value === ', value); // abc
})

// 设置一个马上过期的item
BLS.set('a', 'abc', (status, key, value) => {
    console.log('状态码----', status); // 状态---- 0
    console.log(`set ${key} for`, value); // set BLS_a for abc
}, new Date().getTime());

// 已过期
BLS.get('a', (status, value) => {
    console.log('状态码----', status); // 状态---- 3
    console.log(`value === `, value); // value ===  null
})
````
完整代码：
````js
class BaseLocalStorage {
    constructor(preId, timeSign) {
        // 定义本地数据库前缀
        this.preId = preId;
        // 拼接符
        this.timeSign = timeSign || '|-|';
    }
    get status() {
        return {
            SUCCESS: 0, // 成功
            FAILURE: 1, // 失败
            OVERFLOW: 2, // 溢出
            TIMEOUT: 3 // 过期
        }
    }
    // 保存本地存储链接
    get storage() {
        return localStorage || window.localStorage;
    }
    // 获取本地存储数据库数据真实字段
    getKey(key) {
        return this.preId + key;
    }
    /**
    * 添加（修改）数据
    * @params {string} key 数据字段标识
    * @params {string} value 值
    * @params {function} callback 回调函数
    * @params {time} 最大有效有效时间
    */
    set(key, value, callback, time) {
        // 默认成功
        let status = this.status.SUCCESS;
        key = this.getKey(key);
        try {
            time = new Date(time).getTime() || time.getTime();
        } catch(e) {
            // 默认有效时间一个月
            time = new Date().getTime() + 1000 * 60 * 60 * 24 * 31;
        }
        try {
            this.storage.setItem(key, time + this.timeSign + value);
        } catch (e) {
            status = this.status.OVERFLOW;
        }
        // 执行设置成功回调
        callback && callback.call(this, status, key, value);
    }
    /**
    * 获取数据
    * @params {string} key 数据字段标识
    * @params {function} callback 回调函数
    */
    get(key, callback) {
        // 默认成功
        let status = this.status.SUCCESS,
            value = null,
            timeSignLen = this.timeSign.length,
            result;

        key = this.getKey(key);
        // 判断传入key是否有问题
        try {
            value = this.storage.getItem(key);
        } catch(e) {
            result = {
                status: this.status.FAILURE,
                value: null
            }
            callback && callback.call(this, result.status, result.value);
            return result;
        }
        // 判断是否有内容
        if(value) {
            let index = value.indexOf(this.timeSign);
            let time = +value.slice(0, index); // 获取时间戳
            // 没过期
            if(new Date(time).getTime() > new Date().getTime() || time == 0) {
                value = value.slice(index + timeSignLen);
            } else { // 过期
                value = null,
                status = this.status.TIMEOUT;
                this.remove(key); // 同时删除
            }
        } else {
            status = this.status.FAILURE;
        }

        result = {
            status: status,
            value: value
        }
        callback && callback.call(this, result.status, result.value);
        return result;
    }
    /**
    * 删除数据
    * @params {string} key 数据字段标识
    * @params {function} callback 回调函数
    */
    remove(key, callback) {
        // 默认失败
        let status = this.status.FAILURE,
            value = null;
        key = this.getKey(key);

        try {
            value = this.storage.getItem(key);
        } catch(e) {}
        if(value) {
            this.storage.removeItem(key);
            status = this.status.SUCCESS;
            callback && callback.call(this, status, status > 0 ? null :
             value.slice(value.indexOf(this.timeSign) + this.timeSign.length))
        }
    }
}
````