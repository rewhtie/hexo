---
title: vue3.0（keep-live缓存）
data: 2022-12-19 16:00
tags:
  - vue3.0
---

keep-live 如何实现一些缓存组件、一些不需要缓存组件

<!-- more -->

设置meta属性的keepAlive属性，表示需要缓存，不需要不加
```js
const routes = {
  path: "/settlementManage",
  title: "结算管理",
  meta: {
    keepAlive: "settlementInfo",
  },
  component: () => import("@/views/settlementManage/settlementInfo/index.vue"),
};
```
keep-live组件
```html
<router-view v-slot="{ Component }">
  <keep-alive :include="includeList">
    <component :is="Component" />
  </keep-alive>
</router-view>
```
获取路由路径名
```js
import { useRoute, useRouter } from "vue-router";
const router = useRouter();
const includeList = ref<string[]>([]);
includeList.value = router.getRoutes().map((item) => item.meta.keepAlive ).filter((item) => (typeof item === "string" ? item : ""));
// 获取到所有的路由path路径名
```

<font color="red">以下几点需要注意：</font>

- 组件必须写name值并且不能重复，要不然缓存不会生效

- vue3中使用了keep-alive缓存的页面，不要把注释写在和根节点同级的位置，不然很可能很有几率的报一些莫名其妙的错误

- 用keep-alive尽量使用include和exclude来动态缓存组件，这样更好控制也更明确。不要一股脑全塞进去，不然有的页面要缓存有点页面不需要缓存，在我们跳转到一个不需要缓存的页面，肯定会把我们之前缓存的组件全部干掉，导致所有页面都不缓存

<!-- more -->
