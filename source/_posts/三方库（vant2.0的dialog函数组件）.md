---
title: vant2.0的dialog函数组件实现异步关闭
data: 2022-08-12 14：01
tags:
  - 三方库
---

dialog 弹出框，取消按钮、确认按钮实现异步关闭不互相干扰（当点击确认异步任务，依然可以点击取消按钮执行异步任务），需要在执行完确认按钮的异步任务之前，禁用取消按钮的点击事件，vant3.0 已经添加了 confirmButtonDisabled、cancelButtonDisabled 禁用确认、取消按钮的属性配置。

<!-- more -->

```js
import { Dialog } from 'vant'
/**
 * 通过节点来控制按钮的禁用状态，和样式
 * @param {String} e 取消cancel、确认confirm
 * @param {Boolean} flag 是异步执行结果，原本done回调接收的参数，false阻止关闭，默认关闭
 */
function disabledClose(e, flag = true) {
  const button = document.getElementsByClassName('van-hairline--top van-dialog__footer')[0]
    .childNodes
  // 确认按钮：禁用取消按钮、取消按钮置灰
  if (e === 'confirm') {
    button[0].disabled = flag ? true : false
    button[0].style.opacity = flag ? '0.5' : '1'
  }
  // 取消按钮：禁用确认按钮、确认按钮置灰
  if (e === 'cancel') {
    button[1].disabled = flag ? true : false
    button[1].style.opacity = flag ? '0.5' : '1'
  }
}

const confirm = Dialog.confirm
// 重新封装dialog.confirm事件，相当于做一层拦截器，再调用原生事件之前，先把我自定义的事件执行
Dialog.confirm = function (args) {
  if (args.beforeClose) {
    /*
        dialog.confirm的beforeClose获取
        接收两个参数action按钮值；done回调函数
    */
    const beforeClose = args.beforeClose
    /* 
        action是dialog弹出框cancel，confirm对应按钮点击值，
        done是关闭函数，done(false)阻止弹出框关闭，done()关闭弹出框
    */
    args.beforeClose = function (action, done) {
      disabledClose(action, true)
      /* 
         自定义donefn函数，接收done回调方法，再把自定义事件disabledClose塞进去在这个节点执行，
         donefn回调作为beforeClose的第二个参数植入
         done本身接收参数false，就是阻止弹框关闭，true就是默认值，关闭弹框
      */
      const donefn = function (data) {
        done(data)
        disabledClose(action, data)
      }
      beforeClose(action, donefn)
    }
  }
  confirm(args)
}
```

main.js 入口文件

```js
// 最后引入代码文件，正常使用
import 'utils/dialog.js'
```

App.vue 文件

```js
import { Dialog } from 'vant'
export default {
  method: {
    async bulleBox() {
      data.title = '中石化加油站'
      data.message = '您距离加油站较远，请到达加油站与加油员金额后付款'
      data.messageAlign = 'left'
      data.confirmButtonText = '刷新定位'
      data.cancelButtonText = '我知道了'
      data.confirmButtonColor = 'rgb(255, 104, 28)'
      data.beforeClose = this.locationBeforeClose
      Dialog.confirm(data)
    },
    // 定位异步关闭
    locationBeforeClose(action, done) {
      if (action === 'confirm') {
        new Promise(res => {
          setTimeout(() => {
            res(1)
          }, 1000)
        }).then(res => {
          if (res === 1) {
            done()
          }
        })
      }
      if (action === 'cancel') {
        setTimeout(() => {
          done()
        }, 0)
      }
    },
  },
}
```

<!-- more -->
