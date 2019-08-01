---
layout:     post
title:      RHEL and CentOS v6 and v7用户运行模式切换以及用户自动登陆配置
subtitle:   RHEL and CentOS配置须知
date:       2019-08-01
author:     Hansen Wang
header-img: img/post-bg-linux.jpg
catalog: true
tags:
    - Linux
    - Linux配置
    - RHEL
    - CentOS
---

[TOC]

## 前言

RHEL/CentOS有多种系统运行级别，比较常用的多用户模式(3-multi-user mode)和图形界面模式(5-graphical mode),以下为所有的运行模式。

v6-运行级别|v7-对应的目标|定义
:-:|-|-
0|poweroff.target|关机
1|rescue.target|单用户救援模式
2|multi-user.target|文字界面多用户模式(v6未启用NFS)
3|multi-user.target|文字界面多用户文字模式
4|multi-user.target|文字界面多用户文字模式(v6未用到)
5|graphical.target|图形界面多用户模式
6|reboot.target|重启

本文主要讲解v6/v7单次和永久运行模式切换，以及对应模式的自动登陆。

## v6运行模式切换以及用户自动登陆

以下适用于RHEL/CentOS v6版本。

### 1. 单次用户模式切换

`init 3`  切换到文字界面多用户模式
`init 5`  切换到图形界面多用户模式

### 2. 永久用户模式切换

使用指令`vim /etc/inittab`编辑inittab文件，最后一行`id:5:initdefault`中`id`后面的数字即为开机运行模式，`3`表示文字界面多用户模式，`5`表示图形界面多用户模式，如下图
[![模式切换](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC0zZjJhNTgwMmEyMGU5ODRmLnBuZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC0zZjJhNTgwMmEyMGU5ODRmLnBuZw)

### 3. 文字界面多用户模式用户自动登陆设置

使用命令`vim /etc/init/tty.conf`编辑tty.conf文件，在`exec /sbin/mingetty $TTY`后面添加参数`--autologin root`即表示自动登陆root用户

[![文字界面多用户自动登陆](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC0yMTgzODZmZjcyOWNlM2Y3LnBuZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC0yMTgzODZmZjcyOWNlM2Y3LnBuZw)

此处其实是给`mingetty`命令传递了一个参数进去。如下图是`mingetty`支持的一些参数，更多参数请查看`man page`

[![mingetty参数](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC0yZmVkMmUzMmRiZjAyYzNjLnBuZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC0yZmVkMmUzMmRiZjAyYzNjLnBuZw)


### 4. 图形界面多用户模式用户自动登陆设置

使用命令`vim /etc/gdm/custom.conf`编辑`custom.conf`配置文件，在`[daemon]`后追加如下两行内容:

> AutomaticLoginEnable=True
> AutomaticLogin=root

即表示开机后进入图形界面自动登陆root用户

[![图形界面用户自动登陆](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC1jOTVmN2JlMjA2MmU4ZDc3LnBuZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC1jOTVmN2JlMjA2MmU4ZDc3LnBuZw)

## v7运行模式切换以及用户自动登陆

以下适用于RHEL/CentOS v7版本。

### 1. 单次用户模式切换

`init 3`  切换到文字界面多用户模式
`init 5`  切换到图形界面多用户模式

### 2. 永久用户模式切换

`systemctl set-default multi-user.target` 切换到文字界面多用户模式
`systemctl set-default graphical.target` 切换到图形界面多用户模式

[![用户模式永久切换](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC1lODlkZjY1ODkwOWViYmMxLnBuZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC1lODlkZjY1ODkwOWViYmMxLnBuZw)

由上图可以看出，其实在切换过程中，命令相当于执行了一次链接操作，将预设的启动目标链接到`/etc/systemd/system/default.target`。也就是说其实我们是可以通过手动链接启动模式到这个文件来达到相同的效果。

```shell
ln -sf /lib/systemd/system/graphical.target /etc/systemd/system/default.target
```

### 3. 文字界面多用户模式用户自动登陆设置

使用命令`vim /etc/systemd/system/getty.target.wants/getty@tty1.service`编辑`getty@tty1.service`文件，在`Service` group参数`ExecStart=-/sbin/agetty --noclear %I $TERM`后添加`--autologin root`参数来实现自动登陆。

[![文字界面用户自动登陆](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC02NjIyODE2Y2E4ZGFlMzkyLnBuZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC02NjIyODE2Y2E4ZGFlMzkyLnBuZw)

这里其实和v6 中给`mingetty`命令传递了一个参数类似，v7中实际是给`agetty`命令传递了一个`--autologin root`的参数，`agetty`用于初始化用户终端，支持丰富的参数。如下

> Usage:
> agetty[options] \<line\> [\<baud_rate\>,...] [\<termtype\>]
>  agetty [options] \<baud_rate\>,... \<line\> [\<termtype\>]
>
>Open a terminal and set its mode.
>
>Options:
>-8, --8bits                assume 8-bit tty
> -a, --autologin \<user\>     login the specified user automatically
> -c, --noreset              do not reset control mode
> -E, --remote               use -r \<hostname\> for login(1)
> -f, --issue-file \<file\>    display issue file
> -h, --flow-control         enable hardware flow control
> -H, --host \<hostname\>      specify login host
> -i, --noissue              do not display issue file
> -I, --init-string \<string\> set init string
> -J  --noclear              do not clear the screen before prompt
> -l, --login-program \<file\> specify login program
> -L, --local-line[=\<mode\>]  control the local line flag
> -m, --extract-baud         extract baud rate during connect
> -n, --skip-login           do not prompt for login
> -N  --nonewline            do not print a newline before issue
> -o, --login-options \<opts\> options that are passed to login
> -p, --login-pause          wait for any key before the login
> -r, --chroot \<dir\>         change root to the directory
> -R, --hangup               do virtually hangup on the tty
> -s, --keep-baud            try to keep baud rate after break
> -t, --timeout \<number\>     login process timeout
> -U, --detect-case          detect uppercase terminal
> -w, --wait-cr              wait carriage-return
>     --nohints              do not print hints
>     --nohostname           no hostname at all will be shown
>     --long-hostname        show full qualified hostname
>     --erase-chars \<string\> additional backspace chars
>     --kill-chars \<string\>  additional kill chars
>     --chdir \<directory\>    chdir before the login
>     --delay \<number\>       sleep seconds before prompt
>     --nice \<number\>        run login with this priority
>     --reload               reload prompts on running agetty instances
>     --help                 display this help and exit
>     --version              output version information and exit

### 4. 图形界面多用户模式用户自动登陆设置

使用命令`vim /etc/gdm/custom.conf`编辑`custom.conf`配置文件，在`[daemon]`后追加如下两行内容:

> AutomaticLoginEnable=True
> AutomaticLogin=root

这里配置方法是和v6一样的。

## 总结

文字界面多用户模式和图形界面多用户模式自动登陆需执行不同的配置方法，使用于`gnome`的X11图形管理。按照上面的方法，可以很方便的切换用户模式和配置自动登陆。😀

**推荐关注博主公众号，获取最新的文章😀**

[![服务器测试与运维](https://img-blog.csdnimg.cn/20190801134803863.jpg)](https://img-blog.csdnimg.cn/20190801134803863.jpg)



> 📌转载请注明来源，版权归作者**[@hualong1009](https://hualong1009.github.io)**所有, 谢谢



