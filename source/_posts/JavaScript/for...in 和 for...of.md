---
title: for...in 和 for...of
date: 2023-10-19 21:18:03
categories: [JavaScript]
tags: [JavaScript, 面试题]
---

## for-in

for-in 循环是一种迭代**对象属性**的方法。它可以用于遍历对象中的所有可枚举属性，包括从**原型链继承的属性**。通常，我们使用 for-in 循环来遍历对象的键名。

```javascript
const person = {
  name: "John",
  age: 30,
  gender: "male",
};

for (const key in person) {
  console.log(key, person[key]);
}
```

> for-in 循环并不是按照对象属性在对象中的顺序迭代的。这是因为 JavaScript 中的对象属性没有固定的顺序。

for-in 循环会遍历对象的原型链，因此可能会迭代到不是自身属性的属性。为了避免这种情况，可以使用 **hasOwnProperty** 方法来检查属性是否为对象自身的属性。

```javascript
const person = {
  name: "John",
  age: 30,
};

for (const key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key, person[key]);
  }
}
```

## for...of

for-of 循环是一种迭代**可迭代对象**的方法。可以用于遍历**数组、字符串、Map、Set、TypedArray** 等可迭代对象。通常使用 for-of 循环来遍历**数组或字符串**的值。

for-of 循环**不适用于迭代对象属性**。会抛出 TypeError 异常。

```javascript
const arr = [1, 2, 3];

for (const value of arr) {
  console.log(value);
}
```

## 总结

1. 迭代对象的内容不同
    1. for-in 循环迭代对象的是**键名**。
    2. for-of 循环迭代对象的是**值**。
2. 迭代对象的类型不同
    1. for-in 循环适用于遍历对象，包括**普通对象、数组、函数**等。
    2. for-of 循环适用于遍历**可迭代对象**，包括**数组、字符串、Map、Set、TypedArray** 等。
3. 迭代对象的顺序不同
    1. for-in 循环的迭代**顺序是不确定**的，因为对象的属性没有固定的顺序。
    2. for-of 循环的迭代**顺序是确定**的，因为可迭代对象的值是按照一定顺序排列的。
4. 迭代对象的原理不同
    1. for-in 循环遍历对象时，会遍历对象的**原型链**，并且会包含从原型链继承的属性。
    2. for-of 循环遍历的对象是可迭代对象，它们的值是**可枚举**的。
