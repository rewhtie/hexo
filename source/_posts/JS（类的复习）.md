---
title: js类的继承
data: 2022-6-11 10:42
tags: JavaScript
categories: 开发语言
---

复习类的知识点

<!-- more -->

```js
/*
1.类的构造器不是必须写的，对实例进行初始化操作，如添加指定属性才写
2.A类继承了B类，A类写了构造器，A类的super必须调用
*/
class Person{
    // 构造器的 this是类的实例对象
    constructor(name,age){
        this.name = name,
        this.age = age
    }
    // 并没有找到speak函数，放到了类的原型对象上，供实例使用
    speak(){
        console.log(this.name,this.age)
    }
    // 类可以直接写复制语句
    a = 1
}
// 定义了类，继承了类，里面写了构造器，满足三点super必须调用
const p1 = new Person()
// 创建一个类，继承于person类
class aaa extends Person {
    constructor(name,age,grade){

        // this.name = name,
        // this.age = age,
        // 必须在最开始前调用super
        super(name, age)

        this.grade = grade
    }
    // 重写从父类继承过来的方法,这里的speak和Person类里面的speak不一样
    speak(){
        console.log(this.name,this.age,this.grade)
    }
}
const s1 = new aaa()
// s1能调用speak方法，原型链查找
```

<!-- more -->


