---
title: Centos下 PyDev + Eclipse安装方法
date: 2017-09-19 16:32:19
tags: ["linux","Python","Eclipse"]
categories: 技术
---
通过Eclipse安装PyDev来进行Python代码的编写。


<!--more-->



Centos下 PyDev + Eclipse安装方法


一、Eclipse


1.下载和安装Eclipse

Eclipse下载地址：http://www.eclipse.org/downloads/.   
在下载过程中可能需要连接google域名网址，所以最好先准备好翻墙工具。

`$sudo tar -xvf eclipse-inst-linux64.tar.gz`

`$cd eclipse-inst-linux64`

`$./eclipse-inst``

之后会出现一个安装图形界面，我选择安装JAVA开发环境。

![](https://i.imgur.com/QmrbTrJ.png)

4.添加Eclipse桌面快捷方式



二、安装PyDev

网上介绍了两种安装方法，第一种是利用Eclipse更新来安装的。

Help > Install New Software->点add 输入http://pydev.org/updates ，取个名字 PyDev。
![](https://i.imgur.com/d3UJEak.png)

当然你也可以手动到PyDev官网上下载按安装。
1.下载PyDev

从这里可以找到最新版：http://sourceforge.net/projects/pydev/files/pydev/

2.安装PyDev

下载后解压缩，得到两个文件夹：features和plugins，只要将它们复制到eclipse的相应目录下就可以了

sudo cp -r /SOURCE_PATH/features /TARGET_PATH/eclipse
sudo cp -r /SOURCE_PATH/plugins /TARGET_PATH/eclipse
SOURCE_PATH替换成PyDev.zip解压缩后所在的目录，TARGET_PATH替换成eclipse安装的目录

cp -r表示会复制递归处理，将指定目录下的文件与子目录一并复制过去。

3.修改权限

归档默认权限属于root，因此复制过去的PyDev，如果正常启动Eclipse的话无法加载PyDev，除非sudo 运行Eclipse。

解决方法就是修改权限，简单的做法就是将eclipse文件夹下的所有文件/目录的权限设为所有用户可读可写可执行。

sudo chmod -R 777 /PATH/eclipse
-R : 对目前目录下的所有档案与子目录进行相同的权限变更(即以递回的方式逐个变更)
-rwxrwxrwx (777) -- 所有用户都有读、写、执行权限

这个时候启动Eclipse，Windows->Preferences，就可以在左边看到PyDev了。



4.配置解释器环境

点击Windows ->Preferences.   
在对话框中，点击pyDev->Interpreter - Python. 如果已经安装好Python的话可以点击右侧的Quick Auto-Config，系统会自动帮你配置好，当然你也可以点New自己配置。

![](https://i.imgur.com/AETOj0o.png)

三、新建工程    
 File->New->Projects...   选择PyDev->PyDevProject 输入项目名称，Finish即可
![](https://i.imgur.com/kHqIYsP.png)


