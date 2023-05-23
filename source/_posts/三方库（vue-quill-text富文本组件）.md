---
title: vue-quill-text富文本踩坑
data: 2023-05-26 14：01
tags:  
    - 三方库
---

实现功能点踩坑：1、基础使用；2、插入标签；3、复制黏贴格式化文本、禁止图片复制；4、本地资源上传重写upload组件文件上传

<!-- more -->
#### Quill方法继承重写

```js
// 继承插入标签时埋入自定义属性、节点事件 

import { Quill } from 'vue-quill-editor'
import $ from 'jquery'
let BlockEmbed = Quill.import('blots/block/embed')

let openEditEvent = null 
export function setThis(event) { // 把vue实例方法传递进来
  openEditEvent = event
}

// 继承img标签
export class ImageBlot extends BlockEmbed {
  static create(value) {
    let node = super.create()
    node.setAttribute('style', value.style)
    node.setAttribute('src', value.url)
    node.setAttribute('data', value.data)
    node.setAttribute('type', value.type)
    return node
  }
  static value(node) {
    return {
      style: node.getAttribute('style'),
      url: node.getAttribute('src'),
      data: node.getAttribute('data'),
      type: node.getAttribute('type')
      // class: node.getAttribute('class')
    }
  }
}
ImageBlot.blotName = 'image'
ImageBlot.tagName = 'img'

// 继承span标签
export class FillIn extends BlockEmbed {
  static create(opt) {
    var node = super.create()
    $(node).css({
      padding: '0 0.5em',
      'text-decoration': 'underline',
      color: '#FF5E2A',
      display: 'inline'
    })
    $(node).text(opt.content || '点击修改填空答案')

    $(node).on('click', event => {
      event.stopPropagation()
      openEditEvent($(node))
    })
    return node
  }

  static value(node) {
    return {
      content: $(node).text().trim()
    }
  }
}
FillIn.blotName = 'span'
FillIn.tagName = 'span'
```

<font color="#FF5E2A">使用示例 ~ vue2 </font>

```js

// 标签
<quill-editor :ref="`editors`"></quill-editor>

import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { quillEditor, Quill } from 'vue-quill-editor'
import { ImageBlot } from './ImageBlot.js'
Quill.register(ImageBlot, true) // 插入图片

let quillEditor = this.$refs[`editors`].quill
let index = quillEditor.getSelection()?.index || 0 //光标位置
let blotName = 'span' //上面定义的blotName
quillEditor.insertEmbed(index, blotName, {
  style: 'width:50px;height:50px;display:inline-block;position:relative',
  url: uri,
  type: 'image',
  data: uri
})
```

#### 复制黏贴格式化文本、禁止图片复制

```js

// 标签
<quill-editor :ref="`editors`" :options="editorOptions"></quill-editor>

const editorOptions = {
  modules: {
    toolbar: [],
    clipboard: {
      matchers: [[Node.ELEMENT_NODE, handleCustomMatcher]]
    }
  },
  placeholder: '我是默认值'
}

const handleCustomMatcher = (node, delta) => {
  // 定时器的原因是回显第一次的时候会误杀、所以loading是等页面数据加载完true之后3s时间才禁止复制黏贴
  if (!this.loading) {
    clearTimeout(this.timer)
    this.timer = setTimeout(() => {
      this.loading = true
    }, 3000)
    return delta
  }
  const opsList = []
  delta.ops.forEach(op => {
    if (op.insert && typeof op.insert === 'string') {
      // 正常p标签文本
      opsList.push({
        insert: op.insert
      })
    } else if (op.insert?.span) {
      // span是带样式的标签、提取纯文本
      opsList.push({
        insert: op.insert.span.content
      })
    } else if (op.insert?.image?.data == null) {
      // 没有data属性说明不是插入的图标、上面插入data属性到这里可以做判断、不是常规合法插入图片那就是cv的，给他提示禁止
      this.$message('禁止复制图片')
    }
  })
  delta.ops = opsList
  return delta
}
```

#### 本地资源上传重写upload组件文件上传

<font color="#FF5E2A">先把工具栏禁用隐藏</font>

```css
/deep/ .ql-toolbar {
  display: none !important;
}
```

<font color="#FF5E2A">文件上传js文件 </font>

```js
import md5 from 'js-md5'
import axios from 'axios'

/**
 * 组合使用
 * @param { Object } config  判断条件回调
 * @return { String }
 */
export const uploadFileEvent = config => {
  return new Promise(resolve => {
    const input = document.createElement('input')
    input.type = 'file'
    input.onchange = async () => {
      const file = input.files[0]
      let flag = await beforeUpload(file, config)
      if (flag) {
        let data = await httpRequest(file)
        resolve(data)
      }
    }
    input.click()
  })
}

/**
 * url链接展示文件墙
 * @param { Array } fileUrl url链接数组
 * @return { Array<Object> } 数组
 */
export const fileUrlToBase64 = fileUrl => {
  return fileUrl.map(item => {
    let filename = item.split('/')?.pop() || '*' // 从 URL 中获取文件名
    let blobUrl = URL.createObjectURL(new Blob([''], { type: '' })) // 创建一个 Blob 对象并获取 URL
    return {
      name: filename,
      url: blobUrl
    }
  })
}
/**
 * 上传校验
 * @param { String, Object } file params  二进制 | 回调
 * @return { Boolean }
 */
export const beforeUpload = async (file, params = {}) => {
  const defaultParams = {
    maxSize: 2000,
    suceessCallback: () => {
      return new Promise(resolve => {
        resolve(`成功`)
      })
    },
    errorCallback: () => {
      return new Promise(resolve => {
        resolve(`上传图片大小不能超过${2000}KB!`)
      })
    },
    doneCallback: () => {
      return new Promise(resolve => {
        resolve(`完成`)
      })
    }
  }
  const { maxSize, suceessCallback, errorCallback, doneCallback } = Object.assign(params, defaultParams)
  let flag
  if (file.size / 1024 > maxSize) {
    errorCallback && (await errorCallback())
    flag = false
  } else {
    suceessCallback && (await suceessCallback())
    flag = true
  }
  doneCallback && doneCallback()
  return flag
}
/**
 *
 * @param { String } file  二进制
 * @return { Promise }
 */
export function readBlobAsArrayBuffer(file) {
  return new Promise(resolve => {
    const reader = new window.FileReader()
    reader.onload = event => resolve(event.target.result)
    reader.readAsArrayBuffer(file)
  })
}
/**
 * 上传方法
 * @param { String } file  二进制
 * @return { Promise }
 */
export const httpRequest = async file => {
  let rf = await readBlobAsArrayBuffer(file)
  const MD5 = md5(rf)
  const data = await upload('http://10.88.18.164:30000/api/v2/storage', {
    file_key: 'file',
    filename: file.name,
    hash: MD5,
    mime_type: file.type,
    size: file.size,
    storage: {
      channel: 'public'
    }
  })
  let fileUrl
  if (file.size / 1024 > 2000) {
    let form = new FormData()
    form.append('file', file.file)
    fileUrl = await uploadFileForm(data.uri, form, {
      ...data.headers
    })
  } else {
    fileUrl = await uploadFile(data.uri, rf, {
      ...data.headers
    })
  }
  return fileUrl
}

/**
 * 引入axios调用
 */
const upload = async (url, args) => {
  let res = await axios.post(url, args, {
    headers: {
      // 暂时写死token
      Authorization:
        'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIzIiwianRpIjoiZDJiNjkyNzA5MDNjMmU0ZmRkMzI1YzQzNDkzYjY0YWNhN2U0OGYxY2JjMjgxNDkwOGIxNGM5ODhiOTQxOGJlYTQyMjMwYzdjNzg4NTcwOTIiLCJpYXQiOjE2ODIyMTY3MDAuNjQwMDg0LCJuYmYiOjE2ODIyMTY3MDAuNjQwMDg3LCJleHAiOjE2ODc0MDA3MDAuNTk2NjAxLCJzdWIiOiIxIiwic2NvcGVzIjpbIioiXX0.qdIyAV-z0Hto59SN1yUUO0irD36slDpneffyCGm86VPFSyOdIiVxIIAJba6FfUAP1xBrlzuz0A-loGxpyTvgaZ_BJKIOcJOVeePAXeDymgBMILlC-vP7ByIco9UyJG1K_WZMtbtipn-69qhOXRiMxwoNO46ZN3lq0BiAaoDQWXdkCndE1AqA6mQBAGfDxEdyg8hBW0YgNOADgB09giUea1S-nrj8sSm_Myvm7Q-QesSUJQnl1y2kWzDQaLf-ffKuXJum7Wcx-0mqO3yVmJoetVq4KSVapirMpYhq4HXhlRztb62Qngj-EbcG4kJGA4KYWjXUOCT9t6zel9hLB8bkjrOimStq1dVRicj_EbXvOuzhOY3vqjIF0U7JEK_mMsKHD8C46EUEHiGOFX5QSUEUQPJ6vIePsBO7d6FJM8N7aQpsXP1le6uhFh8YK6WEJT-L9NJxvtWJQ8UmkIBQRRDRMOH8DiaRmOtUj4TY11OccA9hlArBbEOSYbHCJN22sN2Mdgv4gAwFUGZzotqN2AzbPffwJqTIeY8tyuxRshT0no41ybzBqbY-0zVyhSQPEfDxgHvV8OINymaph1_o7v1A9YysMzXvYbz3xINVxdSJUc-xfCCrliSpNLm1EpBrxChjwlM0hM0RJie3x8pGl-XrxarWBTSwtuBCBimPpPUnhPM'
    }
  })
  return res.data
}
const uploadFileForm = async (url, args, config) => {
  let res = await axios.post(url, args, {
    headers: {
      'Content-Type': 'multipart/form-data',
      ...config
    }
  })
  return res.data
}
const uploadFile = async (url, args, config) => {
  let res = await axios.put(url, args, {
    headers: {
      'Content-Type': 'multipart/form-data',
      ...config
    }
  })
  return res.data
}
```

<font color="#FF5E2A">用 insertEmbed 方法插入 imgae 类型的标签把 url 设置节点 src</font>

<font color="red">insertEmbed方法插入触发两次问题</font>

```js
this.insertingFlag = true
await new Promise(reslove => {
  quillEditor.insertEmbed(index, 'image', {
    style: 'width:50px;height:50px;display:inline-block;position:relative',
    url: uri,
    type: 'image',
    data: uri
  })
  reslove()
})
this.insertingFlag = false
// 改成异步写法，原先就有的bug
```

#### 聚焦输入框高亮

```html
<div class="vue-quill-editor" :class="{ active: focused }">
  <quill-editor ref="editors" @focus="onFocus" @blur="onBlur"></quill-editor>
</div>
```
```js
onFocus() {
  this.focused = true
},
focusEditor(id) {
  this.$refs[`editors`].focus()
},
```

```css
.vue-quill-editor.active {
  /deep/ .ql-container.ql-snow {
    border-color: #ff5e2a;
  }
}
```

#### 关于vue-quill-text

<font color="#FF5E2A">属性方法</font>

```js
v-model //值
@input //输入
@focus //聚焦
@blur //失焦
options //配置项
```
<font color="#FF5E2A">纯文本提取 、 直接渲染dom通过节点获取、更简单</font>

<!-- more -->