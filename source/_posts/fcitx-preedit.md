---
title: 在Typora中使用fcitx时光标位置的问题
tags:
  - Linux
  - fcitx
  - Typora
categories: Tutorial
abbrlink: 627065885
date: 2021-01-27 00:00:00
---
Hexo博客的博文是用Markdown格式的，所以我平时写博文都是用Typora，输入法是`fcitx-pinyin`但是一直有个问题，就是在`YAML Front Matter`块下的第一行的第一个字符，如果要通过`fcitx-pinyin`输入法来输入，则光标的位置会在输入过程中跳到上一行的`YAML Front Matter`中，并导致预编辑（preedit）的字符直接输出。我在网上搜了很久，用了英文和中文结果都没有搜到相关内容。


虽然我没有找到出现这种问题的详细原因，但是我注意到，`fcitx-pinyin`输入法会把预输入的内容动态地显示在输入区域，但是光标却并不是跟着输入进度，而是保持在词首，我推测上述问题可能与此有关。于是，我查阅了`fcitx`的文档以及相关的问答，找到了关闭显示预输入区的办法——就是在开启`fcitx`的情况下，按下键盘的<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>P</kbd>，就可以关闭预编辑区的显示。然后，我再次尝试，发现上述问题不会再出现了。虽然仍不知道问题的原因以及此方案的原理，但是终究是解决了问题。而且，这也同时解决了`fcitx`的光标始终在词首，看着很别扭的问题。
