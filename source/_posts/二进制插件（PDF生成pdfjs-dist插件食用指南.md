---
title: pdfjs-dist插件食用指南
data: 2022-06-06 12:00
tags:
  - 文件
  - 三方库
categories: 插件、依赖
---

pdfjs-dist基础使用，以及 pdf 文件流，以图片墙形式展示

<!-- more -->


### 一、安装依赖引入

```bash
npm install pdfjs-dist
```

```ts
import * as PDFJS from 'pdfjs-dist/legacy/build/pdf.js'
import { PDFDocumentProxy } from 'pdfjs-dist/types/src/display/api'
PDFJS.GlobalWorkerOptions.workerSrc = require('pdfjs-dist/build/pdf.worker.entry')
```

### 二、pdf 在线URL文件解析

```ts
const loadFile = () => {
  // const loadingTask = PDFJS.getDocument('https://file-test.midiplusedu.com/2023/06/20/4GmLczjSQtWcxduijTui9FAyl2j73trDRTm1IbvKDE7o3HdIXsBUmODAdh9ZkAdD.pdf')
  const loadingTask = PDFJS.getDocument(textBook.value)
  loadingTask.promise.then((pdf) => {
    total.value = pdf.numPages
    renderPage(pdf)
  })
}
const renderPage = async (pdf: PDFDocumentProxy) => {
  for (const i of [...new Array(pdf.numPages).keys()]) {
    const canvas = document.createElement('canvas') as HTMLCanvasElement
    canvas.id = `canvas_${i + 1}`
    // 页数默认加1   
    const page = await pdf.getPage(i + 1)
    // 缩小倍数
    const viewport = page.getViewport({ scale: 1 })
    const scale = window.innerHeight / viewport.height 
    const context = canvas.getContext('2d') as any
    const scaleViewport = page.getViewport({ scale: scale })
    canvas.height = scaleViewport.height
    canvas.width = canvasWidth.value = scaleViewport.width
    const renderContext = {
      canvasContext: context,
      viewport: scaleViewport,
    }
    page.render(renderContext)
    canvasRef.value.appendChild(canvas)
  }
}
```


### 四、示例、ant上传组件配合pdfjs-dist展示文件墙

>上传PDF，需要展示所有页，形成图片墙
 更换上传文件需要把文件墙清空
 回显的时候文件墙需要解析


```html
<a-upload
  v-model:file-list="fileList"
  :show-upload-list="showUploadList"
  :list-type="fileType === 'file' ? 'text' : 'picture-card'"
  :customRequest="downloadFilesCustomRequest"
>
  <!-- pdf图片方式 -->
  <template v-if="fileType === 'pdf'">
    <loading-outlined v-if="loadingPDF"></loading-outlined>
    <div ref="canvasRef" v-show="thumbUrl" class="flex">
      <canvas id="canvas" width="0" height="0"></canvas>
    </div>
  </template>
</a-upload>
```

```js
// 自定义上传文件事件
const downloadFilesCustomRequest = async ({ file }) => {
  let suffix = file.name.split('.').slice(-1)[0].toLowerCase()
  // 检验文件格式
  let accept = props.accept
  if (!accept.includes(suffix)) {
    let uploadType = accept.join('、')
    message.warning(`文件格式不正确，请选择${uploadType}格式文件！`)
    return false
  }
  const isLt2M = file.size / 1024 / 1024 < props.fileSize
  // 判断文件大小
  if (!isLt2M) {
    message.warning(`文件大小不能超过${props.fileSize}MB!`)
    return false
  }
  let param = new FormData();
  param.append("file", file);
  param.append("fileName", file.name);
  param.append("thumb", "true");
  // 二进制文件请求接口，返回链接
  const api = (props.uploadApi as Function) || uploadFileApi;
  try {
    const { code, data } = await api(param, props.parameter?.type);
    if (code === 200) {
      emit("upload", {
        state: true,
        name: file.name,
        ...data,
      });
    }
  } catch (err) {
    emit("upload", {
      state: false,
    });
  }
}
// 获取标签节点，等同于vue2的this.$ref
const canvasRef = ref()
// 文件读取很慢，需要一个loading控制加载状态
const loadingPDF = ref < boolean > false
// 读取pdf文件流
const pdf2html = async url => {
  let canvas
  // 上传文件不止是pdf，也可能是图片，需要校验 
  if (url.indexOf('.pdf') != -1) {
    loadingPDF.value = true
    // 删除所有的子节点，pdf一个文件一般有n页，需要展示n张图片，所以更换文件时需要把所有子节点全部删除
    for (var k = canvasRef.value.childNodes.length - 1; k >= 0; k--) {
      canvasRef.value.removeChild(canvasRef.value.childNodes[k])
    }
    const pdf = await PDFJS.getDocument(url).promise
    loadingPDF.value = false
    // 遍历渲染pdf文件
    for (const i of [...new Array(pdf.numPages).keys()]) {
      canvas = document.createElement('canvas')
      canvas.id = 'canvas'
      // 页数默认加1   
      const page = await pdf.getPage(i + 1)
      // 缩小倍数
      const scale = 0.18
      const viewport = page.getViewport({ scale })
      const context = canvas.getContext('2d')
      canvas.height = viewport.height
      canvas.width = viewport.width
      const renderContext = {
        canvasContext: context,
        viewport: viewport,
      }
      page.render(renderContext)
      canvasRef.value.appendChild(canvas)
    }
  } else {
    canvas = document.createElement('img')
    canvas.id = 'canvas'
    canvas.src = url
    canvas.style = 'max-height: 128px;'
    for (var k = canvasRef.value.childNodes.length - 1; k >= 0; k--) {
      canvasRef.value.removeChild(canvasRef.value.childNodes[k])
    }
    canvasRef.value.appendChild(canvas)
  }
}
```

<!-- more -->
