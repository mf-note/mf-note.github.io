---
layout: post
title: JavaScript—基础知识
categories: [JavaScript]
description: JavaScript—基础知识
keywords: JavaScript
---


### 一.变量数据类型和检测方式
js数据类型  
```js
基本类型：空值（null）、未定义（undefined）、布尔值（Boolean）、数值（Number）、字符串（String）、符号（Symbol，ES6新增）
引用类型：对象（Object）（还有Function、Array、RegExp、Date等）
```

### **1.typeof**
```js
typeof '0' // string
typeof 0 // number
typeof true // boolean
typeof [] // object
typeof null // object, js的bug
typeof undefined // undefined
typeof Function // function
typeof Symbol() // symbol
```
* 对于基本数据类型除null外，typeof能够返回正确的检测值
* 对于引用类型除Function外，其他都返回object
* 对于null，返回object类型
* 对于Function，返回function类型  

typeof检测数据类型的原理：
在JavaScript最初版本中，使用的32位系统，为了性能考虑使用地位存储变量类型信息
* 000：对象
* 1：整数
* 010：浮点数
* 100：字符串
* 110：布尔值

有两个值比较特殊：
* undefined：用 - （−2^30）表示
* null：

在<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof#null" target="_blank">MDN</a>中对于typeof null === 'object'也有介绍：
>在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是 0。由于 null 代表的是空指针（大多数平台下值为 0x00），因此，null 的类型标签是 0，typeof null 也因此返回 "object"

补充：function其实是object的一个子类型，具体来说，函数是“可调用对象”，它有一个内部属性[[call]]，该属性可以使其被调用。  

### **2.instanceof**
<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof" target="_blank">MDN</a>中解释为：
>instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上

通俗来讲就是instanceof用来比较某个对象是否为构造函数的实例（不适用与原始类型的值）

要理解instanceof的原理，可以从两个方面入手：
* JavaScript原型继承机制
* 语言规范中如何定义这个运算符

1.是否在原型链上
```js
function Person(){}
const person = new Person()
console.log(person instanceof Person) // true
```

2.是否属于该实例的父类或祖先类
```js
function Father(){}
function Son(){}
Son.prototype = new Father()
let f = new Son()
console.log(f instanceof Son) // true
console.log(f instanceof Father) // true
```

注意：instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型

### **3.constructor**
