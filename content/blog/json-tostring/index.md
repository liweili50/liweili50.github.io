---
title: "实现一个函数可以转换JSON成字符串"
date: "2022-06-02 22:53:33"
description: "实现一个函数可以转换JSON成字符串，类似JSON.stringify"
tags:
  - 面试
  - 算法
---

参加声网的面试遇到一个算法，现场没能完成，略有遗憾，第一次遇到考察这个算法，心态没放好，也有时间方面的限制，私下完成以便记忆。

```javascript
// 实现一个函数可以转换JSON成字符串，类似JSON.stringify
const jsonBody = {
  a: 11,
  b: {
    b: 22,
    c: {
      D: 33,
      e: [44, 55, 66],
    },
    f: [],
    v: undefined,
  },
};

const jsonToString = function (jsonBody) {
  // 代码
  let str = "";
  if (Object.prototype.toString.call(jsonBody) === "[object Object]") {
    let arr = Object.keys(jsonBody)
      .map((key) => {
        if (jsonToString(jsonBody[key])) {
          return `"${key}":${jsonToString(jsonBody[key])}`;
        } else {
          return "";
        }
      })
      .filter((item) => item !== "");
    str = "{" + arr.join(",") + "}";
  } else if (Object.prototype.toString.call(jsonBody) === "[object Array]") {
    return `[${jsonBody}]`;
  } else if (
    typeof jsonBody === "undefined" ||
    typeof jsonBody === "function"
  ) {
    return "";
  } else {
    str = `${jsonBody}`;
  }
  return str;
};

console.log(jsonToString(jsonBody));
console.log(JSON.stringify(jsonBody));
```