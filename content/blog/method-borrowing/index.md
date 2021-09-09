---
title: javaScript 方法借用（method borrowing）
date: "2020-08-10T01:44:05.833Z"
description: "在JavaScript中，可以从其他对象借用方法来构建某些功能，而不必继承它们的所有属性和方法。就是我们从一个对象中获取一个方法，并在另一个对象的上下文中“调用”它"
---

## 从对象中借用方法

一个 function 即便是定义在一个对象中，作为对象的一个“方法”，它也只不过是一个普通的函数而已，跟其他函数没有任何区别。在执行时仍然需要为 this 绑定一个具体的对象。

```javaScript
var obj = {
    a: 1,
    foo: function f() {
        console.log(this.a);
    }
}

obj.foo(); //1

var g = obj.foo;
g();    //undefined

obj.foo.call(obj); //1
```

this 并不指 function 对象本身，也不是指 function 的作用域对象。而是在运行时绑定到特定的对象上。因此直接借用对象里的函数 foo，在非严格模式下没有明确绑定对象，this 会指向全局对象，所以输出 undefined。在严格模式下则会报错。

## 使用 "func.call"和 "func.apply" 设定上下文

javaScript 有一个特殊的内置函数方法 func.call(context, ...args)，它允许调用一个显式设置 this 的函数。
语法如下：

```javaScript
func.call(context, arg1, arg2, ...)
```

它运行 func，提供的第一个参数作为 this，后面的作为参数（arguments）

使用 func.call 的时候如果需要传递多个参数时要搭配扩展运算符

```javascript
func.call(this, ...arguments)
```

我们也可以使用 func.apply(this, arguments)代替，它运行 func 设置 this=context，并使用类数组对象 args 作为参数列表（arguments）。
call 和 apply 之间唯一的语法区别是，call 期望一个参数列表，而 apply 期望一个包含这些参数的类数组对象。

因此，这两个调用几乎是等效的：

```javascript
func.call(context, ...args) // 使用 spread 语法将数组作为列表传递
func.apply(context, args) // 与使用 call 相同
```

这里只有很小的区别：

- Spread 语法 ... 允许将 可迭代对象 args 作为列表传递给 call。
- apply 仅接受 类数组对象 args。
  因此，当我们期望可迭代对象时，使用 call，当我们期望类数组对象时，使用 apply。

对于即可迭代又是类数组的对象，例如一个真正的数组，我们使用 call 或 apply 均可，但是 apply 可能会更快，因为大多数 JavaScript 引擎在内部对其进行了优化。
将所有参数连同上下文一起传递给另一个函数被称为“呼叫转移（call forwarding）”。
这是它的最简形式：

```
let wrapper = function() {
  return func.apply(this, arguments);
};
```

当外部代码调用这种包装器 `wrapper` 时，它与原始函数 `func` 的调用是无法区分的。

## 方法借用（method borrowing）

在 JavaScript 中，可以从其他对象借用方法来构建某些功能，而不必继承它们的所有属性和方法。从上面的例子我们已经知道不能直接借用对象里的方法，这样会由于 this 的问题而出现意想不到的结果，所以要借助 func.call 或者 func.apply 实现。

1. 一个最常见的例子就是伪数组的转换

```
[].slice.call(arguments)
```

slice 方法内部的 this 就会被替换成 arguments，并循环遍历 arguments，复制到新数组返回，这样就得到了一个复制 arguments 类数组的数组对象.

2. 使用 Object.prototype.toString 方法来揭示类型

除了 `instanceof` 操作符用于检查一个对象是否属于某个特定的 class 之外，还可以借用 Object.prototype.toString 来检查变量的数据类型

```
let obj = {};

alert(obj); // [object Object]
```

一个普通对象被转化为字符串时为 [object Object],原因在于内建的 toString 方法。该方法可以被从对象中提取出来，并在任何其他值的上下文中执行。其结果取决于该值。

- 对于 number 类型，结果是 [object Number]
- 对于 boolean 类型，结果是 [object Boolean]
- 对于 null：[object Null]
- 对于 undefined：[object Undefined]
- 对于数组：[object Array]
- ……等（可自定义）

```javascript
let s = Object.prototype.toString

console.log(s.call([])) // [object Array]
console.log(s.call(123)) // [object Number]
console.log(s.call(null)) // [object Null]
console.log(s.call(console.log)) // [object Function]
```

还可以使用特殊的对象属性 Symbol.toStringTag 自定义对象的 toString 方法的行为。

例如：

```javascript
let user = {
  [Symbol.toStringTag]: "User",
}

console.log({}.toString.call(user)) // [object User]
```

## func.bind()

call apply bind 都有着改变 this 指向的功能，但与 call 和 apply 不同，bind 返回的是一个新的函数，你必须调用它才会被执行。

```javascript
const Animal = {
  name: "动物",
  say() {
    console.log(`大家好，我是${this.name}`)
  },
}

const Cat = {
  name: "大狸猫",
}

Animal.say.call(Cat)
// 大家好，我是大狸猫

Animal.say.apply(Cat)
// 大家好，我是大狸猫

Animal.say.bind(Cat)()
// 大家好，我是大狸猫
```

方法 `func.bind(context, ...args)` 返回函数 func 的“绑定的（bound）变体”，它绑定了上下文 this 和第一个参数（如果给定了）。
同样有个很常见的例子是简写`console.log()`

```javascript
let log = console.log.bind(console)
log("123") // 123
```
