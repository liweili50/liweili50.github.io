---
title: 求解连续数列
date: "2020-08-12T05:56:22.292Z"
description: 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
tags: - 算法
---

## 求解连续数列

已知连续正整数数列{K}=K1,K2,K3...Ki的各个数相加之和为S，i=N (0<S<100000, 0<N<100000), 求此数列K。输入描述:
输入包含两个参数，1)连续正整数数列和S，2)数列里数的个数N。 输出描述:  
如果有解输出数列K，如果无解输出-1

### 答案一：

```javascript
let solution = (target, len) => {
    let arr = new Array(len).fill(0).map((i, index) => index);
    let sum = eval(arr.join('+'));
    if ((target - sum) % len === 0) {
        const a = (target - sum) / len;
        return arr.map(i => i + a)
    }
    return -1
}
```
#### 复杂度分析：

- 时间复杂度：O(1)

- 空间复杂度：O(n)

### 答案二：
```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */

function solution(sum, n) {
    let arr = []
    let startNum = sum / n - (n - 1) / 2
    if (n % 2 !== 0 && sum % n === 0) {
        for (let i = 0; i < n; i++) {
            arr.push(startNum)
            startNum++
        }
        return arr
    } else if ((sum % n) * 2 == n) {
        for (let i = 0; i < n; i++) {
            arr.push(startNum)
            startNum++
        }
        return arr
    } else {
        return -1
    }
}
```
#### 复杂度分析：
- 时间复杂度：O(n)， 我们只遍历了包含有 n 个元素的列表一次。在表中进行的每次查找只花费 O(1)的时间。

- 空间复杂度：O(n)， 所需的额外空间取决于哈希表中存储的元素数量，该表最多需要存储 n 个元素。

