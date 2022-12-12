### hexo搭建

```js
npm install hexo-cli -g #全局安装hexo
hexo -v #查看hexo版本
```


### 创建新帖子

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### 运行服务器

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### 生成静态文件

需要把.gitignore文件的.deploy*/注释掉

``` bash1
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### 部署到远程站点

``` bash
$ hexo deploy
```