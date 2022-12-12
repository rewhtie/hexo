---
title: H5（webview双通信）
data: 2022-12-08 16:39:45
tags: 
	- H5
---

APP 与 H5 直接 进行双通信交互

<!-- more -->

<font color="red">APP jsBridge 实现封装的一套实现方案</font>

```js
import { isApp } from "@/utils/index";
/**
 * 初始jsBridge
 */
export const setupWebViewJavascriptBridge = (callback = () => {}) => {
  // 非app环境不执行
  if (!isApp()) return;
  if (window.WebViewJavascriptBridge) {
    return callback(window.WebViewJavascriptBridge);
  }
  if (window.WVJBCallbacks) {
    return window.WVJBCallbacks.push(callback);
  }
  window.WVJBCallbacks = [callback];

  var WVJBIframe = document.createElement("iframe");
  WVJBIframe.style.display = "none";
  WVJBIframe.src = "https://__bridge_loaded__";
  document.documentElement.appendChild(WVJBIframe);
  setTimeout(function () {
    document.documentElement.removeChild(WVJBIframe);
  }, 0);
};

const success = {
  code: 1,
  data: null,
  message: "调用成功"
};

const fail = {
  code: 0,
  data: null,
  message: "调用失败"
};

/**
 * 用于JS调用APP的方法，一般直接使用即可
 * @param {*} name 
 * @param {*} handler 
 */
export const jsCallNative = (methood, params, callback) => {
  setupWebViewJavascriptBridge((bridge) => {
    bridge?.callHandler(methood, JSON.stringify(params), callback);
  });
};

/**
 * 用于注册APP调用JS的方法，一般直接使用即可
 * @param {*} name
 * @param {*} handler
 */
export const registerJavascriptHandler = (name, handler) => {
  setupWebViewJavascriptBridge((bridge) => {
    bridge?.registerHandler(name, async (resp, callback) => {
      let respJson;
      try {
        respJson = JSON.parse(resp);
        const res = (await handler(respJson)) || {};
        callback(
          JSON.stringify({
            ...success,
            res
          })
        );
      } catch (e) {
        callback(
          JSON.stringify({
            ...fail,
            message: `app调用js报错: ${e.message}`
          })
        );
      }
    });
  });
};

/**
 * 基础交互方法封装带防重功能
 * @param {String} methood Native功能类型编码
 * @param {Object} params 传递给Native的参数
 * @param {boolean} preventRepeat 防重
 */
export const baseAppCall = (methood, params, preventRepeat) => {
  // 500ms内防重限制
  if (preventRepeat) {
    window.flagMap = window.flagMap || {};
    if (!window.flagMap[methood]) {
      // 防止点击过快，多次唤起app
      window.flagMap[methood] = true;
      setTimeout(() => {
        // 500ms后解开限制
        window.flagMap[methood] = false;
      }, 500);
      return callApp(methood, params);
    } else {
      throw new Error("禁止重复调用");
    }
  } else {
    //否则直接和app交互
    return callApp(methood, params);
  }
};

/**
 * JS调用APP的基础方法，可以基于此方法再次封装
 * @param {*} methood
 * @param {*} params
 */
export const callApp = (methood, params = {}) => {
  return new Promise((resolve, reject) => {
    console.log("JS->APP: ", methood, params);

    // 非App环境
    if (!isApp()) {
      console.warn("非App环境，直接返回空对象");
      reject({
        code: -1,
        message: "非app环境"
      });
    }

    jsCallNative(methood, params, (resp) => {
      console.log("JS->APP结果: ", resp);
      let respJson;
      try {
        respJson = JSON.parse(resp);
        if (respJson.code == 1) {
          resolve(respJson.data);
        } else {
          reject(respJson);
        }
      } catch (e) {
        reject(new Error(`js调用app出错: ${e.message}`));
      }
    });
  });
};

// 1、获取app信息
// 2、获取用户信息（包含位置信息字段）
// 3、位置信息

// 1、跳native页面
// 2、打开webview  支持是否自定义导航栏
// 3、关闭webview  支持关闭当前、所有
// 4、跳转小程序

// 1、分享（分享列表、指定分享)
// 2、支付(支付列表、指定支付)

```


```js
/**
  @param {*} name 函数名
  @param {*} callback 执行的回调方法
  H5 的逻辑注入到 APP 的函数方法 jsRefreshOrderStatus 中， 等到app触发了jsRefreshOrderStatus并且成功执行就会执行 H5 的事件
*/
registerJavascriptHandler("jsRefreshOrderStatus", () => { 
    // h5页面事件逻辑执行
});


/**
  @param {*} name 函数名
  @param {*} data 传递参数 
  H5 调用 APP 的函数方法 jsWebConfiguration 并传递参数
*/
baseAppCall("jsWebConfiguration", {
    showNavigator: false,
    backColor: "#FFFFFF",
    textColor: "#FFFFFF"
}).then((res)=>{
    
}).catch((error)=>{

})
```