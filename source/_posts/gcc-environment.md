---
title: 在 Sublime Text 中配置GCC作为C/C++编译器
tags: C/C++
categories: Tutorial
toc: true
mathjax: true
abbrlink: 385033012
date: 2020-05-07 00:00:00
updated: 2020-05-07 00:00:00
---

  刚入门的C语言的时候，是在高中机房的电脑上用的Dev C++，不得不说Dev C++的确是神器，但是后来了解到编译器与编辑器的区别，本着一个软件只做一件事情这个想法，我开始想要把编辑器和编译器分开。这样编辑器可以用来写不只一种语言的程序，就只是纯粹的编辑文本而已（源代码也是一种文本）。

  仅就开发多种语言而言，Visual Studio也能做到，虽然宇宙第一IDE的Visual Studio的确非常强大，但它也太过庞大，而其中很多功能是我暂时用不上的，也并不符合我的初衷——编辑器和编译器分开，各司其职。于是我决定自己配置一个轻量级的开发环境，可以用个纯粹一点的编辑器，把编辑和编译分开，<!--more-->于是我把目光投向了Sublime Text，简洁而强大，可扩展性很强，有丰富的插件。尤其是对于经常切换操作系统的人来说，它有Linux和Mac的版本，作为跨平台的编辑器非常友好。对于常驻Linux系统的我来说，他有Linux版本这一点也很加分。

  当然了，既然选择将编辑器和编译器分开，各司其职，那就要自己动手配置编译环境，具体到这里也就是编译器了。

# 环境准备

  MinGW，就是Minimalist GNU for Windows，而MinGW-w64就是其用于产生64位Windows可执行文件的版本，原本是MinGW项目的分支，如今已经独立发展，且更新比较及时。笔者使用的是64位的电脑，现在64位应该比较普及了，所以以下编译器安装以64位为例，如果您使用的是32位的电脑，那么只需将下文中的编译器替换为您电脑适合的版本即可，其他步骤照旧。

## 安装

  可以从MinGW-w64的官网下载安装器之后在线安装，或者直接去SourceForge下载离线安装的软件包，可以[点击这里](https://sourceforge.net/projects/mingw-w64/files/)。如下图所示，MinGW-W64-install.exe是安装器，再下面的就是压缩包，在线和离线两种安装方法皆可，我个人偏好于离线安装，因为在线安装会留下一个不太用得上的Installer在电脑上。不过手动解压的话就需要安装完之后自己配置环境变量。
![MinGW-W64 Downloads](/images/gcc-downloads.jpg)

### 不同版本的区别

  压缩包的几个下载链接的名称的区别也体现了这几个版本的区别。x86_64与i686的区别是CPU架构的分别，i386适用于Intel和AMD所有32位的CPU，x86_64 适用于采用x86架构的64位CPU。posix与win32的区分则是线程模型的区分，可以简单地理解为posix启用了C11/C++11的多线程功能，而win32则没有C++11的多线程功能。
  名称的最后一项使关于异常处理的，GCC关于异常的处理的解释如下：

> GCC supports two methods for exception handling (EH):
> DWARF-2 (DW2) EH, which requires the use of DWARF-2 (or DWARF-3) debugging information. DW-2 EH can cause executables to be
> slightly bloated because large call stack unwinding tables have to be
> included in th executables.
> A method based on setjmp/longjmp (SJLJ). SJLJ-based EH is much slower than DW2 EH (penalising even normal execution when no
> exceptions are thrown), but can work across code that has not been
> compiled with GCC or that does not have call-stack unwinding
> information.
> …
> Structured Exception Handling (SEH)
> Windows uses its own exception handling mechanism known as Structured Exception Handling (SEH).
> …

  DWARF-2需要使用调试信息，需要有大的调用堆栈展开表 包含在可执行文件中，编译得到的可执行文件稍大。SJLJ则可以跨越尚未执行的代码，使用GCC编译或没有调用堆栈展开信息。总而言之，如果您有比较高的开发需求，您可以根据需求自己挑选编译器，如果您只是想用它来写一些简单的C/C++程序，而且不太了解上文所说的异常处理的话，在此建议您根据您的CPU和操作系统的位数下载——SJLJ同时支持64位和32位但相对古老，DWARF和SEH比较新但是只分别支持32位和64位。

## 环境变量

  如果采用的是下载压缩包手动安装的方法，则需要自己手动配置环境变量，环境变量作用其实是记录MinGW的位置，如此一来，调用的时候MinGW的位置才能被找到。
  进入高级系统设置，选择环境变量，在系统变量的Path里添加bin文件夹的路径，然后新建一个变量名为LIBRARY_PATH，值为mingw64目录里lib文件夹的路径，新建一个变量名为C_INCLUDE_PATH，值为mingw64目录下include文件夹的路径。
![在这里插入图片描述](/images/systemenv0.jpg)
![在这里插入图片描述](/images/systemenv1.jpg)
![在这里插入图片描述](/images/systemenv2.jpg)
  配置完成后在PoweShell或者cmd中输入gcc -v，如果显示版本信息则表明至少Path环境变量配置完成。

# Sublime Text中的配置

  Sublime Text中的编译系统的默认配置中有C Single File和C++ Single File两项，但是它们只能在Sublime Text窗口底部的控制台显示运行结果，而这个控制台无法在程序运行的过程中从键盘读取数据或字符，也无法调试，所以如果想得到更好的体验，我们可以自定义Build System。选择Tools>Build System>New Build System，把以下代码粘贴进去覆盖原有代码。（分别是C和C++的，分开新建，分别保存，此代码只适用于Windows系统）

```json
{
    "shell_cmd": "gcc -W -Wall -std=c11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c",
    "encoding":"cp936",

    "variants":
    [
        {
            "name": "Build&Run",
            "shell_cmd": "gcc -W -Wall -std=c11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\" && \"${file_path}/${file_base_name}\""
        },
        {
            "name": "Build&Run In Command",
            "shell_cmd": "gcc -W -Wall -std=c11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\" && start cmd /c \"\"${file_path}/${file_base_name}\" && pause\""
        },
        {
            "name": "Build&Run With Debug",
            "shell_cmd": "gcc -g -std=c11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\" && start cmd /q cd \"${file_path}\""
        },
        {
            "name": "Run Without Build",
            "shell_cmd": "start cmd /c \"\"${file_path}/${file_base_name}\" && pause\""
        }
    ]
}
```



```json
{
	"shell_cmd": "g++ -W -Wall -std=c++11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\"",
	"file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
	"working_dir": "${file_path}",
	"selector": "source.c++",
	"encoding":"cp936",

	"variants":
	[
		{
			"name": "Build&Run",
			"shell_cmd": "g++ -W -Wall -std=c++11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\" && \"${file_path}/${file_base_name}\""
		},
		{
			"name": "Build&Run In Command",
			"shell_cmd": "g++ -W -Wall -std=c++11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\" && start cmd /c \"\"${file_path}/${file_base_name}\" && pause\""
		},
		{
			"name": "Build&Run With Debug",
			"shell_cmd": "g++ -g -std=c++11 \"${file}\" -fexec-charset=gbk -o \"${file_path}/${file_base_name}\" && start cmd /q cd \"${file_path}\""
		},
		{
			"name": "Run Without Build",
			"shell_cmd": "start cmd /c \"\"${file_path}/${file_base_name}\" && pause\""
		}
	]
}
```



  大致地解释一下这两个文件，因为C和C++都是用GCC来编译的，所以这两个文件的区别基本就是`gcc`和`g++`这两个命令的差别。这两个文件都是用JSON语言写的，那它们是用来做什么的呢？
  其实，这两个文件的作用就是把要在命令行里使用的指令打包好，待编译时，Sublime Text会把这些指令传递给命令行。读者们可以试一下把里面`"shell_cmd"`后面的引号中的命令复制出来，把`${file}`等变量更改为具体的文件路径或名称，在命令行里直接运行，也可以达成编译或运行的效果。
  这里面的`cmd`、`/c`、`/k`、`pause`等是命令提示符的参数，其余的大部分是GCC的参数。其中`/c`是运行后即关闭，`/k`是运行后不关闭，所以后者可用于调试；`-std=c++11`是启用C++11标准，你可以将其修改为您所需要且您的编译器支持的C/C++标准，`-fexec-charset=gbk`是设置输出的二进制文件的字符编码为GBK，如果你的程序输出结果中含有中文字符，请务必保留这一参数。如果您习惯于在源文件的结束部分加`system("pause")`或者是`getchar()`等语句来保持窗口，您也可以删去`pause`参数。
  还有一个小细节就是，这里的文件路径的变量前后都加上了`\"`，其实就是引号，但是为了避免与JSON中有句法意义的引号冲突，使用了转义的斜杠，而之所以加引号则是因为在命令行中有空格的文件路径和文件名需要加上引号，否则有可能被当作是空格分开的两个参数或两条命令。



# 使用

  写完代码后按Shift+Ctrl+B会弹出如下图的菜单，然后自行选择需要的选项即可。
![这里会根据扩展名判断文件类型，最上面的两个是自带的Build System](/images/sublimemenu.jpg)
