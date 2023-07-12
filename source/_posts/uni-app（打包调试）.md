---
title: uni-app打包调试
data: 2022-04-20 16:39:45
tags: 
	- uni-app
categories: 框架
---

刚开始使用主题的时候肯定有不如意的地方，让你想优化都无从入手，列举一些改动的方法
<!-- more -->

### **调试**

这里举例夜神模拟器

<font color="#08c">1.到夜神模拟器文件所在目录<font color="red">\Nox</font>下，cmd打开控制台</font>
&emsp;adb connect 127.0.0.1:62001
&emsp;adb devices

<font color="#08c">2.打开夜神模拟器</font>

<font color="#08c">3.HBuilderX安装调试插件，系统环境变量Path下新建一个<font color="red">(C:\Users\EDY\Desktop)(*这里是你的路径*)</font>\HBuilderX\plugins\launcher\tools\adbs</font>

<font color="#08c">4.回到HBuilderX打开工具，运行配置，配置adb路径<font color="red">(C:/Program Files (x86)/Nox/bin)</font>这里是夜神模拟器的文件所在路径，记得对应到bin文件夹</font>

### **打包**

<font color="#08c">1.[开发者中心](https://dev.dcloud.net.cn/app/index?type=0)创建应用，生成<font color="red">App id</font>、创建<font color="red">线上证书</font></font>

<font color="#08c">2.HBuilderX发行点击app云打包耐心等待即可</font>


<!-- more -->
