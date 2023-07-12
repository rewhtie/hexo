---
title: 三方库（ant-vue）
data: 2023-06-25 13:00
categories: 插件、依赖
tags: 
- 三方库

---

使用ant-vue组件一些小细节、坑点、这里列举常见的错误

<!-- more -->

输入框展示输入字符长度和限制长度

```js
<a-input
    :suffix="`${formState.signNo.length}/30`"
    :maxlength="30"
    placeholder="如：F2200070"
    v-model:value="formState.signNo"
/>
```

![](http://linmingqi.top/img/%E5%AD%97%E7%AC%A6%E9%95%BF%E5%BA%A6%E5%B1%95%E7%A4%BA.png)

按需导入使用时：引入CheckboxGroup找不到多选框报错

![](http://linmingqi.top/img/%E5%BC%95%E5%85%A5%E5%A4%9A%E9%80%89%E6%A1%86%E6%8A%A5%E9%94%99.png)

```js
// 报错信息: 说找不到模块，到node_modules确实找不到es下有checkboxgroup
Module not found: Error: Can't resolve 'ant-design-vue/es/checkbox-group/style'

```

正确使用
```html
<Checkbox.Group></Checkbox.Group>
```

<!-- more -->


 
