---
title: javaScript 数据类型
date: "2020-06-24T013:51:33.508Z"
description: "JavaScript 中有八种基本的数据类型（译注：前七种为基本数据类型，也称为原始类型，而 object 为复杂数据类型）。"
---

## JavaScript 中有八种基本的数据类型（前七种为基本数据类型，也称为原始类型，而 object 为复杂数据类型）。

* number 用于任何类型的数字：整数或浮点数，在 ±253 范围内的整数。
* bigint 用于任意长度的整数。
* string 用于字符串：一个字符串可以包含一个或多个字符，所以没有单独的单字符类型。
* boolean 用于 true 和 false。
* null 用于未知的值 —— 只有一个 null 值的独立类型。
* undefined 用于未定义的值 —— 只有一个 undefined 值的独立类型。
* symbol 用于唯一的标识符。
* object 用于更复杂的数据结构。

我们可以通过 typeof 运算符查看存储在变量中的数据类型。

两种形式：typeof x 或者 typeof(x)。
以字符串的形式返回类型名称，例如 "string"
```javaScript
    typeof undefined // "undefined"

    typeof 0 // "number"

    typeof 10n // "bigint"

    typeof true // "boolean"

    typeof "foo" // "string"

    typeof Symbol("id") // "symbol"

    typeof Math // "object"  (1)

    typeof null // "object"  (2)

    typeof alert // "function"  (3)
```
最后三行可能需要额外的说明：

1. Math 是一个提供数学运算的内建 object。此处仅作为一个 object 的示例。
2. typeof null 的结果是 "object"。这其实是不对的。官方也承认了这是 typeof 运算符的问题，现在只是为了兼容性而保留了下来。当然，null 不是一个 object。null 有自己的类型，它是一个特殊值。再次强调，这是 JavaScript 语言的一个错误。
3. typeof alert 的结果是 "function"，因为 alert 在 JavaScript 语言中是一个函数。在 JavaScript 语言中没有一个特别的 “function” 类型。函数隶属于 object 类型。但是 typeof 会对函数区分对待。这不是很正确的做法，但在实际编程中非常方便。