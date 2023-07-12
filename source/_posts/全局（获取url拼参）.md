---
title: URL获取参数
tags: 全局函数
categories: 函数
---

url 拼接参数的时候，页面需要获取地址栏上的参数

<!-- more -->

#### 1.获取 url 后面?拼接的参数（会自动解码）

```js
/**
 * 获取url上的指定属性的值
 * @param {String} name 属性名称
 * @param {String} url 指定或者当前页面地址
 */
export const getUrlParam = (name, url = location.href) => {
  let reg = new RegExp('(^|&)' + name + '=([^&#]*)(&|#|$)', 'i')
  let r = (url.split('?')[1] || window.location.search.substr(1)).match(reg) // 获取地址"?"符后的字符串并正则匹配
  let context = ''
  if (r != null) {
    context = r[2]
  }
  reg = null
  r = null
  return context == null || context === '' || context === 'undefined'
    ? ''
    : decodeURIComponent(context)
}
// url地址：www.baidu.com?token=404
getUrlParam('token') //输出404
```

#### 2.对象拆分成字符串（用'&'隔开）

```js
//用&拼接对象成字符串
function getParams(params) {
  let paramStr = ''
  Object.keys(params).forEach(item => {
    if (paramStr === '') {
      paramStr = `${item}=${params[item]}`
    } else {
      paramStr = `${paramStr}&${item}=${params[item]}`
    }
  })
  console.log(paramStr)
  return paramStr
}
let params = { a: 1, b: 2 }
getParams(params) //输出：a=1&b=2
```

#### 3.获取url上的参数拆分为对象

```js
function getParams(url) {
  var obj = {}
  if (url.indexOf('?') != -1) {
    var temp1 = url.split('?')
    var pram = temp1[1]
    var keyValue = pram.split('&')
    for (var i = 0; i < keyValue.length; i++) {
      var item = keyValue[i].split('=')
      var key = item[0]
      var value = item[1]
      obj[key] = value
    }
  }
  return obj
}
let a = 'qwe?www=111&qqq=222'
console.log(getParams(a)) //{www: "111", qqq: "222"}
```

<!-- more -->
