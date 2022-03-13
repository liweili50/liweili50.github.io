---
title: 排序算法
date: "2022-03-13"
description: "记录两种常见的排序算法。"
tags:
  - 算法
  - 快速排序
---

### 冒泡排序

大致流程：依次比较相邻的两个数,正序则不动,倒序则交换位置,如此循环,直到整个数组为有序为止。

```javascript
function bubble(arr) {
  for (let i = 0; i < arr.length - 1; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        let temp = arr[j]
        arr[j] = arr[j + 1]
        arr[j + 1] = temp
      }
    }
  }

  return arr
}
```

### 快速排序

快速排序就是个二叉树的前序遍历，快速排序的逻辑是，若要对 nums[lo..hi] 进行排序，我们先找一个分界点 p，通过交换元素使得 nums[lo..p-1] 都小于等于 nums[p]，且 nums[p+1..hi] 都大于 nums[p]，然后递归地去 nums[lo..p-1] 和 nums[p+1..hi] 中寻找新的分界点，最后整个数组就被排序了。

```javascript
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr
  }
  let index = Math.floor(arr.length / 2)
  let target = arr.splice(index, 1)[0]

  let left = []
  let right = []
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < target) {
      left.push(arr[i])
    } else {
      right.push(arr[i])
    }
  }

  return quickSort(left).concat([target], quickSort(right))
}
```
