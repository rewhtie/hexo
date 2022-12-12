---
title: git食用指南
data: 2021-11-26 14：01
tags: 
    - git 
---

工作必备的管理工具，好的项目git管理肯定十分严谨。

<!-- more -->
## git 常用命令

```js

git add //'文件列表' 追踪文件

git init //初始化git仓库

git status //产看文件状态

git commit -m //'提交信息'  向仓库提交代码

git log 查看提交记录

git checkout 文件 //用暂存区的文件覆盖工作目录中的文件

git rm --cached 文件 //将文件从暂存区中删除

git rest --hard commitID //（ID就是git log查看）将git仓库指定的更新记录恢复出来，并且覆盖暂存区和工作目录

git branch //查看文件

git branch //'分支名称 '创建分支

git checkout //'分支名称' 切换分支（分支保存提交才能转其他分支才不会出错）

git merge //'来源分支' 合并分支

git merge --abort //取消合并

git branch -d //'分支名称' 删除分支（分支被杀出后才允许删除）-D强制删除

git stash //存储临时改动

git stash pop  //恢复改动

git push //'远程仓库地址' 分支名称

git push -u //'远程仓库地址' 分支名称

　　-u //记住推送地址及分支，下次推送只需要输入 git push 即可

git remote add //'远程仓库地址别名' 远程仓库地址

git push //远程仓库地址别名 

//克隆操作（没有本地仓库的情况下使用）

git clone //'远程仓库地址'

cd //目录名称 切换

git pull //远程仓库地址 分支名称

ssh-keygen //生成密钥

//密钥存储目录：C:\Users\用户\.ssh

//公钥名称：id_rsa.pub (添加到git仓库)

//密钥名称：id_rsa（保留到本机上）

gir add . //表示把文件内所有内容添加到暂存区

.gitignore//忽略清单文件夹

readme.md//仓库说明


```

## 拉取别人分支

```js

git pull origin 'dev' //有时候改到公共部分，我们的分支需要先拉取别人的东西

```

## 回退远程仓库的提交

```js

git reset --hard <version>版本号 //这种方法回退不会有历史记录，慎用

git reset --hard HEAD~2    //回退前2次提交

git push origin dev-lmq -f  //（dev-lmq）是你的分支名，记得 -f 慎用

```

## 改代码忘记切换分支咋办

```js

git stash  //保存代码到本地

git checkout dev  //切换到dev分支

git stash apply  //将刚才改动的代码拿出来

git stash pop  //命令也是一样的

// 区别 pop 会删除栈里面数据 apply 会保留数据

```

```js

git stash list  //这个可以查看本地栈中所有的保存代码

git stash apply stash@{1}  //克隆某次本地保存的改动

//举例：git stash apply 0或者git stash pop 0

git stash drop   //删除本地保存

git stash clear  //清空所有本地保存

```

<!-- more -->

