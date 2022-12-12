---
title: vue3 + TypeScript 警告找不到相应的模块
data: 2022-2-9 10:42
tags: TypeScript
---

项目里tsconfig.json文件配置属性

<!-- more -->

### 在根目录（也就是 tsconfig.json这一级）下新建名为 ”vue.d.ts“ 的文件。文件名中的 ”vue“ 也可以改为任一名称。

### 在 ”vue.d.ts“ 文件中写入以下声明：

```js
// 以下两种方案二选一

// 方案一
declare module "*.vue" {
  import Vue from "vue";
  export default Vue;
}

// 方案二
declare module '*.vue' {
  import { defineComponent } from 'vue'
  const Component: ReturnType<typeof defineComponent>
  export default Component
}

```
### 在 ”tsconfig.json“ 中，将第二步中创建的文件 ”vue.d.ts“（或者你自己新建的其他名称的 .d.ts 文件）添加到 include 中：
```json
"include": [
   "vue.d.ts"
],

```
<!-- more -->