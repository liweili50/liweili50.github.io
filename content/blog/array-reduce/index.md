---
title: 数组的reduce方法
date: "2022-02-20"
description: "reduce() 方法对数组中的每个元素执行一个由您提供的reducer函数(升序执行)，将其结果汇总为单个返回值。"
tags:
  - reduce
  - 算法
  - 面试
---

reduce() 方法对数组中的每个元素执行一个由您提供的reducer函数(升序执行)，将其结果汇总为单个返回值。

reducer 函数接收4个参数:
Accumulator (acc) (累计器)
Current Value (cur) (当前值)
Current Index (idx) (当前索引)
Source Array (src) (源数组)
您的 reducer 函数的返回值分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

以上是MDN上对reduce的部分介绍，最近遇到了几个与之相关的面试题，记录一下。

### 求笛卡尔积

```javascript
const input = [
  ['e1', 'e2'],
  ['e4', 'e5'],
  ['e6', 'e7']
]
const calculate = input => {
  return input.reduce((acc, curr, index) => {
    if (index === 0) {
      return acc
    } else {
      let arr = []
      acc.forEach(t => {
        curr.forEach(item => {
          arr.push(t + item)
        })
      })
      return arr
    }
  })
}
console.log(calculate(input))
```

### 把a.b.c.d转换成相应的嵌套对象

```javascript
let str = "a.b.c.d";

function transform() {
  let arr = str.split(".");
  let obj = {};
  arr.reduce((prev, current) => {
    if (typeof prev === "string") {
      obj[prev] = {};
      obj[prev][current] = {};
      return obj[prev][current];
    } else {
      prev[current] = {};
      return prev[current];
    }
  });
  return obj;
}

let result = transform();
```

### 请你完成一个safeGet函数，可以安全的获取无限多层次的数据

```javascript
// 请你完成一个safeGet函数，可以安全的获取无限多层次的数据，一旦数据不存在不会报错，会返回 undefined，例如
var data = { a: { b: { c: 'yideng' } } }
safeGet(data, 'a.b.c') // => yideng
safeGet(data, 'a.b.c.d') // => undefined
safeGet(data, 'a.b.c.d.e.f.g') // => undefined

const safeGet = (o,path) => {
    try {
        return path.split('.').reduce((o,k) => o[k],o)
    } catch (error) {
        return undefined
    }
}
```