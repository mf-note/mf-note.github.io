---
layout: post
title: TypeScript—类的使用
categories: [TypeScript]
description: TypeScript—类的使用
keywords: TypeScript
---

## 类的使用

### 1.类的使用

首先需要理解面向对象的基础和概念

主要概念为：面向对象的思维、类、三大特性（封装、继承、多态）等知识点

在 JavaScript 中，我们要创建一个对象，具体如下：

```js
let Person = {
  name: 'Hello World',
  age: 100,
  run() {
    console.log(`${this.name}的年龄为：${this.age}`)
  }
}

Person.run() // Hello World的年龄为：100
```

在 TypeScript 中支持类的创建，下面是包含成员的属性、构造方法以及普通方法（对于ES6的 class）

```ts
class Person {
  name : string
  age : number
  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
  run(): string {
    return `${this.name}的年龄为：${this.age}`
  }
}

let P = new Person('Hello World', 100)
console.log(P.run()) // Hello World的年龄为：100
```

### 2.类的继承

```ts
// 子类， Man 类
class Man extends Person {}

// 子类， Woman 类
class Woman extends Person {}

// 此时 Man 类将拥有父类的所有内容
let m = new Man('男人', 30)

// Woman 类也拥有父类的所有内容
let w = new Woman('女人', 20)
```

子类继承父类，拥有父类的属性和方法，也可以再创建自己独有的特性

```js
// 子类
class Woman extends Person {
  // 子类成员
  food: string = '早餐'
  // 子类方法
  eat(): string {
    return `${this.name}吃${this.food}`
  }
}
let w = new Woman('女人', 20)
console.log(w.eat())
```

TypeScript 支持多重继承，不支持多继承（一个子类继承多个父类）

```ts
// 不支持继承多个类
class Woman extends Person1 extends Person2 {}

// 第一重继承
class Woman extends Person {}

// 第二重继承
class OldWoman extends Woman {}
```

### 3.类方法重写

类方法分为构造方法和普通方法，这里我们都要实现一下重写机制

为什么要进行重写？因为父类只提供一个架构，更多子类细节需要自己实现

```ts
class Man extends Person {
  height: number
  // 重写构造方法
  constructor(name: string, age: number, height: number) {
    super(name, age)
    this.height = height
  }
  // 重写 run 方法
  run(): string {
    // 普通方法采用 super. 方法进行调用
    return `${super.run()}，身高为：${this.height}`
  }
}

let m = new Man('男人', 30, 1.80)
console.log(n.run())
```

## 类方法的重载

类方法的重载，即：方法相同但传递参数不同从而执行不同的一种操作

比如构造方法里，可能只传一个参数，也可能只传递两个参数来实现不同效果

```ts
class Person {
  name: string,
  age: number | undefined
  // 构造方法    ?:语法为可选参数
  constructor(name: string, age?: number) {
    this.name = name
    this.age = age
  }
  run(): string {
    if (this.age === undefined) {
      return this.name + '的年龄为：保密'
    }
    return `${this.name}的年龄为：${this.age}`
  }
}

let M = new Person('Mr', 100)

let W = new Person('Miss')
```

普通方法的重载和构造方法重载一个意思

## 类成员的修饰符

在不加修饰符的情况下，成员字段和方法默认是公共 public 完全可见的状态

如果要在其设置一下不同的可见性，一共有三种方案：

* public：默认设置，公有可见性
* protected：受保护的，自身和子类可访问
* private：私有的，只能自身访问

为什么要设置可访问性，主要是为了保护类中属性和方法避免被污染

在默认的情况下，成员字段是 public 修饰符，我们在类外可以直接赋值：

```ts
const lee = new Person('Mr.Lee', 50)
// 修改
lee.name = 'Mr.Zhang'
// 也可以直接取值
console.log(lee.name)
console.log(lee.run())
```

如果设置了私有成员，那么就无法在类外进行取值赋值，进行有效的保护：

```ts
class Person {
  private name: string
  private age: number
}
```

在类外对 `p.name` 赋值取值会直接报错，但这种私有化，子类就无法直接访问了：

```ts
// 成员字段私有化，子类如果要重写，就无法访问 this.name
class Man extends Person {
  run(): string {
    // 报错 属性 "name" 为私有属性，只能在 "Person" 中访问
    return `${this.name}的年龄为：${this.age}`
  }
}
```

如果有子类，并且需要对子类开放权限，那么就使用 protected 受保护的：

```ts
class Person {
  protected name: string
  protected age: number
}
```

对于方法或构造方法，也可以使用这些修饰符，比如受保护的构造，就不能在外部调用了：

```ts
class Person {
  protected name: string
  protected age: number
  // 构造方法
  protected constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
}

class Man extends Person {
  constructor(name: string, age: number) {
    super(name, age)
  }
  run(): string {
    return `${this.name}的年龄为：${this.age}`
  }
}
new Person('Mr.Lee', 100) // 类 "Person" 的构造函数是受保护的，仅可在类声明中访问
```

## 类成员的读取器

### 1.成员字段访问

成员字段我们进行了封装保护，那么除了实例化的方案外，还怎么对外暴露接口呢？

可以通过 `setXXX()` 和 `getXXX()` 约定俗成的方法来实现对成员字段的取值赋值：

```ts
class Person {
  private name: string
  // 成员 name 赋值
  setName(name: string) {
    this.name = name
  }
  // 成员 name 取值
  getName() {
    return this.name
  }
}

let p = new Person('Mr.Lee', 100)

p.setName('Mr.Zhang')

console.log(p.getName())
```

这样就解决了私有成员的类外赋值取值的问题

但是我们一般调用很多框架或库的属性是不带花括号的，方法彩带。所以，TypeScript 提供了成员的存储器来实现对成员的取值赋值

### 2.成员字段存储器

成员字段有专门的 `setter` 和 `getter` 方法来处理私有成员字段的取值赋值；只不过原有的成员字段最好相对应的改成固有名称让其一看就知道是私有成员

这样，类的内部都用 `this.私有成员`，外部用 `setter` 和 `getter` 的仿成员：

```ts
class Person {
  private _name: string
  private _age: number
  constructor(name: string, age: number) {
    this._name = name
    this._age = age
  }
  // 赋值
  set name(name: string) {
    this._name = name
  }
  // 取值
  get name() {
    return this._name
  }
  // 赋值
  set age(age: number) {
    this._age = age
  }
  // 取值
  get age() {
    return this._age
  }
  // 普通方法
  run(): string {
    return `${this.name}的年龄为：${this.age}`
  }
}

let p = new Person('Mr.Lee', 100)
p.name = 'Mr.Zhang'
p.age = 50
console.log(p.run())
```

## 静态成员和方法

`static` 关键字用于声明静态成员和方法，这是一种不需要实例化的调用方式。一般用于各种工具类，方便直接使用

```ts
class Person {
  static pi = 3.14
  private name: string
  private age: number
  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
  run(): string {
    return `${this.name}的年龄为：${this.age}`
  }
  static getPI() {
    return '圆周率为：' + Person.pi
  }
}
console.log(Person.pi)
console.lgo(Person.getPI())
```

如果想让静态成员设置为只读，可以加上 `readonly` 关键字防止被改写

```ts
class Person {
  static readonly pi = 3.14
}
```

static 结合 readonly 不可改变，类似常量

## 抽象类的使用

抽象类可以用于顶层指定和设计标准，让子类继承时去实现具体细节

抽象类设计相关成员和方法，不用于具体使用，所以无法实例化

```ts
abstract class Person {
  constructor(protected name: string, protected age: number) {}
  // 需要完成细节的方法
  abstract run(): string
}
// 无法实例化
new Person('Mr.Lee', 100)
```

抽象类是不允许被实例化的，抽象类中的抽象方法必须被子类实现

```ts
class Man extends Person {
  constructor(name: string, age: number) {
    super(name, age)
  }
  run(): string {
    return `${this.name}的年龄为：${this.age}`
  }
}
```

所以，子类必须实现抽象类的方法，严格按照标准实现，否则会报错

抽象类虽然可以指定标准给子类实现，但自己也可以实现一些通用的公共行为

需要注意的是，即使是抽象方法，TypeScript 的编译结果中，仍然会存在这个类，知识这个类不会保留抽象方法

## 接口的使用

### 1.接口

接口类似于抽象类，只不过它更加彻底，只提供标准却完全不实现细节

具体表现在，成员字段不可以赋值初始化，方法不可以实现

```ts
// 不需要修饰符，都是 public
interface Person  {
  name: string
  age: number
  run(): string
}

// 类实现接口
class Man implements Person {
  name: string = 'Mr.Lee'
  age: number = 100
  run(): string {
    return `${this.name}的年龄为：${this.age}`
  }
}
```

接口的成员和方法必须由子类实现，也可以设置为可选方案让子类自由实现

```ts
// 接口，实现可选
interface Person {
  name?: string
  age?: number
  run?(): string
}
```

这样实现的子类就不必强制去初始化或实现接口中的成员和方法了

继承只能继承一个父类，是接口实现支持实现多个接口。并且，接口还可以继承另一个接口，从而进行合并

```ts
interface Woman extends Person
```

### 2.接口类型

接口还有一个比较常用的使用场景，就是规范字面量对象的类型

比如一个字面量对象，规范它的属性和方法，就可以使用接口类型

```ts
interface Person {
  name: string
  age: number
  run(): string
}

// 字面量对象
let man: Person = {
  name: 'Mr.Lee',
  age: 100,
  run() {
    return `${this.name}的年龄为：${this.age}`
  }
}

console.log(man.name)
console.log(man.run())
```

当然，也是支持 `name ?: string` 这种可选字段属性

接口提供了规范后，字面量对象如果自行增加接口不存在的属性将报错

```ts
let man: Person = {
  name: 'Mr.Lee',
  age: 100,
  // 接口中没有，这里将报错
  gender: '男'
}
```

接口的字段也可以设置任意属性，具体如下：

```ts
interface Person {
  [attr: string]: any
}
```

此时，对应的对象可以自由添加各种属性和类型。左边的 `string` 表示属性(key)的类型，只支持 string 和 number。右边的 `any` 表示值(value)的类型，和普通类型限制一样