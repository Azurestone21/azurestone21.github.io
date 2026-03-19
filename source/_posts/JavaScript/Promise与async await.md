---
title: Promise 与 async/await
date: 2024-2-19 21:18:03
categories: [JavaScript]
tags: [JavaScript, 面试题]
---

## Promise

Promise 是一种异步编程的解决方案，用于解决回调地狱问题，其特点是链式调用和状态机制。

Promise 的构造函数是同步执行的，方法 then、catch、finally 是异步执行的。

Promise 的状态只能变一次。

### Promise 三种状态

- Pending 等待
- Fulfilled 完成
- Rejected 失败

> 状态只能由 Pending --> Fulfilled 或者 Pending --> Rejected，且一但发生改变便不可二次修改。
>
> Promise中的异步错误（例如setTimeout抛错）不会改变状态。

### 实例方法

#### then()

参数是两个函数，处理成功（resolve）和失败（reject）的回调函数

#### catch()

参数是一个函数，定义一个 Promise 被拒绝时的回调函数

#### finally()

参数是一个函数，定义一个成功或拒绝时都运行的回调函数

#### then 的 err回调 和 catch回调

**catch在then之后**

1. `then`写了`err`回调，则错误由`err`回调解决
2. `then`没写`err`回调，则错误由`catch`解决
3. 如果`err`回调也抛出错误，则错误由`catch`解决
4. 如果`catch`之前有多个`then`，只要有一个`then`抛出错误，就会直接走到`catch`
5. 同理，只要`catch`之前有一个`err`回调，则错误由`err`回调处理，并且中间的`then`不会被执行，但是`err`后面的`then`的`success`会被执行

**catch在then之前**

1. 错误由`catch`解决，但是`then`的`success`也会执行
2. 可以在`then`之后继续写`catch`，处理后续`then`抛出的错误

### 静态方法

#### Promise.all

- 参数：接收一个 Promise 对象的数组。
- 当所有的 Promise 都成功，才会返回成功，有一个失败，就会返回失败。

`Promise.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```js
const p = Promise.all([p1, p2, p3]);
```

`p`的状态由`p1`、`p2`、`p3`决定

（1）只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。

（2）只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

> **注意，如果作为参数的 Promise 实例，自己定义了`catch`方法，那么它一旦被`rejected`，并不会触发`Promise.all()`的`catch`方法。**

> 理解：p1、p2、p3 有一个报错，p 就会报错

参考<https://es6.ruanyifeng.com/#docs/promise#Promise-all>

#### Promise.race()

- 参数：接收一个 Promise 对象的数组。
- 其中任何一个 Promise 的状态确定（成功或拒绝），就会调用 race 的回调函数。

#### Promise.any()

- 参数：接收一个 Promise 对象的数组。
- 只要其中有一个 Promise 成功执行，就会返回已经成功执行的 Promise 的结果。
- 如果全部为 rejected 状态，则会到最后的 promise 执行完，全部的 promise 返回到异常函数 catch 中。

### Promise 解决了什么问题

4. 通过 then 方法实现链式回调，避免回调地狱。
5. 通过 Promise.all 方法实现同时发起多个异步请求。
6. 通过 Promise.race 方法实现等待多个异步请求的结果。

## async/await

async/await 是一种处理异步操作的语法，基于ES6的 promise 和 generator 实现

**async**

- async 是“异步”的简写。
- async 是声明一个函数是异步的，返回一个`promise`对象。
- async 函数是 Generator 函数的语法糖（底层实现）。

**awiat**

- awiat 是等待一个异步方法的执行，只能出现在 async 里面。
- await可以直接接收到 promise 的结果，不需要使用then()。
- awiat 可以用于等待一个 async 函数的返回值，也可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量的。

## promise 与 async/await 的原理

### promise

### async/await

- async 函数执行结果返回的是一个Promise
- async 函数就是将 [Generator](./Generator.md) 函数的星号（\*）替换成 async，将 yield 替换成await
- async/await 就是 [Generator](./Generator.md) 的语法糖，其核心逻辑是迭代执行next函数

## async/await 与 Promise 的差别?

1. 语法：`async/await` 语法更简洁，代码易维护。Promise 需要使用 `then` 和 `catch` 方法来处理结果和错误，语法上较为冗长。
2. 错误处理：在 `async/await` 中，可以直接使用 `try...catch` 来捕获错误，而在 Promise 中需要使用 `catch` 方法。
3. 代码流程：`async/await` 可以使异步代码看起来更像同步代码，更容易阅读和理解。Promise 的代码流程则较为不连贯。

## 手写 Promise

```javascript
const PADDING = "padding";
const FULFILLED = "fulfilled";
const REJECTED = "rejected";

class myPromise {
  #status = PADDING; // Promise的状态
  #result = undefined; // Promise返回的结果
  #handler = []; // 保存Promise的then
  // 构造函数，立即执行
  constructor(executor) {
    const resolve = (data) => {
      this.#changeStatus(FULFILLED, data);
    };
    const reject = (reason) => {
      this.#changeStatus(REJECTED, reason);
    };
    try {
      executor(resolve, reject);
    } catch (res) {
      reject(res);
    }
  }
  // 改变Promise状态，一旦改变不会再变
  #changeStatus(status, result) {
    if (this.#status != PADDING) return;
    this.#status = status;
    this.#result = result;
    this.#run(); // 用于异步修改状态
  }

  // 利用Promise A+ 规范判断是否是Promise
  #isPromiseLike(value) {
    if (
      value !== null &&
      (typeof value === "object" || typeof value === "function")
    ) {
      return typeof value.then === "function";
    }
    return false;
  }

  // 在微队列中执行
  #runMicroTask(func) {
    if (typeof process === "object" && typeof process.nextTick === "function") {
      console.log("node环境");
      process.nextTick(func);
    } else if (typeof MutationObserver === "function") {
      console.log("浏览器环境");
      const ob = new MutationObserver(func);
      const textNode = document.createTextNode("1");
      ob.observe(textNode, {
        characterData: true,
      });
      textNode.data = "2";
    } else {
      console.log("无环境");
      setTimeout(func, 0);
    }
  }

  #runOne(callback, resolve, reject) {
    this.#runMicroTask(() => {
      // 如果then传入的不是函数，直接返回
      if (typeof callback !== "function") {
        const settled = this.#status === FULFILLED ? resolve : reject;
        settled(this.#result);
        return;
      }
      // 如果then传入的是函数，直接回调函数
      try {
        const data = callback(this.#result);
        if (this.#isPromiseLike(data)) {
          data.then(resolve, reject);
        } else {
          resolve(data);
        }
      } catch (err) {
        reject(err);
      }
    });
  }

  #run() {
    if (this.#status === PADDING) return;
    // 把每个then都执行一遍
    while (this.#handler.length) {
      const { onFulfilled, onRejected, resolve, reject } =
        this.#handler.shift();
      if (this.#status === FULFILLED) {
        this.#runOne(onFulfilled, resolve, reject);
      } else {
        this.#runOne(onRejected, resolve, reject);
      }
    }
  }

  // then 方法
  then(onFulfilled, onRejected) {
    return new myPromise((resolve, reject) => {
      // then方法会调用多次，需要用数组存起来
      this.#handler.push({
        onFulfilled,
        onRejected,
        resolve,
        reject,
      });
      this.#run();
    });
  }
}

// const p = new myPromise((resolve, reject) => {
//   // resolve(123);
//   // reject(456);

//   setTimeout(() => {
//     resolve(123);
//   }, 1000);

//   // 异步错误不会改变Promise状态，Promise捕获不到
//   // setTimeout(() => {
//   //   throw 123
//   // }, 0)
// });
setTimeout(() => {
  console.log(1);
}, 0);
new myPromise((resolve, reject) => {
  resolve(2);
}).then((data) => {
  console.log(data);
});
console.log(3);
```
