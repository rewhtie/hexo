---
title: hexo+gitee搭建部署采坑总结
data: 2020-12-19 16:39:45
tags: hexo
---
相信各位使用hexo框架搭建个人博客时遇到很多麻烦，列举一些出来让大家避免一些坑
<!-- more -->
## hexo搭建

#### 1.[node.js安装](https://nodejs.org/zh-cn/)

安装cpnm、镜像源选择淘宝镜像

```js
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

先在node安装路径根目录下新建node_global和node_cache两个文件夹

```js
npm config set prefix "D:\nodejs安装路径\node_global"
npm config set cache "D:\nodejs安装路径\node_cache"
```

系统环境变量新建如图



这里node环境一定要配置好，还有npm的路径也不要搞错，不然后续安装hexo时安装成功但hexo命令会提示找不到，真的奇怪，没办法只能删node重新来，node要删除干净不然也很麻烦。

#### 2.[git安装](https://git-scm.com/downloads)

官网直接下载安装即可，也什么都不要配，防止添乱，也不需要配，win10输入一次git账号后都不用重复输入了

#### 3.hexo安装

```js
npm install hexo-cli -g #全局安装hexo
hexo -v #查看hexo版本
```

这里最大的坑就是安装后提示我,如图



网上有些文章说可以不理，有些又说不能不理，记住这里是hexo安装是不成功的，为什么会不成功，别人的命令教程也是这样为什么可以，所以不要再去查东查西了，直接多配置几遍node环境，环境不一样命令敲进去结果不一样

#### blog项目

直接建立一个文件夹，路径名字随意，打开在此文件夹下鼠标右键git Bash Here

```bash
$hexo init #初始化一个项目
$hexo server -p 8080 #win10的4000端口被占用改其他端口运行，如果不行再改其他的端口
```

生成以下文件

```.
├── .deploy       # 需要部署的文件
├── node_modules  # 项目所有的依赖包和插件
├── public        # 生成的静态网页文件
├── scaffolds     # 文章模板
├── source        # 博客正文和其他源文件等都应该放在这里
|   ├── _drafts   # 草稿
|   └── _posts    # 文章
├── themes        # 主题
├── _config.yml   # 全局配置文件
└── package.json  # 项目依赖信息
```

## gitee部署

#### 1.[gitee新建仓库](https://gitee.com/)

仓库名，路径名跟用户名一样就行，语言选Javascript，勾选reademe初始化

#### 2.blog配置

修改本地仓库推到gitee仓库

```bash
# blog根目录_connfig.yml
deploy:
  type: git 
  repo: https://gitee.com/lin-mingqi/linmingqi.git #gitee仓库路径
  branch: master
```

#### 3.主题修改

```bash
# blog根目录_connfig.yml
theme: 主题文件夹名
```

无法上传theme主题文件夹到github上，[上传theme主题](https://zhuanlan.zhihu.com/p/349280018)


#### 4.部署

```bash
$npm install hexo-deployer-git --save #安装hexo部署到git page的deployer
$hexo g -d 
#gitee仓库页面有个服务Gitee Pages点击更新，访问链接查看效果
```

#### 5.markdown语法

语法都是一些简单的东西，但图片插入是一个坑，如图





