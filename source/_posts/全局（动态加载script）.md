---
title: 动态加载script执行回调
data: 2022-08-13 13:00
tags:
  - 全局函数
---

加载脚本的常用代码封装成函数，并执行完成的回调，成功的回调，失败的回调

<!-- more -->

## **一.js 动态插入 srcipt 回调写法**

<font color="#08c">我们可以动态的创建 'script' 元素，然后通过更改它的 src 属性来加载脚本，但是怎么知道这个脚本文件加载完成了呢？因为有些函数需要在脚本加载完成才能调用。<font color="red">IE 浏览器中可以使用 'script' 元素的 onreadystatechange 来监控加载状态的改变</font>，并通过判断它的 <font color="red">readyState 是 loaded 或 complete 来判断脚本是否加载完成</font>。而<font color="red">非 IE 浏览器可以使用 onload </font>来直接判断脚本是否加载完成。</font>

封装的方法

```js
function JSscript(url, callback) {
  var hm = document.createElement('script')
  hm.src = url
  callback?.finally && callback.finally()
  hm.onload = hm.onreadystatechange = function () {
    if (!this.readyState || this.readyState == 'loaded' || this.readyState == 'complete') {
      // 这里需要执行函数，因为外层有匿名函数
      callback?.success && callback.success()
    } else {
      hm.onload = hm.onreadystatechange = null
    }
  }
  // 这里赋值函数
  hm.onerror = callback?.error && callback.error
  // 插入脚本
  var s = document.getElementsByTagName('script')[0]
  s.parentNode.insertBefore(hm, s)
}
```

调用示例1（百度地图js定位）（有回调）

```js
JSscript('https://api.map.baidu.com/getscript?v=2.0&ak=uUZas4GaaI6QalSTn8giR2rX&services=', {
  finally: function () {
    console.log('不管成功失败都会执行的回调')
  },
  success: function () {
    console.log('成功的回调')
    var geolocation = new BMap.Geolocation()
    geolocation.getCurrentPosition(
      function (result) {
        if (result.latitude && result.longitude) {
          console.log('定位成功', result.latitude, result.longitude)
        } else {
          console.log('定位失败')
        }
      },
      {
        enableHighAccuracy: true,
      }
    )
  },
  error: function () {
    console.log('失败的回调')
  },
})
```

调用示例2（md5加密）（无回调）

```js
JSscript('https://api.map.baidu.com/getscript?v=2.0&ak=uUZas4GaaI6QalSTn8giR2rX&services=')
let sign = window.hex_md5("" + 1111 + 1111);
// vue需要加window指向，或者.eslintrc.js设置
```

```js
module.exports = {
  root: true,
  env: {
    node: true,
  },
  extends: ["plugin:vue/essential", "eslint:recommended", "@vue/prettier"],
  parserOptions: {
    parser: "babel-eslint",
  },
  rules: {
    "no-console": process.env.NODE_ENV === "production" ? "warn" : "off",
    "no-debugger": process.env.NODE_ENV === "production" ? "warn" : "off",
  },
  "globals": {
    "error": true
  }
};
```

## **二.js 动态插入 srcipt promise写法**
```js
/**
 * 加载js文件
 * @param {文件链接} url
 * @returns
 */
export const loadScript = (url = "") => {
  return new Promise((resolve, reject) => {
    const script = document.createElement("script");
    script.src = url;
    document.body.appendChild(script);
    script.onload = function () {
      resolve(this);
    };
    script.onerror = function () {
      reject(this);
    };
  });
};

/**
 * 加载css文件
 * @param {文件链接} url
 * @returns
 */
export const loadCss = (url = "") => {
  return new Promise((resolve, reject) => {
    const link = document.createElement("link");
    link.href = url;
    link.type = "text/css";
    link.rel = "stylesheet";
    const head = document.head || document.getElementsByName("head")[0];
    head.appendChild(link);

    link.onload = function () {
      resolve(this);
    };
    link.onerror = function () {
      reject(this);
    };
  });
};

/**
 * 加载css文件
 * @param {文件链接} url
 * @returns
 */
export const loadImg = (url = "") => {
  return new Promise((resolve, reject) => {
    const image = new Image();
    image.src = url;
    image.onload = function () {
      resolve(this);
    };
    image.onerror = function () {
      reject(this);
    };
  });
};

export const load = (url = "", type = "script") => {
  switch (type) {
    case "script":
      return loadScript(url);
    case "stylesheet":
      return loadCss(url);
    case "image":
      return loadImg(url);
    default:
      return loadScript(url);
  }
};

export default load;
```

```js
// 示例
load('https//www.baidu.com')
  .then(()=>{

  })
  .catch(()=>{
    
  })
```

<!-- more -->
