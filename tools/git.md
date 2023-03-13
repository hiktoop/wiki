# Git远程

- git clone

- git fetch

- git pull

- git push

![git](http://ruanyifeng.com/blogimg/asset/2014/bg2014061202.jpg)

## git clone支持https/ssh/git等

- 克隆时远程主机默认命名origin，可使用`-o`指定主机名

> git协议下载速度最快、ssh用于用户认证

## git remote

```shell
$ git remote # 列出所有远程主机
$ git remote -v # 带有主机网址
$ git remote show <host-name> # 显示主机详细信息
$ git remote add <host-name> <url> # 添加远程主机
$ git remote rm <host-name> # 删除远程主机
$ git remote rename <host-name> <new-name> # 重命名
```

## git fetch

```shell
$ git fetch <host-name> # 将主机的更新取回本地
$ git fetch <host-name> <branch-name> # 仅取回指定分支
$ git branch -r # 远程分支
$ git branch -a # 所有分支
$ git checkout -b newBranch oriigin/master # 在远程分支基础上建立新的分支
$ git merge origin/master # 在当前分支上合并origin/master
```

## git pull

```shell
$ git pull <host-name> <host-branch>:<local-branch> # 取回远程分支合并到本地
```

## git push

```shell
$ git push <host-name> <local-branch>:<host-branch>
$ git push <host-name> <local-branch> # 到同名远程分支
$ git push <host-namee> :<host-branch> # 删除远程分支
```

> 还有一些进阶内容待读

![](https://images2015.cnblogs.com/blog/987101/201703/987101-20170307170555016-1139738856.png)

## 参考

- [Git远程操作详解 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2014/06/git_remote.html)

- [将本地文件夹push到github仓库 - 风茗 - 博客园](https://www.cnblogs.com/njqa/p/6515713.html)
