---
layout: post
title: html2canvas、htmlToImage食用指南
date: 2023-07-12 15:30:02
tags: 三方库
categories: 插件、依赖
---

获取元素生成图片

<!-- more -->

```bash
npm install html2canvas
npm install html-to-image
```

### 一.html2canvas使用

```ts
import html2canvas from 'html2canvas'
const htmlToImage = async () => {
	const body = iframeContent.querySelector('body')
	const canvasEl = await html2canvas(body)
	const link = canvasEl.toDataURL('image/jpg')
	const img = new Image() as HTMLImageElement
	img.src = link
}
```

### 二.htmlToImage使用

```ts
import * as htmlToImage from 'html-to-image'
const htmlToImage = async () => {
	const baseView = (document.querySelector('#base-view') as HTMLDivElement).cloneNode(true) as HTMLDivElement
	const screenSlide = (baseView.querySelector('.current') as HTMLDivElement)
	const canvas_2 = (document.querySelector('.writing-board-tool') as HTMLDivElement)
	const canvas = new Image() as HTMLImageElement
	canvas.src = await htmlToImage.toPng(canvas_2)
}
```

### 三.html2canvas和htmlToImage的区别

> 1.html2canvas可以对iframe生效、htmlToImage对其会报错（应该不能直接转换成图片、需要将其转换成canvas再进行图片导出）
2.[htmlToImage](https://www.npmjs.com/package/html-to-image)更全面
- toPng
- toSvg
- toJpeg
- toBlob
- toCanvas
- toPixelData


### 四.iframe生成图片

<font color='red'>需要先获取iframe的contentWindow，不然获取的iframe区域是空白的</font>

```ts
nextTick(async () => {
	const baseView = document.querySelector('#base-view') as HTMLDivElement
	const screenSlide = baseView.querySelector('.current') as HTMLDivElement
	await new Promise((resolve) => {
		$(screenSlide).ready(resolve)
	})
	const iframe = $('iframe')[0] as any
	const iframeContent = iframe.contentDocument || iframe.contentWindow.document
	const body = iframeContent.querySelector('body')
	const canvasEl = await html2canvas(body)
	const link = canvasEl.toDataURL('image/jpg')
	const img = new Image() as HTMLImageElement
	img.src = link
	img.style.width = '100%'
	img.style.height = '100%'
	nodeViewRef.value = img as HTMLImageElement
})
```

<!-- more -->