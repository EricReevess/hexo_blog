---
title: TypeScript 泛型
tags:
- "TypeScript"
- "学习笔记"
- "基础概念"

categories: 
 - TypeScript
 
keywords: "TypeScript, 泛型, infer, extends"
cover: https://cdn.thenewstack.io/media/2022/01/10b88c68-typescript-logo.png

---

## 泛型条件类型

使用三元运算符，类型A需要满足类型B的约束，返回满足条件的类型，即：

```typescript
interface hhh = T extends U ? X: Y;
```

这里使用`extends`关键字检查泛型T是否满足泛型U的类型约束，是则返回类型X，否则返回类型Y

### infer关键词

微软ts官方给出的解释原文/译文为：

> Within the `extends` clause of a conditional type, it is now possible to have `infer` declarations that introduce a type variable to be inferred. Such inferred type variables may be referenced in the true branch of the conditional type. It is possible to have multiple `infer` locations for the same type variable.

> `infer` 关键词常在条件类型中和 `extends` 关键词一同出现，表示将要推断的类型，作为类型变量可以在三元表达式的 True 部分引用。而 `ReturnType` 正是使用这种方式提取到了函数的返回类型。

`Infer`关键词主要是推断某个**满足约束**的 泛型变量的类型，例如：

```typescript
type SomeType<T> = T extends (infer U)[] ? U: T

SomeType<string[]> // string
SomeType<string> // string
```

`U`是由传入参数推倒出来的类型：即如果 `T` 遵循 `any[]` 这样一个结构，那么`U`即为传入的数组的item的`类型`

泛型SomeType的表达式含义为：如果传入的类型符合 `any`类型的数组 的约束，则返回 `any` 的类型，否则返回自身的类型`any`

`(infer U)[]`可以理解为`[infer U, infer U,infer U, ...]`这样的数组

infer同样也适合联合类型，例如：对于固定索引值类型的数组

```typescript
SomeType<[number, string]> // U = number | string
```

其实这不是一个很好的例子，需要从需求与设计方面来理解为什么需要infer

### 理解infer

设想一个使用ts编写的函数的需求： 需要一个函数，接受的参数为任何类型的数组，返回该数组的第一项(只能是第一项)

其实不使用infer，写法也很简单：

````typescript
function getFirstItem<T>(array: T[]): T {
  return array[0];
}

type GetFirstParamType<T> = T extends (...args: infer R) => any ? R[0] : never
````

但是如果传入的参数是一个对象，假设需要一个函数，返回对象拥有一个`onChange`的回调函数这个`key`的返回值，普通`type`泛型显然做不到，因为T是一个调用时传入的类型整体，不具备在调用时`Pick T`子类型的能力，例如：

```typescript
type SubType<T> = { 
  objType: T;
}

SubType<{name: string}>
```

`T = {name: string}`, 即给`objType` 的类型定义为 `{name: string}`

但是如果在上方`SubType`中当我们传入`T = {name: string}`, `objType` 需要为`{name: string}`中`name`的类型，此时是做不到的，因为泛型是一个不可拆分的整体。

使用`infer`，可以解决获取`name`的类型的问题，上述的对象的`onChange`的属性的返回值类型也能使用相同的方式获取

```typescript
type SubType<T> = T extends { name: infer U } ? U: T
type SubType<T> = T extends { onChange: () => infer U } ? U: T
```

`Infer` 还可以用于元组转 联合类型，意味着可以将类型放在元组（元素可以是不同类型的数组）中，然后将其转换为联合类型 `xxx | xxx | xxx`

```typescript
const typeTuple = [1, '12', [1], false];

type TypeTupleToUnion<T> = T extends (infer U)[] ? U: any;

type unionTuple = union<typeof typeTuple> 
// type unionTuple = string | number | number[] | boolean

```

## 常用typescript类型应用

### 得到返回值类型：

```typescript
// 得到函数第一个参数等类型
type GetFirstParamType<T> = T extends (...args: infer R) => any ? R[0] : never

// 得到函数的返回值类型
type GetFuncReturnType<T> = T extends (...args: any[]) => infer U ? U : never
```

### 忽略属性：

```typescript
type Exclude<T, U> = T extends U ? never: T

type OmitProps<T, K extends keyof T> = {
  [P in Exclude<keyof T, K>]: T[P]
} 

type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}

type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>

// extends 执行分配律
type MyExclude<T, E> = T extends E ? never: T

type C = MyExclude<'a' | 'b' | 'c', 'c' | 'd'>   // 'a' | 'b'

// C eq D code 
type D = ('a' extends ('c' | 'd') ? never : 'a') | 
         ('b' extends ('c' | 'd') ? never : 'b') | 
         ('c' extends ('c' | 'd') ? never : 'c'); 

type MyExtract<T, U> = T extends U ? T: never;  
```

### 取出Class 构造器中的参数组成元组

```typescript
type MyConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer U) => any ? U : never;

class Foo {
  constructor (a: string, b: number, c: boolean) {}
}

type C = MyConstructorParameters<typeof Foo> 
// [a: string, b: number, c: boolean]

class Foo {}
// typeof Foo 等价于 new () => any
```

### TypeScript 4.1 新特性：模板字符串类型

```typescript
type EventName<T extends string> = `${T}Changed`;
// 模板字符串符合排列组合
type T3 = `${'top' | 'bottom'}-${'left' | 'right'}`;  

// 增加了4个字符处理的关键字
type Cases<T extends string> = `${uppercase T} ${lowercase T} ${capitalize T} ${uncapitalize T}`

```

注意⚠️

* 字符串模板中的 `infer` 会一直匹配到字符串的结尾，比如用 `${infer T}x` 去匹配 `'abcxxx'` 会把 `T` 推断为 `'abcxx'`。

2. 字符串模板中两个 `infer` 相邻，第一个 `infer` 只会推断出单个字符，这有助于一些递归操作，比如 `${infer A}${infer B}` 去推断 `abcd`，`A` 会推断为 `a`，而 `B` 会推断为 `bcd`

实现元组到联合类型 [x,x,x] => x | x | x

```typescript
type TupleToUnion<T extends any[]> = T extends [infer U, ...infer R] ? U | TupleToUnion<R> : never;
```

### 实现Join

```typescript
type Join<T extends (string | number | boolean | bigint)[], D extends string> =
    T extends [] ? '' :
    T extends [unknown] ? `${T[0]}` :
    T extends [unknown, ...infer U] ? `${T[0]}${D}${Join<U, D>}` : string;

type T30 = Join<[1, 2, 3, 4], '.'>;  // '1.2.3.4'
type T31 = Join<['foo', 'bar', 'baz'], '-'>;  // 'foo-bar-baz'
```

### 判断是否空类型

```typescript
type IsEmptyType<T> = T extends Record<string, string> ?
  [keyof T] extends [never] ? true: false 
  : false
```

### 判断是否为any类型

```typescript
type IsAny<T> = 0 extends 1 & T ? true : false;
```

### 数字字符串转换为number   利用模板字符串判断0值

```typescript
type ToNumber<T extends string, N extends number[] = []> = `${N['length']}` extends T ? N['length'] : ToNumber<T, [1, ...N]>
```
