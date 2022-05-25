---
title: TypeScript协变和逆变
tags:
- "TypeScript"
- "学习笔记"
- "基础概念"

categories: 
 - TypeScript
 
keywords: "TypeScript, 协变， 逆变"
cover: https://cdn.thenewstack.io/media/2022/01/10b88c68-typescript-logo.png

---
# 协变与逆变

## 协变和逆变是什么？

我们先来看看维基百科的解释：
> 协变与逆变（Covariance and contravariance）是在计算机科学中，描述具有父/子型别关系的多个型别通过型别构造器、构造出的多个复杂型别之间是否有父/子型别关系的用语。

简单的说，协变和逆变是一种关系

## 如何解释协变与逆变

### 背景

我们先假设有以下这几种类型以及关系：

`Animal`  >=  `Dog` >=  `Greyhound（灰狗）` 

以上的类型规定，很显然 灰狗属于狗，狗属于动物，那我们有以下的几种函数的类型

`(Animal) → Animal`

`(Dog) → Dog`

`(Greyhound) → Greyhound`

假设现在有一个函数`g`，他的参数为`(Dog) → Dog`，即：

```typescript
function g(animal: Animal, dogCallback: (Dog)=> Dog): string{
  // some code to call dogCallback(dog) , dog is Dog type
}
```
我们要调用函数`g`，在第二个参数`dogCallback`的位置传入一个函数，我们暂且叫它`f`

### 提出问题

我们要传入何种类型的`f`，才能使得函数`g`接收的参数`dogCallback`的类型是安全的呢？

### 思考问题

我们假设使用`(Greyhound) → Greyhound`类型，记为`f:(Greyhound) → Greyhound`

从上面`f`的返回值类型上看，`Greyhound`是属于`Dog`，返回值类型是安全，因为灰狗是狗的子类型，我可以调用返回值上是`Dog`的所有公共属性和方法，没有什么问题。

我们看看参数类型部分是否安全：

`g`的参数上定义第二个参数`dogCallback`

`g`传入的参数`dog`的类型是`Dog`，涵盖所有狗的类型

传入的 `f` 函数的参数只被定义为 `Greyhound` 类型

在函数`g`中调用`f`时，`Greyhound` 类型的参数不能接收`Dog`类型的变量，传入变量`dog`的值可能是`Greyhound`**之外**的类型，例如 `BlackDog`

所以，在参数中，`Dog` 到 `Greyhound` 不安全，因为`Dog`的值**可能**是狗，但不是灰狗，可能是其他类型的狗

### 解决问题

那传入什么类型的函数才是安全的呢？答案是（不止以下两种）：

`(Dog) → Greyhound `

`(Animal) → Greyhound`

在参数上，`Animal` 或者 `Dog` 可以将 `Dog` 的所有子类型包含在内，因此这是安全的


### 理解概念

返回值的类型要是返回类型，或其子类型，例如 返回类型定为`Dog`，返回值类型为`Dog`的子类型`Greyhound`，返回类型安全

参数类型 必须是 传入的参数值的类型，或其的子类型，例如参数要求的类型为`Dog`，传入的参数必须要包含`Dog`的所有子类型，例如`Animal`。 `Animal`包含了`Dog`，所以`Animal`到 `Dog` 是安全的。

但是传入的子类型不能为 `Greyhound` , 因为`Greyhound`  到`Dog`不安全，即`Dog` 类型可能会将其他非`Greyhound`的类型传进去

这样就意味着`(Animal → Greyhound) ≼ (Dog → Dog)`， 也就是`(Dog → Dog)`是`(Animal → Greyhound)`的子类型

比较好理解是从`Dog`到`Greyhound`，由多到少，很好理解，我们可以称这种变体为*协变*

`Animal` 到 `Dog`,这个变体过程由多变少， 我们称这个变体过程为*逆变*

## 总结

从宏观上来理解:

* 协变(covariant)：类型收敛

* 逆变(contravariant)：类型发散

在 TypeScript 中：

* 对象、类、数组和函数的返回值类型都是协变关系，而函数的参数类型是逆变关系

* 返回值类型是 **协变** 的，意味着如果`A`为`B`的子类型，则`(T) =>A `为`(T) => B`的子类型

* 参数类型是 **逆变** 的，意味着如果`A`为`B`的子类型， 则`(B)=> T`为`(A) => T`的子类型