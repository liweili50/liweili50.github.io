---
title: 滑动窗口算法
date: "2021-10-22 13:37:40"
description: 滑动窗口算法可以用以解决数组/字符串的子元素问题，它可以将嵌套的循环问题，转换为单循环问题，降低时间复杂度。
tags:
  - 算法
  - leetCode
---

**算法核心思路**

用左右指针维护一个窗口（连续的子数组/子串），根据题目在遍历数组或者字符串的时候动态调整两个指针（一般都是++），遇到可行解就进行记录。

```javascript
let left = 0
let right = 0
while (right < target.length) {
  //未满足条件时
  right++
  while (condition) {
    //满足条件时
    left++ //收缩
  }
}
```

**leetcode 算法：求无重复字符的最长子串**

1. 给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

```
输入: s = "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

2. 完整代码：

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function (s) {
  let right = 0
  let left = 0
  let targetStr = ""
  while (right !== s.length) {
    right++
    let index = s.substring(left, right).indexOf(s[right])
    if (s.substring(left, right).length > targetStr.length) {
      targetStr = s.substring(left, right)
    }
    if (index !== -1) {
      left = left + index + 1
    }
  }
  return targetStr.length
}
```
