---
layout: post
title: TypeScript—数据类型
categories: [TypeScript]
description: TypeScript—数据类型
keywords: TypeScript
---

## TypeScript 基础类型
### 1. Boolean 类型
```ts
let bool: boolean = false
// ES5: var bool = false
```

### 2. Number 类型
```ts
let num: number = 9
// ES5: var num = 9
```

### 3. String 类型
```ts
let str: string = 'this is a string'
// ES5: var str = 'this is a string'
```

### 4. Array 类型
```ts
let arr: number[] = [1, 2]
// or
let arr: Array<number> = [1, 2]

// ES5: var arr = [1, 2]
```

### 5. Enum 类型 (枚举)

> 数字枚举
```ts
enum TestEnum {
  FIRST,
  SECOND,
  THIRD,
}

let dir: Direction = Diretion.FIRST
```
默认情况下，FIRST 的初始值为 0，其余成员会从 1 开始自动增长。换句话说，`TestEnum.SECOND` 的值为 1，`TestEnum.THIRD` 的值为 2。  
我们也可以手动设置 FIRST 的初始值，如：
```ts
enum TestEnum {
  FIRST = 3,
  SECOND,
  THIRD,
}
```

> 字符串枚举

在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化
```ts
enum TestEnum {
  FIRST = "FIRST",
  SECOND = "SECOND",
  THIRD = "THIRD",
}
```

> 异构枚举

异构枚举的成员值是数字和字符串的混合：
```ts
enum TestEnum {
  A,
  B,
  C = "C",
  D = 5,
  E,
  ...
}
```

### 6. Any 类型
在 TypeScript 中，任何类型都可以被归为 any 类型。这让 any 类型成为了类型系统的顶级类型（全局超级类型）
```ts
let anyType: any = 123
anyType = 'hello world'
anyType = false
```

any 类型本质上是类型系统的一个逃逸仓。TypeScript 允许我们对 any 类型的值执行任何操作，无需事先执行任何形式的检查。比如：
```ts
let value: any

value.foo.bar // OK
value.trim() // OK
value() // OK
new value() // OK
value[0][1] // OK
```

使用 any 类型，可以很容易地编写类型正确但在运行时有问题的代码。如果使用 any 类型，就无法使用 TypeScript 提供的大量保护机制。为了解决 any 带来的问题， TypeScript3.0 引入了 unknown 类型。

### 7. Unknown 类型
就像所有类型可以赋值给 any 类型，所有类型也都可以赋值给 unknown。这使得 unknown 成为 TypeScript 类型系统中的另一种顶级类型。
```ts
let value: unknown

value = true // OK
value = 42 // OK
value = 'hello world' // OK
value = [] // OK
value = {} // OK
value = null // OK
value = undefined // OK
value = new TypeError() // OK
```

对 value 变量的所有赋值都被认为时类型正确的。但是，当我们尝试将类型为 unknown 的值赋值给其他类型的变量时会有不同的结果
```ts
let value: unknown

let a: unknown = value // OK
let b: any = value // OK
let c: boolean = value // Error
let d: number = value // Error
let e: string = value // Error
let f: object = value // Error
let g: any[] = value // Error
let h: Function = value // Error
```
unknown 类型只能被赋值给 any 类型和 unknown 类型本身。现在我们看看当我们尝试对类型为 unknown 的值执行操作时会发生什么
```ts
let value: unknown

value.foo.bar // Error
value.trim() // Error
value() // Error
new value() // Error
value[0][1] // Error
```
将 value 变量类型设置为 unknown 后，这些操作都不再被认为是类型正确的。通过 any 类型改变为 unknown 类型，我们已将允许所有更改的默认设置，变为禁止任何更改

### 8. Tuple 类型（元组）
元组可用于定义具有有限数量的未命名属性的类型。每个数据都有一个关联的类型。使用元组时，必须提供每个属性的值。
```ts
let tupleType: [string, boolean]
tupleType = ['hello world', true]

console.log(tupleType[0]) // hello world
console.log(tupleType[1]) // true
```
上面的代码中，我们定义了一个名为 `tupleType` 的变量，它的类型是一个类型数组 `[string, boolean]`，然后按照正确的类型依次初始化 `tupleType`。与数组一样，我们可以通过下标来访问元组中的元素。

在元组初始化的时候，如果出现类型不匹配，TypeScript 编译器会提示错误信息：
```ts
tupleType = [true, 'hello world']

// [0]: Type 'true' is not assignable to type 'string'.
// [1]: Type 'string' is not assignable to type 'boolean'.
```
很明显是因为类型不匹配导致的。在元组初始化时，我们还必须提供每个属性的值，不然也会报错：
```ts
tupleType = ['hello world']

// Property '1' is missing in type '[string]' but required in type '[string, boolean]'.
```

### 9. Void 类型
某种程度上来说，viod 类型像是与 any 类型相反，它表示没有任何类型。当一个函数没有返回值时，其返回类型是 void
```ts
// 返回值为 void 类型
function voidBack(): void {
  ...
}
```
需要注意的是，声明一个 void 类型的变量没有什么作用，因为它的值只能为 undefined 或 null

### 10. Never 类型
never 类型表示用不存在的值的类型。值永不存在的两种情况：
* 如果一个函数执行时抛出异常，那么这个函数永远不存在返回值（抛出异常会直接中断程序运行，使得程序运行不到返回值那一步）
* 函数中执行无限循环的代码（死循环）

```ts
// 抛出异常
function err(msg: string): never {
  throw new Error(msg)
}

// 死循环
function loop(): never {
  while(true) {
    ...
  }
}
```
never 类型 与 null、undefined 一样，也是任何类型的子类型，也可以赋值给任何类型。但是没有类型是 never 的子类型或可以赋值给 never 类型（除never 本身以外，any）

### 11. Null 和 Undefined
默认情况下，null 和 undefined 是所有类型的子类型。可以把 null 和 undefined 赋值给其他类型
```ts
// null和undefined赋值给string
let str: string = 'hello world'
str = null // OK
str = undefined // OK

// null和undefined赋值给number
let num: number = 123
num = null
num = undefined

// null和undefined赋值给object
let obj: object ={}
obj = null
obj = undefined

// null和undefined赋值给Symbol
let sym: symbol = Symbol("me")
sym = null
sym = undefined

// null和undefined赋值给boolean
let bool: boolean = false
bool = null
bool = undefined

// null和undefined赋值给bigint
let big: bigint = 100n
big = null
big = undefined

```
