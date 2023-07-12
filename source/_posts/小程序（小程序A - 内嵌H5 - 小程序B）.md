---
title: 微信小程序（小程序A内嵌的H5跳转小程序B）
data: 2022-09-19 12:00
tags:
  - 微信小程序
categories: 框架
---

实现微信小程序原生支付。步骤流程拆解：小程序 A => webview 内嵌 h5 => 从 h5 跳回到小程序 A 的中转页面 => 在小程序 A 中转页面使用 wx.navigateToMiniProgram 方法跳转到小程序 B （这时会有提示弹框打开某某小程序、是否同意）=> 小程序 B 实现交互后使用 wx.navigateBackMiniProgram 方法返回小程序 A => 流程交互完毕

<!-- more -->

<font size=3 color='red'> 
难点：
</font></br>

<font size=3 color='red'> 
1.内嵌H5无法直接跳离打开第三方小程序的能力，只能小程序跳小程序（ wx.navigateToMiniProgram ）注：wx.navigateToMiniProgram 方法必须通过用户点击触发
</font></br>

<font size=3 color='red'> 
2.传参问题，必须JSON.stringify化
</font></br>

<font size=3 color='red'> 
3.小程序跳转小程序时，参数extraData需要用wx.getEnterOptionsSync()方法接收或者app.js的onLaunch接收（在页面的onLoad是接收不到的），path传递直接onload(option)接收
</font></br>

<font size=3 color='red'> 
4.小程序跳转小程序时，第二次跳转的时候，会有一个冷启动合热启动的概念，第一次小程序 A 打开 B （冷启动） 之后，B 会在后台进程挂着，下次 A 再次进入 B 时就是 （热启动）了，区别在哪呢，就是冷启动会触发 app.js 中的 onLaunch 和 wx.getLaunchOptionsSync() ，热启动不会触发了，要想获取第二次的数据只能通过 wx.getEnterOptionsSync() 获取
</font></br>

[小程序详细文档（wx.navigateToMiniProgram 方法）](https://developers.weixin.qq.com/miniprogram/dev/api/navigate/wx.navigateToMiniProgram.html) [（wx.getEnterOptionsSync 方法）冷启动合热启动的区别](https://developers.weixin.qq.com/miniprogram/dev/api/base/app/life-cycle/wx.getEnterOptionsSync.html)

<font size=5 color='black'>实际案例（小程序A内嵌H5创建订单跳转到小程序B支付） </font>


#### **1.web-view H5创建订单**

```js
// 用户交互点击创建订单
async createOrder() {
  try {
    let params = {} //创建订单所需参数
    const { path, appId, redirectUrl, extra } = await JSON.parse(
      decodeURIComponent(this.miniPath)
    ); //与A小程序的参数，对应
    let env = window.location.host.split(".")[0];
    let query = extra ? JSON.stringify(extra) : JSON.stringify({});
    let env_version = env === "api" ? "release" : "trial";
    const orderId = await api.platform.createOrder(params); // 获取接口订单号
    let miniProgram = { // 跳转的参数
      path: "/pages/index/index",
      appId: "wx6726e87e4e314e04",
      extraData: JSON.stringify({
        orderId,
        env,
        env_version,
        redirectUrl,
        extra: query,
        appId
      }),
      envVersion: env_version
    };
    // getParams把对象'&'拼接成字符串
    window.wx.miniProgram.navigateTo({
      url: `${path}?${this.getParams(miniProgram)}`
    });
  } catch {
    Toast("异常了，创建订单失败");
  }
}
```

#### **2.小程序B 接收参数完成支付调回小程序A**

```js
// onload接收参数
onLoad(options) { 
  const { scene } = wx.getLaunchOptionsSync()
  const { referrerInfo } = wx.getEnterOptionsSync()
  const { extraData } = referrerInfo
  console.log('extraData', referrerInfo, extraData, scene)
  // 1037 小程序打开小程序的场景值
  if (scene == 1037 && extraData?.orderId) {
    this.setData({
      orderId: extraData.orderId,
      extra: extraData,
    });
    this.getCashierPay_1()
    return
  }
}
// 获取登录信息，支付前置条件
async getCashierPay_1(){
  wx.login({
    success: res => {
      // 发送 res.code 到后台换取 sessionKey, sessionKey, unionId
      this.setData({
        loginData: res
      }) 
      // getOpenidAndSession_key把code给后台接口获取微信用户信息，会保存在app.js的公共数据globalData
      this.getOpenidAndSession_key(res.code).then(res => {
        if(res.code != 200) return
        this.getCashierPay_2()
      }).catch(() => {

      })
    }
  })
}
// 订单号反查微信收银台参数
async getCashierPay_2(){
  try {
    const { openid } = app.globalData.userInfo 
    // 后台接口订单号反查拉起微信收银台必须参数
    const { code, msg, data } = await api.getCashierPay(openid, this.data.orderId) 
    // 创建微信支付收银台的返回的参数示例
    // {
    //   "code": 200,
    //   "msg": "SUCCESS",
    //   "data": {
    //     "wxWebPayLastMoney": "0.95",
    //     "orderSign": "1000000125738087",
    //     "aliOrderData": null,
    //     "wxPayReqOutputI": {
    //       "timeStamp": "1663726624",
    //       "murl": null,
    //       "packageValue": "prepay_id=wx211017040962747a197a415359bf050000",
    //       "appId": "wx6726e87e4e314e04",
    //       "sign": "L74v9v0QWF9PM8U9UUbNVB9JKTYFn60Eranlr5fP+0DVnP7XcPpvcnUyfdMQfU0AZmOJWUfQyTD/s61fPA/AxDuWyRdJSttiepPCIVoAWcf7tHoHE51o/hdKLeBI82KbDlYIn6dn18D+Ib/bSyw2GMeZdn2OrpdaJnkGIHVT0U4xPxYCEvargiX2NtAJ44dUkPY8uXIugAYXg6rU+R2Al9UgwgmaROI2P1XEAkcxo/ZgvOkFJS5DQ9rw14eNinuPlJlEMvXP0W4TsT3n10QpEtm2AM5A6gkgnxo4+G4K2k3ziJ3e7o9EOCJtiArtGOF5HnWBjrqj1lqdzC4J2qft0A==",
    //       "signType": "RSA",
    //       "partnerId": null,
    //       "prepayId": "wx211017040962747a197a415359bf050000",
    //       "nonceStr": "8e19f724c938495ba5df7d523da4c3c4"
    //     },
    //     "stationId": 21019
    //   }
    // }
    if(code == 200) {
      this.setData({
        payData: data.wxPayReqOutputI,
      }) 
      // 微信支付
      this.wxpay_2(); 
    } else {
      wx.showModal({
        showCancel: false,
        title: "订单查询错误",
        content: msg ? msg : "系统异常，解码错误"
      });
    } 
  } catch (error) {
    
  }
},
// 拉起微信收银台支付
async wxpay_2 () { 
  let that = this; 
  let { timeStamp, nonceStr, packageValue, sign, signType } = that.data.payData
  var mSignType = "MD5";
  const { redirectUrl, appId, extra } = this.data.extra;
  extra.orderId = this.data.orderId
  wx.requestPayment({
    timeStamp: timeStamp + "",
    nonceStr: nonceStr + "",
    package: packageValue,
    signType: mSignType,
    paySign: sign + "",
    success: function (res) {
      console.log('支付成功',res)
      // 返回对应的小程序，其实这里应该使用的是wx.navigateBackMiniProgram，不然交互体验非常差、一次流程总共出现两次提示确认弹框、提示用户即将打开小程序是否同意
      extra.orderStatus = 1
      wx.navigateToMiniProgram({
        appId: appId,
        path: redirectUrl,
    　  extraData: extra,
    　  envVersion: 'trial',// 打开正式版
      })
      //   wx.navigateBackMiniProgram({
      // 　  extraData: extra,
      //     success(res) {
      //       console.log(res) // 打开成功    
      // 　　 },
      //   })   
    },
    fail: function (res) {
      console.log('支付失败',res)
      // 返回对应的小程序
      extra.orderStatus = 0
      wx.navigateToMiniProgram({
        appId: appId,
        path: redirectUrl,
        extraData: extra,
    　  envVersion: 'trial',// 打开正式版
      })
    };
  })
}
```

<!-- more -->
