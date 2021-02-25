---
title: 误删Ubuntu的EFI分区后手动grub开机修复
tags: Linux
categories: Tutorial
abbrlink: 3003136438
date: 2021-02-02 00:00:00
updated: 2021-02-02 00:00:00
---

前两天给磁盘分区的时侯不小心把EFI分区给误删了，好在我平时就常备一个Linux的启动盘，可以从启动盘进入grub然后手动引导。如果没有启动盘的话，制作也不复杂，只需要下载一个Ubuntu的iso安装镜像文件，解压到`FAT32`文件系统的U盘或者移动硬盘里即可。

随后我参考了这篇[Ubuntu开机进入GRUB手动引导](https://www.jianshu.com/p/708f12472b85)，这篇文章中<!--more-->手动grub引导的部份写得很详细，我在此补充另一个找到根目录的办法，就是在grub中输入`ls -alh`，这样的话各个分区的详细信息都列出来了，也可以从分区的详细信息来找到根目录所在的设备。还有一个可能需要改变的地方在于，`vmlinuz`和`initrd.img`不一定在根目录下有软链接，有的发行版可能就没有。所以改进后的命令如下：

```shell
grub > set root=hd0,A    #此处不需要括号，注意A的实际值（参前）
grub > set prefix=(hd0,A)/boot/grub   #boot在分区A的情况下
grub > set prefix=(hd0,B)/grub   #boot在分区B的情况下
grub > linux /boot/vmlinuz root=/dev/sdaA  #vmlinuz必须使用TAB补全（可能有后缀），注意A的值，sda或sdb需要看自己的实际情况
grub > initrd /boot/initrd.img-xxx #TAB补全，多内核情况下版本需统一
grub > boot
```

然后就成功开机了，随后我重新格式化了EFI分区，却发现前文所述的那个教程中的修复引导的方法并不能正常使用，这是因为EFI分区没有正常挂载，而且`/boot/efi`目录不能通过`mount`命令来手动挂载，而是`boot-efi`的systemd服务来挂载。但是重新划分、格式化的EFI分区的UUID变化了，`fstab`中记录的EFI分区的UUID需要手动更新一下。

接下来需要在`/dev/disk/by-uuid`目录下输入`ls -al`，根据这些UUID所指向的设备找到EFI的UUID，然后在fstab里做对应更改，再运行以下命令：

```shell
systemctl restart boot-efi
grub-install -v
```

然后重启，正常引导开机，修复成功。