---
title: RT-Preempt编译安装
date: 2017-03-19 18:58:19
tags: ["linux","RT"]
categories: 技术
---
RT-Preempt是Linux的一个实时补丁，只需将其补丁打入内核，编译内核即可得到实时Linux。


<!--more-->
分别下载Linux-4.9.11内核版本和RT-preempt patch 4.9.11版本。

>\#apt-get install build-essential kernel-package libncurses5-dev
//这些是编译内核所需 

>\# cd /usr/src

>/usr/src\# wget https://www.kernel.org/pub/linux/kernel/projects/rt/4.9/patch-4.9.11-rt9.patch.gz    

>/usr/src\# wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.1.8.tar.gz

>/usr/src\# tar xvf patch-4.9.11-rt9.patch.gz    

>/usr/src\# tar xvf linux-4.1.8.tar.gz

>/usr/src\# ln -s linux-4.1.8 linux && cd linux

>/usr/src/linux\# zcat ../patch-4.9.11-rt9.patch.gz   | patch -p1   //打上补丁

>/usr/src/linux\# make menuconfig//之后会出现

>![configuration](http://i.imgur.com/zQ1XHCo.png)

这里根据自己所需完成配置，如果没有特殊要求可以直接上网找找默认配置。

>/usr/src/linux\# make clean && make -j4 //-j4即4个线程进行编译，这样速度快一些

>/usr/src/linux\# make modules_install && make install

>/usr/src/linux\# mkinitramfs -o ... //生成initrd.img

>/usr/src/linux\# update-grub //更新grub,确定vmlinux、initrd.img、system.map是否都在(后面都跟4.1.8版本号)

>/usr/src/linux\# reboot //重启，内核算是编译成功了。注意重启后需要进入新编译的内核，如果不能进入，可以手动F12进入grub选择版本

RT-Preempt环境搭建比RTAI简单，出的问题也比较少。一般出的问题都是编译内核问题，搜索相关内容寻找答案即可。


##相关资源
[https://rt.wiki.kernel.org/index.php/RT_PREEMPT_HOWTO](https://rt.wiki.kernel.org/index.php/RT_PREEMPT_HOWTO)