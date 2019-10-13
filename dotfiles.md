---
title: dotfiles
date: 2019-09-30 14:23:24
tags:
---

# 我的 `.bashrc` 配置记录

```sh
stty -ixon # Disable ctrl-s and ctrl-q.
```

`stty` 可以修改或打印 terminal line settings
`ixon` 是一个整体设置(input XON)，enable XON/XOFF flow control，加上 `-` 表示disable


```sh
shopt -s autocd #Allows you to cd into directory merely by typing the directory name.
```
shopt 是bash内置的命令，`-s` 表示 enable (set) each optname，autocd：If set, a command name that is the name of a directory is executed as if it were the argument to the cd command. This option is only used by interactive shells.

[参见](https://www.gnu.org/software/bash/manual/html_node/The-Shopt-Builtin.html)

```sh
HISTSIZE= HISTFILESIZE= # Infinite history.
```

`HISTSIZE` 是内存中保留的历史记录大小，而`HISTFILESIZE` 是文件中保留的历史记录大小



```sh
export PS1="\[$(tput bold)\]\[$(tput setaf 1)\][\[$(tput setaf 3)\]\u\[$(tput setaf 2)\]@\[$(tput setaf 4)\]\h \[$(tput setaf 5)\]\w\[$(tput setaf 1)\]]\[$(tput setaf 7)\]\\$ \[$(tput sgr0)\]"
```
我的命令提示符

# 我的 `.profile` 配置记录


