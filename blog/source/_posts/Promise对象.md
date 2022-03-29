---
title: Promise对象
date: 2022-03-28 13:48:31
tags: [js,es6]
---

`Promise`对象用来表示一个异步操作的最终完成（或失败）及其结果值。异步编程的一种解决方案。

`Promise`有三种状态：`pending`进行中、`fulfilled`已成功、`rejected`已失败

![](/images/promise.jpg)

用法：

```js
function fn(num){
    return new Promise((resolve,reject)=>{
    	if (typeof num == 'number') {
            resolve(num);
        } else {
            reject('TypeError');
        }
	})
}
// 两秒之后输出成功
fn(2).then(res=>{
    console.log(res)
})
```

`Promise.all()`的用法 ，接收一个数组参数，并行执行异步操作，等到都执行完之后才会放到then里面。都成功之后执行`resolve`回调返回的是一个数组，如果有任何一个`reject`回调执行或者输入不合法会立即抛出错误，并且`reject`的是第一个抛出的错误信息

```js
let promise1 = Promise.resolve("qizhenyuan");
let promise2 = new Promise((resolve,reject)=>{
    resolve("很帅")
})
Promise.all([promise1,promise2]).then(values=>{
    console.log(values) // ['qizhenyuan', '很帅']
})

// 有reject
let promise3 = Promise.resolve("qizhenyuan");
let promise4 = new Promise((resolve,reject)=>{
    reject("111")
})
let promise5 = new Promise((resolve,reject)=>{
    resolve("222")
})
let p = Promise.all([promise3,promise4,promise5])
p.catch() // Promise {<rejected>: '111'}
```

`Promise.allSettled()`的用法，接收一个数组参数，返回一个在所有给定的promise都已经`fulfilled`或者`rejected`后的promise,并带有一个对象数组，每个对象表示对应的promise结果

```js
const promise1 = Promise.resolve(2);
const promise2 = new Promise((resolve,reject)=>{
    reject(22)
});
const promiseList=[promise1,promise2]
Promise.allSettled(promiseList).then(result=>{
    console.log(result) 
})
// output:
// [{status:"fulfilled",value:2},{status:"rejected",reason:22}]
```

`Promise.any()`的用法，接收一个数组参数，用于返回第一个成功的`promise`。只要有一个`Promise`成功方法就会终止。如果所传入的`promises`都失败，将返回异步失败。

```js
const promise1 = Promise.resolve(2);
const promise2 = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve(3)
    })
});
Promise.any([promise1,promise2]).then(res=>{
    console.log(res) // 2
})

const promise3 = Promise.reject(3);
const promise4 = new Promise((resolve,reject)=>{
    reject(5)
});
Promise.any([promise3,promise4]).then(res=>{
    console.log(res) 
}).catch(err=>{
    console.log(err)  //AggregateError: All promises were rejected
})

```

`Promise.race()`的用法，接收一个数组参数，只要有一个`Promise`成功或者失败，返回的就是成功或者失败。如果迭代包含一个或多个非承诺值和/或已解决/拒绝的承诺，则` Promise.race` 将解析为迭代中找到的第一个值。

```js
const promise1 = new Promise((res,rej)=>{
    res("111")
})
const promise2 = new Promise((res,rej)=>{
    rej(new Error("错误"))
})
Promise.race([promise1,promise2]).then(res=>{
    console.log(res)
}).catch(err=>{
    console.log(err)
})
//output:111
```

手写Promise

```js
const PENDING = "pending";
const FULFILLED = "fulfilled";
const REJECTED = "rejected";
function Promise(executor) {
    var _this = this;
    this.status = PENDING;
    this.value = undefined; //成功结果
    this.reason = undefined; //失败原因
    this.onFulfilled = []; //成功回调
    this.onRejected = []; //失败回调
    function resolve(value) {
        if (_this.status === 'pending') {
            _this.status = FULFILLED;
            _this.value = value;
            _this.onFulfilled.forEach(fn => fn(value))
        }
    }
    function reject(reason) {
        if (_this.status === 'pending') {
            _this.status = REJECTED;
            _this.reason = reason;
            _this.onRejected.forEach(fn => fn(reason))
        }
    }
    try {
        executor(resolve, reject)
    } catch (e) {
        reject(e)
    }
}
//`then`方法必须返回一个promise对象 promise.then(resFn,rejFn)
Promise.prototype.then = function (onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled == "function" ? onFulfilled : undefined;
    onRejected = typeof onRejected == "function" ? onRejected : undefined;
    return new Promise((resolve, reject) => {
        if (this.status == FULFILLED) {
            onFulfilled(this.value)
        } else if (this.status == REJECTED) {
            onRejected(this.reason)
        } else {
            this.onFulfilled.push(onFulfilled(this.value));
            this.onRejected.push(onRejected(this.reason));
        }
    })
}

module.exports = Promise
```

