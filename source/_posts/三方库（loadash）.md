---
title: loadash方法使用场景快速查找
data: 2021-11-18 12:00
tags: 三方库
---

loadash方法使用场景快速查找

<!-- more -->

## map 数组对象找出对应key值

```js
import { map } from 'lodash'
type plainOptionsType = { label: string, value: number }
const plainOptions = ref([] as Array<plainOptionsType>)
const data = map(plainOptions.value, 'value') as Array<number>
```

## 对象

```js


```

## 判断是否等于某个值

<!-- more -->
