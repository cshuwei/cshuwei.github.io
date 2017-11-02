---
title: Ping Tunnel
date: 2017-11-02 14:41:06
tags: ["linux","tcp/ip"]
categories: 技术
---
通过icmp协议里的ping requests and replies来传输tcp数据，借此实现突破网络限制，连接外网。


<!--more-->

在实验室有一台服务器（老师授权于我。。。相当于随意玩耍），然而学校的网络有着令人难以捉摸的特性：如果不用一卡通帐号密码登录，不仅不能上网，连内网学校官网、教务处等也上不去，更不必说要从笔记本通过电信网ssh连接到服务器了。因为总总原因，我并不想登录（登录需要冲钱，没钱登不了），于是先在校园网上借助室友帐号连接服务器，经过一系列折腾，发现了一条特色社会主义道路。

实验室（与学校不在同一地点）的机子都是通过一台无线路由器连接外网（至于宿舍校园网、教室校园网如何与实验室相连等一系列问题至今未知），并且用的是公网地址123.234.56.1（事实上路由器分配的ip都是公网ip，但是从外连里都被挡住了）。如果服务器不进行web帐号密码登录，80等一系列端口访问是无法发出的，但是通过ping外网地址发现可以ping通，估计网关(即无线路由器)只过滤了tcp(udp我还没尝试过)，但从内到外的ping requests 和从外到内的 ping replies 可以正常通过。

于是，我找到了PingTunnel这个软件，通过icmp的ping requests and replies来形成tunnel从而进行tcp数据传输。

其网络传输路线如下图，更多传输原理和细节可以点击文末链接同时可以下载得到源码。

![](https://i.imgur.com/IrVFfkp.png)

简单分析一下：client 和 Proxy 通过安装PingTunnel软件来进行icmp信息交换（通过如下代码启动），-da ,-dp 参数为client欲真正访问的目的地址和端口。 之后client就可以启动通过tcp进行通信的应用，将其目标地址定为127.0.0.1 ，端口与 -lp 参数 相同。 最后，应用数据就会先传到本地指定端口，之后通过PingTunnel封装数据穿过网关到达Proxy，proxy得到数据后会自发响应与目的地址发起通信，并将回复数据转发回Client。

    Client: ./ptunnel -p <proxy address> -lp <listen port> -da <destination address> -dp <destination port> [-c <network device>] [-v <verbosity>] [-f <logfile>] [-u] [-x password]
    Proxy: ./ptunnel [-c <network device>] [-v <verbosity>] [-f <logfile>] [-u] [-x password]

但是，最终实现的只是我的服务器能够连接外网（并且是免费的。。。。）。而我的目的是想通过我的笔记本从电信网络直接ssh到服务器。而这只要通过内网转发工具就可以实现外网连接内网了。我是用frp实现该功能的，如图依然是通过一个公网proxy来进行各种通信，具体信息可以看文末的给出的github地址。

![](https://i.imgur.com/quZiZBF.png)

下面给出所有操作的代码，图中装PingTunnel是可能需要安装libpcap包，然后又需要flex，bison等软件

	# wget http://202.119.24.249/cache/11/02/www.tcpdump.org/08ba796d389dad839031e2766e2ad  //通过tcpdump下载libpcap
	# tar xvf libpcap-1.8.1.tar.gz
	# cd lipcap-1.8.1
    # apt-get install flex bison
	# ./configure && make all && make install
	# tar xvf PingTunnel-0.72.tar.gz                                                        //到PingTunnel压缩包所在目录
	# cd PingTunnel
    # make all

	# ./ptunnel -p 12.12.12.12 -lp 8000 -da 12.12.12.12 -dp 19420         //client,12.12.12.12为proxy地址，同时目的地址也是proxy。
	# ./ptunnel                                                           //proxy

Ubuntu途中可能会出现error：

    ./ptunnel: error while loading shared libraries: libpcap.so.1: cannot open shared object file: No such file or directory

通过打开/etc目录下的文件ld.so.conf ，添加两行

    /usr/local/lib
    /usr/lib

接下来安装frp，将下载的压缩包中frpc、frpc.ini、frpc_full.ini放到client机子上（即我的服务器），同理将frps等传到proxy上。

    # ./frpc -c frpc.ini
	# ./frps -c frps.ini

frpc.ini和frps.ini配置信息如下：

![](https://i.imgur.com/wyGaOGT.png)
![](https://i.imgur.com/l6pXJvR.png)

最后启动在client启动sshd，监听2018端口。远程主机（即我的笔记本）访问proxy地址的56785端口

    # ssh 12.12.12.12 -p 56785

这样就大功告成了，后续还可以通过screen等程序运行frp和ptunnel，以避免关闭ssh所有连接都中断了（我是通过ssh远程配置proxy和client，当然都是通过校园网，用室友的帐号，一点都不心疼）


使用的软件以及更多详细信息

[http://www.cs.uit.no/~daniels/PingTunnel/](http://www.cs.uit.no/~daniels/PingTunnel/)

[https://github.com/fatedier/frp](https://github.com/fatedier/frp)