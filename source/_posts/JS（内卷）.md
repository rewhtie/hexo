---
title: js内卷大法
data: 2021-11-18 12:00
tags: JavaScript
---

你写的代码这么简单？升级一下你的逼格吧，小伙伴

<!-- more -->

## Number 类型转换成 String 类型

```js
// 普通
10.toString()		// '10'

// 简写
10 + ''				// '10'
```

## String 类型转换成 Number 类型

```js
// 普通
parseInt('10') // 10
parseFloat('10.5') + // 10.5
  // 简写
  '10' + // 10
  '10.5' // 10.5
```

## 求 n 次方

```js
// 普通
2 * 2 * 2 * 2 * 2 // 32

// 简写
2 ** 5 // 32
```

## 浮点数转换成整数

```js
// 普通
Math.floor(5.4321) // 5

// 简写
~~5.4321 // 5

// 注意只适用于32位整数
// 即大于2147483647的数字会给出错误的结果，这种情况使用 Math.floor()
```

## 求某个字符串第 n 个字符

```js
const string = '我爱你'

// 普通
const str1 = string.charAt(1) // 爱

// 简写
const str2 = string[1] // 爱
```

## 求数组中最大和最小的数字

```js
const arr = [5, 4, 3, 2, 1, 0]

const max = Math.max(...arr) // 5
const min = Math.min(...arr) // 0
```

## 合并数组

```js
// 普通
let arr1 = [2, 1, 0]
arr1 = arr1.concat([1, 2]) // [2, 1, 0, 1, 2]

// 简写
let arr2 = [2, 1, 0]
arr2 = [...arr2, 1, 2] // [2, 1, 0, 1, 2]
```

## 判断是否等于某个值

```js
const val = 2

// 普通
if (val === 1 || val === 2 || val === 3) {
  // ……
}

// 简写
if ([1, 2, 3].includes(val)) {
  // ……
}
```

## 循环数组

```js
const arr = [5, 4, 3, 2, 1, 0]
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i])
}
// 简写方式一，只有一句代码时配合箭头函数最简短
arr.forEach(item => console.log(item))
// 简写方式二，通常用于内部有相对于外部的异步操作
for (const item of arr) {
  console.log(item)
}
// 简写方式三，通常用于对象
for (const prop in arr) {
  console.log(arr[prop])
}
```

<!-- more -->
