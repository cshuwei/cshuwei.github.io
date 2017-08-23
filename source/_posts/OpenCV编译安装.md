---
title: OpenCV编译安装
date: 2017-04-19 16:32:19
tags: ["linux","OpenCV","ffmpeg"]
categories: 技术
---
OpenCV是一个用于图像处理的库，其中包含了许多图像处理技术如腐蚀膨胀、开闭运算等封装函数。


<!--more-->



首先安装一些依赖库

    # apt-get install build-essential
	# apt-get install cmake cmake-gui pkg-config libgtk2.0-dev libavcodec-dev libavformat-dev libswscale-dev

下载openCV3.2.0源码,ffmpeg-3.3.1视频处理库源码以及x264编码库，解压,先编译ffmpeg。

	# tar xvjf x264-stable.tar.bz2 && cd x264-stable 
	# ./configure --enable-pic --enable-shared --disble-asm
	# make -j6 && make install
	# tar xvjf ffmpeg-3.3.1.tar.bz2 && cd ffmpeg-3.3.1
	# ./configure --enable-nonfree --enable-pic --enable-shared --enable-gpl --enable-libx264 --disable-asm
	# make -j6 && make install
之后编译openCV，进入解压后的目录，为方便操作，选择在cmake图形界面上进行编译配置
	
	# cmake-gui
  
![](http://i.imgur.com/PFXIKzk.png)
![](http://i.imgur.com/QEnm0Em.png)

设置输出文件夹为opencv-86,configure，之后填写CMAKE-BUILD-TYPE为Release，generate，然后到opencv-86进行编译。

    # cd opencv-86 && make j-6 && make install
	# /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
	# ldconfig