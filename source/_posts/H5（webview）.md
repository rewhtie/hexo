---
title: H5 webview
data: 2021-11-27 16:39:45
tags: 
	- H5
---

场景：当 app 或者小程序需要嵌入我们开发的 h5 页面的时候，需要处理的一些问题，完成不同环境下实现不同的交互

<!-- more -->

## 封装的一套环境识别方法

  <font color="#08c">window.webkit.messageHandlers.jsCallToNativePages.postMessage("{}"); 调用 ios 的 app 方法</font>

  <font color="#08c">window.Android.jsCallToNativePages("{}"); 调用 android 的 app 方法</font>

  <font color="#08c">jsCallToNativePages 是 app 方法名，这个需要 app 开发者提供</font>

```js
function getClient() {
  var u = navigator.userAgent.toLowerCase()
  var isWeiXin = u.match(/MicroMessenger/i) == 'micromessenger' ? 1 : 0
  var isiOS = /iphone|ipad|ipod/.test(u)
  if (isWeiXin) {
    return 3
  } else if (isiOS) {
    //ios
    try {
      //我们无法识别app的环境，只能通过调用app软件暴露出来的方法，执行一次，没有这个方法就会报错，我们可以捕获异常从而达到环境的判断
      window.webkit.messageHandlers.jsCallToNativePages.postMessage('{}')
      return 1
    } catch (e) {
      console.log('isios')
      return 5
    }
  } else {
    //android
    try {
      window.Android.jsCallToNativePages('{}')
      return 2
    } catch (e) {
      return 5
    }
  }
}
const client = getClient()
// 1： ios环境的app
// 2:  android环境的app
// 3:  微信环境下的浏览器
// 5:  浏览器环境
```


<!-- more -->
