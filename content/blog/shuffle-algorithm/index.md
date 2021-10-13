---
title: 洗牌算法
date: "2021-10-13 10:32:32"
description: 洗牌算法是我们常见的随机问题，在玩游戏、随机排序时经常会碰到。具体就是使原数组的某个数在打散后的数组中的每个位置上等概率的出现。
tags:
  - 算法
---

洗牌算法是我们常见的随机问题，在玩游戏、随机排序时经常会碰到。具体就是使原数组的某个数在打散后的数组中的每个位置上等概率的出现。

算法步骤为：

1.  建立一个数组大小为 n 的数组 arr，分别存放 1 到 n 的数值
2.  生成一个从 0 到 n - 1 的随机数 x
3.  输出 arr 下标为 x 的数值，即为第一个随机数
4.  将 arr 的尾元素和下标为 x 的元素互换
5.  同 2，生成一个从 0 到 n - 2 的随机数 x
6.  输出 arr 下标为 x 的数值，为第二个随机数
7.  将 arr 的倒数第二个元素和下标为 x 的元素互换

```javascript
let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

function transformArr(arr) {
  let len = arr.length
  let random = Math.floor(Math.random() * len) + 1

  while (len) {
    let target = arr[random]
    arr[random] = arr[len - 1]
    arr[len - 1] = target
    len--
  }
  return arr
}

console.log(transformArr(arr))
```
