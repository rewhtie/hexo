---
title: TypeScript基本类型
tags: TypeScript
---

TS定义变量的类型,在TS中是最常用的最基本的

<!-- more -->

element-plus多选框

```ts
import { CheckboxValueType } from 'element-plus'
const onCheckAllChange = (e: CheckboxValueType) => {
  checkedList.value = e ? map(plainOptions.value, 'value') : []
  indeterminate.value = false
}
const onCheckChange = (e: CheckboxValueType[]) => {
  // checkedList.value = [e[e.length - 1]] 单选
  checkAll.value = e.length === plainOptions.value.length
  indeterminate.value = e.length > 0 && e.length < plainOptions.value.length
}
```