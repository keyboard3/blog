---
title: Linux Nohup 命令
top: false
cover: false
toc: true
mathjax: false
date: 2022-03-31 21:27
password:
summary:
tags: [Linux, terminal, 翻译]
categories:
---

[原文](https://linuxize.com/post/linux-nohup-command/)

nohup 命令执行另一个指定为其参数的程序并忽略所有 SIGHUP（挂起）信号。 SIGHUP 是在进程的控制终端关闭时发送给进程的信号。

通常，当您通过 SSH 运行程序时，如果您的连接断开或您注销，会话将终止，并且从终端执行的所有进程都将停止。这就是 nohup 命令派上用场的地方。它忽略所有挂断信号，进程将继续运行。

## 如何使用 nohup 命令

nohup 命令的语法如下：
`nohup COMMAND [ARGS]`
该命令不接受除标准 --help 和 --version 之外的任何其他选项。

让我们看一下下面的例子：
`nohup mycommand`
Output
`nohup: ignoring input and appending output to 'nohup.out'`
nohup 在前台运行 mycommand 命令并将命令输出重定向到 nohup.out 文件。该文件是在[当前工作目录](https://linuxize.com/post/current-working-directory/)中创建的。如果运行该命令的用户对工作目录没有写权限，则在用户的主目录中创建该文件

如果您注销或关闭终端，该过程不会终止。

### 在后台运行命令

在前台使用 nohup 不是很有用，因为在命令完成之前您将无法与 shell 交互。

要在[后台运行命令](https://linuxize.com/post/how-to-run-linux-commands-in-background/)，请在命令末尾附加 & 符号：
`nohup mycommand &`
输出包括 shell job ID（用括号括起来）和进程 ID：
Output
`[1] 25177`
您可以使用 job ID 使用 fg 命令将命令置于前台。

如果由于某种原因您想终止进程，请使用 kill 命令后跟进程 ID：
`kill -9 25132`

### 将输出重定向到文件

默认情况下，nohup 将命令输出重定向到 nohup.out 文件。如果要将输出重定向到不同的文件，请使用标准 shell 重定向。

例如，要将[标准输出和标准错误重定向](https://linuxize.com/post/bash-redirect-stderr-stdout/)到 mycommand.out，您可以使用：
`nohup mycommand > mycommand.out 2>&1 &`
要将标准输出和标准错误重定向到不同的文件：
`nohup mycommand > mycommand.out 2> mycommand.err &`

## 备择方案

您可以使用几个替代程序来避免在您关闭终端或断开连接时终止命令。

### Screen

[Screen](https://linuxize.com/post/how-to-use-linux-screen/) 或 GNU Screen 是一个终端多路复用器程序，它允许您启动屏幕会话并在该会话中打开任意数量的窗口（虚拟终端）。即使您断开连接，在 Screen 中运行的进程也会在其窗口不可见时继续运行。

### Tmux

[Tmux](https://linuxize.com/post/getting-started-with-tmux/) 是 GNU 屏幕的现代替代品。使用 Tmux，您还可以创建会话并在该会话中打开多个窗口。 Tmux 会话是持久的，这意味着即使您关闭终端，在 Tmux 中运行的程序也会继续运行。

### Disown

disown 是一个 shell 内置函数，可以从 shell 的 job 控制中删除一个 shell job。与 nohup 不同，您也可以在正在运行的进程上使用 disown。

## 总结

nohup 允许您在注销或退出终端时防止命令被终止。
