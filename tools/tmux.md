# tmux

## Tmux是什么

### 会话与进程

- 窗口与会话是连接的，窗口关闭，连接断开

- tmux使两者解绑，实现窗口关闭会话不终止。

> 就因为窗口关闭会话终止，我需要重写这篇笔记 :（

### tmux的作用

1. 单个窗口中同时访问多个会话。这对于同时运行多个命令行程序很有用。

2. 让新窗口"接入"已经存在的会话。

3. 每个会话有多个连接窗口，因此可以多人实时共享会话。

4. 支持窗口任意的垂直和水平拆分。

## 基本用法

### 会话管理

```shell
# 新建会话
$ tmux new -s <session-name>
# 分离会话
$ tmux detach
# 查看会话
$ tmux ls
# 接入会话
$ tmux attach -t 0 # 使用会话编号
$ tmux attach -t <session-name> # 使用会话名称
# 杀死会话
$ tmux kill-session -t 0 # 使用会话编号
$ tmux kill-session -t <session-name> # 使用会话名称
# 切换会话
$ tmux switch -t 0 # 使用会话编号
$ tmux switch -t <session-name> # 使用会话名称
# 重命名会话
$ tmux rename-session -t 0 <new-name>
```

> - `Ctrl+b d`：分离当前会话。
> - `Ctrl+b s`：列出所有会话。
> - `Ctrl+b $`：重命名当前会话。

### 窗格操作

> - `Ctrl+b %`：划分左右两个窗格。
> - `Ctrl+b "`：划分上下两个窗格。
> - `Ctrl+b <arrow key>`：光标切换到其他窗格。`<arrow key>`是指向要切换到的窗格的方向键，比如切换到下方窗格，就按方向键`↓`。
> - `Ctrl+b ;`：光标切换到上一个窗格。
> - `Ctrl+b o`：光标切换到下一个窗格。
> - `Ctrl+b {`：当前窗格与上一个窗格交换位置。
> - `Ctrl+b }`：当前窗格与下一个窗格交换位置。
> - `Ctrl+b Ctrl+o`：所有窗格向前移动一个位置，第一个窗格变成最后一个窗格。
> - `Ctrl+b Alt+o`：所有窗格向后移动一个位置，最后一个窗格变成第一个窗格。
> - `Ctrl+b x`：关闭当前窗格。
> - `Ctrl+b !`：将当前窗格拆分为一个独立窗口。
> - `Ctrl+b z`：当前窗格全屏显示，再使用一次会变回原来大小。
> - `Ctrl+b Ctrl+<arrow key>`：按箭头方向调整窗格大小。
> - `Ctrl+b q`：显示窗格编号。

### 窗口管理

> - `Ctrl+b c`：创建一个新窗口，状态栏会显示多个窗口的信息。
> - `Ctrl+b p`：切换到上一个窗口（按照状态栏上的顺序）。
> - `Ctrl+b n`：切换到下一个窗口。
> - `Ctrl+b <number>`：切换到指定编号的窗口，其中的`<number>`是状态栏上的窗口编号。
> - `Ctrl+b w`：从列表中选择窗口。
> - `Ctrl+b ,`：窗口重命名。
