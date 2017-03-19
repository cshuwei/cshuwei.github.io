---
title: RTAI编译安装
date: 2017-02-04 18:57:37
tags: ["linux","RT"]
categories: 技术
---
RTAI, 即(Real Time Application Interface)，是基于Linux Kernel而使Linux变成一个真正的实时操作系统。


<!--more-->

分别下载rtai源代码、Linux-4.1.8内核，解压，编译。我的操作系统环境是Ubuntu-16.04，现在RTAI最新版本便是rtai-5.0-test2.tar.bz2，其中的x86/patches最新只能支持4.1.8-linux内核，故将其下载。

>\#apt-get install build-essential kernel-package libncurses5-dev
//这些是编译内核所需 

>\# cd /usr/src

>/usr/src\# wget http://www.rtai.org/userfiles/downloads/RTAI/rtai-5.0-test2.tar.bz2 

>/usr/src\# wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.1.8.tar.gz

>/usr/src\# tar xvjf rtai-5.0-test2.tar.bz2

>/usr/src\# tar xvf linux-4.1.8.tar.gz

>/usr/src\# ln -s linux-4.1.8 linux && cd linux

>/usr/src/linux\# patch -p1 <../rtai-5.0-test2/base/arch/x86/patches/hal-linux-4.1.18-x86-3.patch   //打上补丁

>/usr/src/linux\# make menuconfig//之后会出现

>![configuration](http://i.imgur.com/zQ1XHCo.png)

这里根据自己所需完成配置，如果没有特殊要求可以直接上网找找默认配置。

>/usr/src/linux\# make clean && make -j4 //-j4即4个线程进行编译，这样速度快一些

>/usr/src/linux\# make modules_install && make install

>/usr/src/linux\# mkinitramfs -o ... //生成initrd.img

>/usr/src/linux\# update-grub //更新grub,确定vmlinux、initrd.img、system.map是否都在(后面都跟4.1.8版本号)

>/usr/src/linux\# reboot //重启，内核算是编译成功了。注意重启后需要进入新编译的内核，如果不能进入，可以手动F12进入grub选择版本

>/usr/src/linux\# cd ../rtai-5.0.test2 //开始编译RTAI

>/usr/src/linux\# make menuconfig

>/usr/src/linux\# make && make install

>/usr/src/linux\# cd /usr/realtime/testsuite/user/latency

>/usr/realtime/testsuite/user/latency\# ./run

如果成功运行，那就证明安装初步成功了。之后若遇到问题，可以去官网文档查找，当然也可以借助搜索引擎解决问题。

我当初安装的时候，遇到过很多的问题，如

`include/linux/compiler-gcc.h:106:30: fatal error: linux/compiler-gcc5.h: No such file or directoory`

可以通过找到compiler.h改名为compiler-gcc5.h。又如mpi_inline.h文件多重定义等等问题，要么是gcc版本太高了（即当时操作系统版本太先进）,要么是linux内核选取不够稳定，之后我采用rtai-5.0版后，一切问题迎刃而解。

而在5.0版中会经常出现类似
>\#define XXXX do｛some_statement;｝while（0）  

格式的宏来巧妙实现各种编程技巧。通过查阅得gcc也能用Statement-Expressions，即

>\#define XXX ({ some_statement;}); 

来代替，在出错的地方替换后成功编译。


####如果编译过程中出现其他问题，不必惊慌，通过搜索引擎看看有没有人遇到同样的问题，百度不行，就换google（不能用就用bing，不过还是学会“用”比较好）。linux编译问题还是可以在各个论坛，linux内核邮件列表等找到答案。但是rtai可用资源就比较少，真没办法就去看文档，还不行就只能试试联系开发人员了。。。

##更多资源
[https://www.rtai.org/userfiles/documentation/magma/html/api/](https://www.rtai.org/userfiles/documentation/magma/html/api/ "RTAI API")


