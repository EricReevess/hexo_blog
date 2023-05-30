---
title: 探究函数柯里化
tags:
- "JavaScript"
- "柯里化"
- "基础概念"

categories: 
 - JavaScript
 
keywords: "JavaScript, 函数柯里化"
cover: https://wonderdevelop.com/wp-content/uploads/2023/02/What-is-group-selector-in-css-15.jpg

---

## 概念

闭包函数： 引用了另一个函数内变量的函数，造成另一个函数的上下文能继续保持在栈中，

高阶函数：参数包含函数，返回值为函数的函数，可以理解为函数工厂函数

函数柯里化：将一个接受n个参数的函数，转换为每次调用只接受不超过n个参数的函数，且最多需要n次调用完

> carriedFunc  =  carry(func)
>
> netxArgsFunc = carriedFunc(..partArgs)
>
> funcResult = carriedFunc(...fullArgs)

## 使用场景

* 参数复用，柯里化后的函数相当于预传参数的函数，这样避免和频繁输入参数
* 代码简洁，在不使用对象最为传参数的情况下，避免一次性传入过多的参数导致代码可读性变差
* 延迟计算，如果涉及到多个参数的实时输入计算，防止频繁计算出现非预期结果，柯里化可以在没有完全传入参数的情况下节省计算资源
* 动态创建特定函数，可以通过不同的参数进行动态返回函数

## 实现原理

要实现分分步传入参数，返回下一步的函数，使得最终步调用函数，需要使用`Function.prototype.bind()` 方法的特性：使用预备参数生成新函数。
`bind`函数第一个参数是决定新函数的`this`指针，第二个参数之后为参数列表，意味着将一个参数长度为`l`的函数`foo`，`bind`参数列表传入小于`l`的参数个数`n`，调用`bind`后，返回的新函数`fooo`的参数列长度`fooo.length`就为`l - n`，传入的参数会在调用新函数的时候作为预备参数

以上面的操作，可以写一个递归柯里化函数`carry`，该函数的参数为需要分步调用的函数`foo`，每次返回一个新函数`carriedFoo`，作为返回给使用者的调用函数，也就是柯里化后的函数

柯里化后的函数可以通过传入参数判断是直接调用，还是继续返回含参函数：

* 当一次性传入所有`foo`的参数时`args.length = foo.length`，直接返回结果

* 当使用者传入`carriedFoo` 函数的参数列表长度`args.length`小于原函数参数长度`foo.length`时，则使用`bind`将`args`中的参数作为预备参数生成一个新的函数`carriedFooWithArgs`，在将`carriedFooWithArgs`进行柯里化一次

```javascript
function carry(foo) {
  return function carriedFoo (...args){
    if(args.length < foo.length) {
      //boundFunc.length = foo.length - args.legnth
      const carriedFooWithArgs = foo.bind(null, ...args) // 将不完整的参数绑定至新函数，形成新的预备参数的函数 
      return carry(carriedFooWithArgs) // 参数没有传完，继续返回柯里化函数
    } else {
      foo(...args) // 参数传完了，最终调用
    }
  }
}

// 使用carry

const add = (n1, n2, n3) => n1 + n2 + n3

const carriedAdd = curry(add)

const result = add(1)(2)(3) // 6

```

## TypeScript 类型定义

柯里化的ts类型比较复杂，就连 lodash 里面那个类型也是定义了很多可能的情况， 没怎么看懂😅，所以不想写了

## 参考

[learn-advanced-typescript](https://hackernoon.com/learn-advanced-typescript-4yl727e6)