---
title: 在Linux上搭建Node.js环境
tags:
- Linux
- JavaScript
categories: Tutorial
toc: true
abbrlink: 3049945751
date: 2021-01-15 00:00:00
updated: 2021-01-15 00:00:00
---



在安装和使用Node.js的过程中遇到过很多坑，笔者在此将自己的经验整理，给出一种比较简便且合理的安装方案，如果没兴趣看前面的介绍，只想知道怎么安装比较合适，可以直接翻到文章结尾一段。此外，在此声明，如果你已经涉及到多版本的Node.js开发，需要在不同版本的Node.js间切换，则本文不适用，建议直接使用nvm。

# 前情提要

笔者使用的是Ubuntu和CentOS，但是最初因为源里面的Node.js版本太低了，就干脆从官网下载已经编译好的分发，直接安装到`/usr/local`下。假如是用包管理器安装的话，就是安装到了`/usr`下，而这两者都有一个共同点，就是文件夹归属`root`用户。
<!--more-->

无论安装到哪个路径下，Node.js的内容都包含这几个文件夹：`bin`、`lib`、`include`、`share`，其中`bin	`目录包含的文件有node自身的二进制文件，以及指向npm包的软链接，这些包在`lib/node_modules`里，软链接使用的是相对路径，所以`bin`和`lib`必须在目录结构的同一层次，或者说并列在同一文件夹里。与此同时，`include`和share这两个文件夹则没有必然的相对位置的要求，只要放在合适的路径即可。所以，如果要使用npm进行包管理，必须要对`bin`和`lib`这两个目录有读写权限，但是无论是`/usr`还是`/usr/local`都是`root`用户的目录，所以要么使用`root`权限运行npm命令，要么就得改`bin`和`lib/node_modules`的权限。这两种方案都不值得推荐，前者是因为无法确保npm包的安装脚本中不会有什么会破坏你系统的操作，赋予它root权限很危险，而后者则是因为`bin`目录的权限如果开放给非`root`用户，等于是给Linux的多用户权限体系开了个口子，同样不安全。

所以我们需要的安装方式需要满足以下几个要求：

1. 安装目录属于当前的普通用户
2. `bin`和`lib`文件夹要包含在同一目录下
3. `include`文件夹要在系统的`include`搜索的路径中

# 最终方案

从官网下载你需要的版本的Node.js的压缩包，解压后，`bin`目录的内容放在`$HOME/.local/bin`里，`lib`的内容放在`$HOME/.local/lib`里，`share`的内容放在`$HOME/.local/share`里，`include`里的内容则放在`/usr/local/include`里，其余的内容可以不要。这样一来，Node.js完全不需要`root`，而且也不需要在你的`$PATH`里添加奇奇怪怪的路径。
