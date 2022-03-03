---
layout: post
title: TypeScript—泛型的使用
categories: [TypeScript]
description: TypeScript—泛型的使用
keywords: TypeScript
---


## 泛型的变量

### 泛型变量

首先，JS 和 ES6 没有泛型的概念，并且如果也没接触过包含后端的泛型，那么，理解泛型会比较困难。所以，这里九八最简单的了解下即可。

在使用泛型之前，我们需要作些前置工作，先来看一下任意参数类型的函数：

```ts
function info(msg: any) : any {
  return msg
}
console.log(info('Mr.Lee'))
```

这里为什么用 `any` 进行传参和返回类型，因为我们无法确定这两块的类型，但这样不再进行检查类型，和 JS 无差别了，违背了 TS 静态类型语言的初衷，我们需要在开发中使用强制类型让其在编译阶段就发现类型导致的错误而不是运行时，所以这里两个 `any` 基本上没有上面意义，就是 JS 了

在类型不明确的时候，我们可以使用泛型来解决这个问题，提高了灵活性

泛型最核心的部分就是下面例子中的 `<T>`，`T` 就是泛型变量，将类型赋给它

```ts
// 函数， T 是泛型变量（变量名自定义，也可以是 U、P、K 都行）
function info<T>(msg: T): T {
  return msg
}
// 自动推断
console.log(info('Mr.Lee'))
// 强制确定类型
console.log(info<string>('Mr.Lee'))
```

这里的 `T` 相当于未知的类型，当函数调用 `<string>` 时， T 就是 string

也就而已看出泛型可以在调用的时候来指定类型，大大提高了灵活性

当设置了 `info<T>` 时， `(msg: T)` 也必须是 T，不能单独使用

```ts
function info<T>(msg: string): T {
  return msg
  // 报错： 不能将类型 "string" 分配给类型 "T"
}
```

至于返回类型，如果设置的是 T，那么就必须 `return msg`，否则报错

而如果函数本身要返回其他类型，比字符串类型，那么修改为如下：

```ts
// 返回的是字符串类型
function info<T>(msg: T): string {
  return msg + 'Mr.Lee'
}
```

### 泛型数组

数组中怎么去使用泛型，先看下数组泛型的语法：

```ts
// 普通数组
let arr: number[] = [1, 2, 3, 4, 5, 6]

// 泛型数组
let arr: Array<number> = [1, 2, 3, 4, 5, 6]
```

首先看一下在函数中使用 any 数组的写法

```ts
// 返回类型是 any，没有类型检查
function setArr(value: any) : Array<any> {
  let result = [value, value, value]
  return result
}
console.log(setArr('a'))
```

多个泛型，可以自由灵活搭配，具体如下：

```ts
function more<T, P>(name: T, age: P): [T, P] {
  return [name, age]
}
console.log(more<string, number>('Mr.Lee', 100))
```

### 泛型类型

泛型也可以作为类型的方式存在，理解这一点，先了解下函数的声明方式：

```ts
// 普通函数
function info(name: string, age: number): string {
  return name + '的年龄：' + age
}
// 匿名函数1
let info = function(name: string, age: number): string {
  return name + '的年龄：' + age
}
// 匿名函数2
let info: (name: string, age: number) => string = function(name, age) {
  return name + '的年龄：' + age
}
```

匿名函数2 的声明方式中的 `=>` 箭头函数，表示函数的返回类型

那么用泛型来表示上面的匿名函数，我们可以对照样式来写：

```ts
function info<T>(msg: T): T {
  return msg
}
// 匿名函数形式的泛型类型
let myInfo: <T>(msg: T) => T = function(msg) {
  return msg
}
```

变形到这里，你会发现 `: <T>(msg: T)` 这个就是类型了，即 `: 泛型类型`

### 接口

泛型不但可以作为类型来使用，还可以继续变形，使用兑现字面量的形式存在

```ts
// 对象字面量形式的泛型类型
let info: {<T>(msg: T): T} = function(msg) {
  return msg
}
```

如果使用 `{}` 对象字面量方式，那么 `=>` 箭头函数就改成 `:` 冒号的对应语法即可

泛型类型支持 {}，那么就可以使用接口方式进行分离，有助于复用

```ts
// 将字面量形式用接口形式代替
interface info {
  <T>(msg: T): T
}
let myInfo: info = function (msg) {
  return msg
}
console.log(myInfo('Mr.Lee'))
```

当然，还可以将 `<string>` 的类型参数通过 `info<string>` 的形式传递

```ts
interface info<T> {
  (msg: T): T
}
let myInfo = function(msg) {
  return msg
}
console.log(myInfo('Mr.Lee'))
```

### 泛型约束

泛型也会有无法检查通过所有类型都具有同一个属性的问题，比如 `.length`

```ts
function info<T>(msg: T) : T {
  // 类型检查无法通过所有类型的 `.length` 属性
  conssole.log(msg.length)
  return msg
} 
```

> 这里一样会报错，我们可以使用之前的断言，但如果不需要其他类型时也可以使用约束。

使用泛型约束，就是限制任何传入的类型具有的属性，比如 `.length`

```ts
interface Ilength {
  length: number
}
function info<T extends Ilength>(msg: T): T {
  // 继承了约束接口后，具有 length 属性的检查通过
  console.log(msg.length)
  return msg
}
console.log(info<string>('Mr.Lee'))
```

### 泛型类

先看示例：

```ts
class Person<T, P> {
  _name: T
  _age: P
  constructor(name: T, age: P) {
    this._name = name
    this._age = age
  }
  get name(): T {
    return this._name
  }
  get age(): P {
    return this._age
  }
}
let p = new Person<string, number>('Mr.Lee', 100)
console.log(p.name)
console.log(p.age)
```

泛型只支持实例部分，不支持静态部分，也就是 static 关键字无法支持