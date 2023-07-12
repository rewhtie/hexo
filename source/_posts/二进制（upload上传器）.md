---
title: upload上传器
data: 2022-02-23 12:00
tags: 
- 文件上传
categories: 组件
---

上传图片限制宽高、大小；上传execl解析数据

<!-- more -->

## 图片自定义上传（限制宽高、大小）
<font color="#08c" size=3>引用element组件</font>
```html
<!-- 单个图片上传，重新上传覆盖已有 -->
 <el-upload ref="uploadImg" :show-file-list="false" class="upload-demo" :list="1" :http-request="uploadFile" :before-upload="beforeUpload" accept=".jpg, .png, .gif" list-type="picture-card">
    <img v-if="img" :src="img" class="avatar" />
    <el-icon v-else class="avatar-uploader-icon"><plus /></el-icon>
    <template #tip>
        <div class="el-upload__tip" style="color: red">宽度750，高度不限，大小不得超过1M</div>
    </template>
</el-upload>
```
</br>
<font color="#08c" size=3>封装一个校验图片像素的方法</font>

```js
/**
 * 判断图片大小
 * @param {二进制文件} file 图片二进制流
 * @param {Number} width 图片像素宽度
 * @param {Number} height 图片像素高度
 * @param {Number} size 图片内存大小
 * @param {Array} pass 上传文件格式
 * @param {String} msg 提示文案
 */
import { ElMessage } from 'element-plus';
export const imgSize = (file, width, height, size, pass, msg) => {
  return new Promise((resolve, reject) => {
    const extension = file.name.substring(file.name.lastIndexOf('.') + 1);
    const size1 = file.size / 1024 / 1024;
    const url = window.URL || window.webkitURL;
    const img = new Image();
    img.onload = () => {
      // 图片比例校验
      console.log(img.width, img.height);
      if (pass.includes(extension)) {
        if (size1 > size) {
          if ((img.width == width || width == null) && (img.height == height || height == null)) {
            resolve(ElMessage.success('上传成功'));
          } else {
            reject(ElMessage.error(`上传宽度${width == null ? '不限' : height}、高度${height == null ? '不限' : height}的图片`));
          }
        } else {
          reject(ElMessage.error(`文件不能超过${size}${size >= 1 ? 'M' : 'K'}`));
        }
      } else {
        reject(ElMessage.error('文件格式不正确'));
      }
    };
    img.src = url.createObjectURL(file);
  })
};
```
</br>
<font color="#08c" size=3>文件上传的方法使用</font>

```js
// 上传之前校验
const beforeUpload = (file) => {
    return imgSize(file, 200, 200, 1, ['jpg', 'png', 'gif'], '特殊提示');
};
// 上传
const uploadFile = ({ file }) => {
    img = new FormData();
    img.append('file', file);
    // 这里是接口post请求,'Content-Type': 'multipart/form-data',
    upLoadImgApi(img);
};
// 上传图片的接口（一般不支持判断图片像素，支持更省事）
const upLoadImgApi = (img) => {
    api
    .postUpload(`activity/file/service/uploadImg`, img)
    .then((res) => {
        console.log(res);
    })
    .catch((err) => {
        consoel.log(err)
    });
}
```
</br>

## execl本地上传解析数据
<font color="#08c" size=3>这里记得用on-change，不然报<font color="red" size=3>parameter 1 is not of type ‘Blob‘.“</font>错误</font>
<font color="#08c" size=3>注：upload上传组件我需要的是file二进制文件，被封装一层导致文件格式报错</font>

```html
<template>
  <el-upload ref="upload" class="upload-demo" :on-change="uploadExcel" :show-file-list="false" :auto-upload="false">
    <template #default>
      <el-button type="primary">上传execl</el-button>
    </template>
  </el-upload>
</template>
```

```js
import * as XLSX from 'xlsx';
const readWorkbookFromLocalFile = (file, callback) => {
  console.log(file);
  const reader = new FileReader();
  reader.onload = (e) => {
    const data = e.target.result;
    const workbook = XLSX.read(data, {
      type: 'binary',
    });
    if (callback) {
      callback(workbook);
    }
  };
  reader.readAsBinaryString(file.raw);
};
const readWorkbook = (workbook) => {
  console.log('执行readWorkbook');
  const sheetNames = workbook.SheetNames; // 工作表名称集合
  const worksheet = workbook.Sheets[sheetNames[0]]; // 这里我们只读取第一张sheet
  const csv = XLSX.utils.sheet_to_csv(worksheet).split('\n');
  const excelArr = [];
  csv.pop();
  for (const one of csv) {
    const arr = one.split(',');
    // 就是获取数据的加工
    const item = { stationId: '', stationName: '' };
    item.stationId = arr[0];
    item.stationName = arr[1];
    excelArr.push(item);
  }
  console.log(excelArr);
};
const uploadExcel = (file) => {
  const f = file;
  const extension = f.name.substring(f.name.lastIndexOf('.') + 1);
  if (!['xlsx', 'xls'].includes(extension)) {
    console.log('仅支持读取xlsx,xls格式！');
    return;
  }
  readWorkbookFromLocalFile(f, (workbook) => {
    readWorkbook(workbook);
  });
};

```

<!-- more -->
