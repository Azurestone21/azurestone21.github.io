---
title: JavaScript中的this
date: 2023-08-24 21:12:32
category: [JavaScript]
tags: [JavaScript]
---

this 是函数在运行时的一个内部对象，只在函数内部使用，指向调用它的对象

this 总是返回一个对象。

只要函数被赋给另一个变量，this的指向就会变。

## 使用场景

1. 全局环境使用this，它指的就是顶层对象window。
2. 构造函数中的this，指的是实例对象。
3. 如果对象的方法里面包含this，this的指向就是方法运行时所在的对象。该方法赋值给另一个对象，就会改变this的指向。

**注意**

1. 如果this所在的方法不在对象的第一层，这时this只是指向当前一层的对象，而不会继承更上面的层。
2. 如果这时将嵌套对象内部的方法赋值给一个变量，this会指向全局对象。
3. 避免多层使用this，因为this指向不确定。
4. 内层的this不指向外部，而指向顶层对象。
5. 可以固定this，例如var that = this，内层函数调用that，这样不会发生this指向的改变。
6. 避免数组处理方法（如map、foreach）中的 this。
7. 避免回调函数中的 this。

```javascript
var o = {
  v: "hello",
  p: ["a1", "a2"],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + " " + item);
    });
  },
};
o.f();
// undefined a1
// undefined a2

// 使用变量固定this
var o = {
  v: "hello",
  p: ["a1", "a2"],
  f: function f() {
    var that = this;
    this.p.forEach(function (item) {
      console.log(that.v + " " + item);
    });
  },
};
o.f();
// hello a1
// hello a2

// 将this当作foreach方法的第二个参数，固定它的运行环境。
var o = {
  v: "hello",
  p: ["a1", "a2"],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + " " + item);
    }, this);
  },
};
o.f();
// hello a1
// hello a2
```

## 绑定 this 的方法

绑定方法有默认绑定、隐式绑定、new、显示绑定，优先级：new > 显示 > 隐式 > 默认

1. 默认绑定：一般是在全局定义方法后直接调用，this 指向 window，严格模式下不会默认绑定，this 是 undefined
2. 隐式绑定：一般是作为某个对象的调用方法，this 指向上级的对象
3. new 绑定：通过构建函数 new 生成一个实力对象，this 指向这个对象
4. 显示绑定：一般是通过 call()、appiy()、bind() 改变调用对象，它们的第一个参数就是调用对象，this 指向第一个参数

### new 绑定

在构造函数new实例的过程中，this改变的过程

```javascript
function mynew(Func, ...args) {
  // 1.创建一个新对象
  const obj = {};
  // 2.新对象原型指向构造函数原型对象
  obj.__proto__ = Func.prototype;
  // 3.将构建函数的this指向新对象
  let result = Func.apply(obj, args);
  // 4.根据返回值判断，如果是原始值则被忽略，如果是返回对象，需要正常处理
  return result instanceof Object ? result : obj;
}
```

### 显示绑定

#### call()

- 参数个数不定
- 第一个参数是一个对象。如果参数为空、null 和 undefined，则默认传入全局对象。
- 是立即执行函数

```javascript
var n = 123;
var obj = { n: 456 };
function a() {
  console.log(this.n);
}
a.call(); // 123
a.call(null); // 123
a.call(undefined); // 123
a.call(window); // 123
a.call(obj); // 456
```

#### apply()

- apply() 和 call() 唯一的区别就是，它接收一个数组作为函数执行时的参数。
- 第一个参数是 `this` 所要指向的那个对象，如果设为 `null` 或 `undefined`，则等同于指定全局对象。
- 第二个参数则是一个数组。
- 是立即执行函数

```javascript
function f(x, y) {
  console.log(x + y);
}
f.call(null, 1, 1); // 2
f.apply(null, [1, 1]); // 2
```

#### bind()

- 用于将函数体内的this绑定到某个对象，然后**返回一个新函数**。
- 第一个参数是 `this` 所要指向的那个对象。
- 后面传入的也是一个参数列表(但是这个参数列表可以分多次传入)。

```javascript
function fn(...args) {
  console.log(this, args);
}
let obj = {
  myname: "张三",
};

const bindFn = fn.bind(obj); // this 也会变成传入的obj ，bind不是立即执行需要执行一次
bindFn(1, 2); // this指向obj
fn(1, 2); // this指向window
```

#### call、apply、bind 的区别

1. 相同点是都可以用来改变 this 的指向（上下文），不同点是传参方式不太相同以及返回不同
2. call( ) 是接收一个及其以上的参数，第一个参数表示 this 要指向的对象，其余参数表示调用函数需要传入的参数，返回调用函数的返回结果，属于立即执行函数
3. apply( ) 是接收两个参数，第一个参数表示 this 要指向的对象，第二参数表示调用函数需要传入的参数所组成的数组，返回调用函数的返回结果，属于立即执行函数
4. bind( ) 是接收一个及其以上的参数，和 call()一致，但是其返回是一个函数，而不是调用函数的返回结果
