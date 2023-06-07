---
title: 功能模块（html-to-image生成图片）
date: 2023-06-17 14:04:02
tags: 功能模块
--- 

使用html-to-iamge插件依赖，html元素转换成图片

<!-- more -->

```ts
import { toPng, toJpeg, toBlob, toPixelData, toSvg } from 'html-to-image'

export const htmlToCanvasToBaseUrl = (node = <HTMLDivElement>document.body) => {
  return new Promise((reslove) => {
    htmlToImage.toPng(node).then(function(dataUrl) {
      const img = new Image() as any
      img.src = dataUrl
      document.body.appendChild(img)
      reslove(dataUrl)
    }).catch(function(error) {
      console.error('oops, something went wrong!', error)
    })
  })
}
```

html-to-image遇到iframe元素会报错，可以试试 html2canvas 插件依赖解决 iframe 转换报错问题，功能实现差不多

<!-- more -->