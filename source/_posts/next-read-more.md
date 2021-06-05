---
title: Hexo博客的next主题下使read more按扭跳转到从头阅读
tags: blog
date: 2021-02-03
categories: Miscellaneous
abbrlink: 9350604
---

Hexo博客的next主题官方文档推荐的在主页收起长文的方法是，在Markdown文档中需要截断的地方添加`<!--more-->`标记。但是默认状态下点击`read more`按扭后，跳转到全文是继续阅读而不是从头阅读。旧版本的next主题提供了选项，可以在配置文件当中简便地设置，但是新版本的就没有了，所以网上找到的更改配置文件的办法都失效了。

新的解决办法就是，直接修改主题的源代码，要修改的源文件的具体路径是`themes/next/layout/_macro/post.swig`，打开后搜索到`if theme.read_more_btn`，然后在其后几行找到`#more`的字样将其删除即可。
