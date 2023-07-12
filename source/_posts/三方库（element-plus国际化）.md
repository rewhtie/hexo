---
title: element-plus国际化
data: 2021-11-26 14：01
categories: 插件、依赖
tags:  
    - 三方库
---

element-puls解决日历组件里面的年月份周期没有汉化

<!-- more -->
#### 非常简单粗暴，直接引入以下模板直接使用即可
```js
<template>
  <el-config-provider :locale="locale">
    <router-view />
  </el-config-provider>
</template>
<script>
import { ElConfigProvider } from 'element-plus'
import zhCn from 'element-plus/lib/locale/lang/zh-cn'
import 'dayjs/locale/zh-cn'
export default {
  components: {
    [ElConfigProvider.name]: ElConfigProvider,
  },
  setup() {
    const locale = zhCn
    return {
      locale,
    }
  },
}
</script>
```

```js
npm
import { ArrowLeftBold, MoreFilled } from '@element-plus/icons';
```

<!-- more -->