---
title: execl文件流读取
data: 2022-03-11 12:00
tags: 
- 文件上传
---

execl二进制文件流读取；本地execl读取数据

<!-- more -->
## 1.安装依赖
```js
npm install xlsx
npm run axios
```
## 2.先用axios获取xlsx二进制数据，再转成html

```js
import xlsxStyle from './xlsxStyle';	//  html 样式标，见下方
const xlsx2html = async (url) => {
  const { data } = await axios.get(url, { responseType: 'arraybuffer' });
  const workbook = XLSX.read(data, { type: 'array' });
  let html = template;
  let nav = '';
  for (const sheetName in workbook.Sheets) {
    try {
      const sheet = workbook.Sheets[sheetName];
      const sheetHtml = XLSX.utils.sheet_to_html(sheet);
      html += `
        <div id="${sheetName}">
          <div class="title">${sheetName}</div>
          ${sheetHtml.match(/<table>[\s\S]*<\/table>/)[0].replace('<table>', `<table id = "${sheetName}">`)}
        </div>
      `;
      nav += `<a href="#${sheetName}">${sheetName}</a>`;
    } catch (err) {}
  }
  document.write(`<nav id="nav">${nav}</nav>${html}`);
};

xlsx2html('http://6c8b8fa4d995353581176d6fe667a993.lanprp.sz.yontoys.com:898/api/v2/file/read?file_hash=a84aec324c9f4b6eb3b9c3e64c8d75c1&token=f24970fc9f70c81e89810d2c78432dfe');
```

## 3.xlsxStyle 自定义样式表

```js
const template = `
<style type="text/css">
  html,
  body {
    margin: 0;
    padding: 0;
  }
  body {
    padding-bottom: 100px;
  }
  #nav {
    position: fixed;
    bottom: 0;
    border-top: 1px solid #cbcbcb;
    left: 0;
    line-height: 50px;
    background: #fff;
    width: 100vw;
    overflow-x: auto;
  }
  a {
    text-decoration: none;
    margin-left: 20px;
  }
  .title {
    font-size: 30px;
    font-weight: bold;
    padding-left: 20px;
    height: 50px;
    line-height: 50px;
  }
  table {
    width: 100vw;
    height: 100vh;
    overflow: auto;
    border-collapse: collapse;
    border-spacing: 0;
  }
  td,
  th {
    padding: 0;
  }
  table {
    border-collapse: collapse;
    border-spacing: 0;
    empty-cells: show;
    border: 1px solid #cbcbcb;
  }
  table caption {
    color: #000;
    font: italic 85%/1 arial, sans-serif;
    padding: 1em 0;
    text-align: center;
  }
  table td,
  table th {
    border-left: 1px solid #cbcbcb;
    border-bottom: 1px solid #cbcbcb;
    font-size: inherit;
    margin: 0;
    overflow: visible;
    padding: 0.5em 1em;
  }
  table thead {
    background-color: #e0e0e0;
    color: #000;
    text-align: left;
    vertical-align: bottom;
  }
  table td {
    background-color: transparent;
  }
</style>
`;
```

<!-- more -->
