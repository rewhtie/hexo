
---
title: Vue2、vue3
data: 2022-03-11 17:00
tags: 
- 高薪之路

---

Vue2的知识点，Vue3的特性，两者之间的差别

<!-- more -->

<font size=6 color='black'> Vue面试题</font>

<font size=5 color='#4DD7EB'> ref与reactive的区别
- ref用来定义: 基本类型数据。
- ref通过 Object.defineProperty() 的 get 与 set 来实现响应式（数据劫持）。
- ref定义的数据：操作数据需要.value，读取数据时模板中直接读取不需要.value。
- reactive用来定义: 对象或数组类型数据。
- reactive通过使用: Proxy 来实现响应式（数据劫持）, 并通过 Reflect 操作源代码内部的数据。
- reactive定义的数据：操作数据与读取数据：均不需要 .value。

ref可以定义对象或数组的，它只是内部自动调用了 reactive 来转换。

proxy只能监听引用对象，比如数组，对象。基本数据类型无法监听 ; 所以要监听基本数据类型，就得使用ref，ref实际就是把你
的基本数据类型放在一个对象里，然后再.value

<!-- more -->