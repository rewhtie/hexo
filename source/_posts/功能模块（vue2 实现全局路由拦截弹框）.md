---
title: 功能模块（vue2 实现全局路由拦截弹框）
date: 2022-12-20 11:28:54
tags: 功能模块
---

一般实现登录token过期就是重定向到登录页面，让用户重新进行操作，但是也可以实现以弹框的形式进行登录，从而达到保存用户行为的效果

<!-- more -->

- 登录组件
  - 函数式调用"extend" login.install({})
  - 标签式调用 "< login / >"
- 全局路由守卫
  - 白名单,绕过登录
  - 名单,一定需要登录
  - 不在任何名单,默认重定向到初始路由页面
- 接口返回code对应码 ~ 清除本地登录信息
  - 411 用户冻结
  - 9111 用户异常
  - 9137 token过期
- 自定义指令 ~ 校验登录



<font color='red'>login登录函数组件（extend）</font>

```js
import Vue from "vue";
import login from "./login.vue";

const LoginDialog = Vue.extend(login);

let instance = null;

login.install = function (next) {
  if (instance) {
    document.body.removeChild(instance.$el);
  }

  instance = new LoginDialog({}).$mount();

  document.body.appendChild(instance.$el);

  Vue.nextTick(() => {
    instance.open(next);
  });
};

export default login;
```

<font color='red'>路由封装 ~ 路由跳转校验登录</font>

```js
import Vue from "vue";
import VueRouter from "vue-router";
import routes from "./routes";
import login from "@/components/login";
import { Dialog } from "vant";

Vue.use(VueRouter);

const router = new VueRouter({
  mode: "history",
  base: window.loaction.href,
  routes
});

// 白名单、有些路由页面不需要登录也能访问
const whiteList = ["/", "/home", "/detail", "/customer", "/about"];

// 需要登录的路由页面
const loginList = [
  "/search",
  "/order",
  "/person",
  "/collectionList",
  "/couponStationList"
];

// 其他的就是重定向的路由

router.beforeEach(async (to, from, next) => {
  document.title = to.meta.title || from.meta.title;
  const isLogin = localStorage.get("userInfo")
    ? !!localStorage.get("userInfo").token
    : false;
  if (isLogin) next();
  else if (whiteList.indexOf(to.path) > -1) next();
  else if (loginList.indexOf(to.path) > -1) {
    await login.install(next);
  } else {
    Dialog.alert({
      title: "提醒",
      message: "链接走丢了",
      confirmButtonColor: "#000"
    }).then(() => {
      router.push("/");
    });
  }
});
export default router;
```

<font color='red'>axios封装 ~ 响应拦截处理</font>

```js
import axios from "axios";
import login from "@/components/login";

const http = axios.create({
  baseURL: process.env.VUE_APP_BASE_URL,
  timeout: 10000
});

// 请求响应拦截处理
http.interceptors.response.use(
  ({ data, config }) => {
    if (data.code === 200) {
      return data;
    } else {
       // 定时器是因为全局的toast.clear导致污染
       setTimeout(() => {
        Toast({
          message: data.message,
          duration: 1500
        });
       }, 0);
        // 401、9111、9137是token过期或者用户冻结，弹出登录框 ~ 登录之后刷新当前页面
       if (data.code == 401 || data.code == 9111 || data.code == 9137) {
         login.install(() => {
           window.location.reload();
         });
       }
    }
    return Promise.reject(data);
  },
  (error) => {
    if (error.response.data?.message) {
      Toast(error.response.data.message);
    }
    return Promise.reject(error.response || error);
  }
);

export default http;
```

<font color='red'>自定义指令 ~ 标签 "< div v-login / >" 校验登录提前，未登录弹登录框阻止冒泡，已登录无事发生 </font>

```js
import Vue from "vue";

Vue.directive("login", {
  bind: function (el) {
    el.addEventListener("click", login, true);
  }
});

import login from "@/components/login";
function login(e) {
  const isLogin = localStorage.get("userInfo")
    ? !!localStorage.get("userInfo").token
    : false;
  if (!isLogin) {
    e.stopImmediatePropagation();
    login.install();
  }
}
```

[基础的登录组件代码](https://rewhtie.github.io/hexo/2022/12/08/%E5%8A%9F%E8%83%BD%E6%A8%A1%E5%9D%97%EF%BC%88vue2%20+%20vant2.0%20%E7%99%BB%E5%BD%95%E5%BC%B9%E6%A1%86%EF%BC%89/)


<!-- more -->