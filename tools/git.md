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


