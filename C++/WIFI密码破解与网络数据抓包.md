##课程介绍
屌丝逆袭之蹭网利器，在女朋友面前炫耀一下自己的“黑客功底”，在Linux的世界里，其实这都不是什么难事，瘫痪路由器，发起网络攻击，抓包数据分析，加入“网络圣战”，进入Linux世界，轻松让你成为“高逼格”的IT达人

##课程知识点
1、WIFI密码破解原理剖析

2、网络数据抓包与分析

3、路由器攻击，瘫痪网络

4、随心所欲，开发定制密码字典

5、枚举字典，暴力破解

6、轻松使用”隔壁老王“的wifi

##课程详情
Wireshark是一个非常好用的抓包工具，当我们遇到一些和网络相关的问题时，可以通过这个工具进行分析，不过要说明的是，这只是一个工具，用法非常灵活的。

![wireshark](pictures/1.png)

Wireshark下载地址

	https://www.wireshark.org/download.html
	
##选取网卡
wireshark左上角，查找list那个按钮

##设置抓包过滤器(CaptureFilters)
	语法:Protocaol $ Direction $ Host(s) $ Value $ Logical Operations $ Other expression
	例子: ip src host 192.168.1.100 tcp dst port 8080
	例子：ip src host 192.168.1.100
	
###Protocol（协议）：
可能得值：ether,fddi,ip,arp,rarp,decnet,lat,sca,moprc,mopdl,tcp adn udp

如果没有特别指明是什么协议，则默认使用所有支持的协议。

###Direction（方向）：
可能的值：src,dst,src and dst,src or dst

如果没有特别指明来源或目的地，则默认使用”src or dst“作为关键字。例如，"host 10.2.2.2"与"src or dst host 10.2.2.2"是一样的。

###Host(s):
可能的值：net,port,host,portrange.

如果没有指定此值，则默认使用”host“关键字。例如，"src 10.1.1.1"与"src host 10.1.1.1"相同

###Logical Operations(逻辑运算)：
可能得值：not,and,or

否("not")具有最高的优先级。或("or")和与("and")具有相同的优先级，运算时从左至右进行。例如：

	"not tcp port 3128 and tcp port 23"与"(not tcp port 3128) and tcp port 23"相同
	"not tcp port 3128 and tcp port 23"与"not (tcp port 3128) and tcp port 23)"不同
	
如果过滤器的语法是正确的，表达式的背景呈绿色。如果呈红色，说明表达式有误。

##设置显示过滤器(DisplayFilters)
	
	语法：Protocol.String1.String2 $ Comparison operator $ Value $ Logical Operations $ Other expression
	例子：ip.src=192.168.1.100	(显示来源为192.168.1.100的包)
		tcp.port==80
	例子：http.request.method==POST(显示http请求方法是post)
		
##网络封包分析
	登录获取某网站的表单，分析其账号和密码
	
##WIFI密码破解原理剖析
	1、把我的网卡变成监听模式(捕获无线信号)
	2、扫描周围无线WIFI
	3、专门监听老王的WIFI（老王和路由器的数据传输）
	4、发起攻击，模拟WIFI路由器单插，向老王发出断开连接请求
	5、老王的电脑断开连接，自动重新建立连接，咱们捕获4次握手包
	6、4次握手包里包含有经过加密的密码，SHA1 MD5
	7、暴力破解，穷举字典破解
	8、轻松加愉快上老王家的网

##Aircrack-ng
###下载
	http://www.aircrack-ng.org/
	http://www.aircrack-ng.org/downloads.html
	http://download.aircrack-ng.org/aircrack-ng-1.2-rc4.tar.gz
	
###安装
	sudo apt-get install build-essential libssl-dev pkg-config libnl-3-dev libnl-genl-3-dev
	tar zxvf aircrack-ng-1.2-rc2.tar.gz
	cd aircrack-ng-1.2-rc2
	make
	sudo make install
	
安装步骤：
		
	ljaer@ubuntu:~$ mkdir mingmingMM	ljaer@ubuntu:~$ cd mingmingMM/	ljaer@ubuntu:~/mingmingMM$ ls	ljaer@ubuntu:~/mingmingMM$ wget http://download.aircrack-ng.org/aircrack-ng-1.2-rc4.tar.gz	ljaer@ubuntu:~/mingmingMM$ ls	aircrack-ng-1.2-rc4.tar.gz	ljaer@ubuntu:~/mingmingMM$ tar zxvf aircrack-ng-1.2-rc4.tar.gz	ljaer@ubuntu:~/mingmingMM$ ls	aircrack-ng-1.2-rc4  aircrack-ng-1.2-rc4.tar.gz	ljaer@ubuntu:~/mingmingMM$ cd aircrack-ng-1.2-rc4/	ljaer@ubuntu:~/mingmingMM/aircrack-ng-1.2-rc4$ ls	apparmor  ChangeLog   INSTALLING  LICENSE.OpenSSL  packages  scripts  VERSION	AUTHORS   common.mak  lib         Makefile         patches   src	autocfg   evalrev     LICENSE     manpages         README    test	ljaer@ubuntu:~/mingmingMM/aircrack-ng-1.2-rc4$ sudo apt-get install build-essential libssl-dev pkg-config libnl-3-dev libnl-genl-3-dev 	ljaer@ubuntu:~/mingmingMM/aircrack-ng-1.2-rc4$ make	ljaer@ubuntu:~/mingmingMM/aircrack-ng-1.2-rc4$ sudo make install	ljaer@ubuntu:~/mingmingMM/aircrack-ng-1.2-rc4$ cd ..	ljaer@ubuntu:~/mingmingMM$ ls	aircrack-ng-1.2-rc4  aircrack-ng-1.2-rc4.tar.gz	ljaer@ubuntu:~/mingmingMM$ mkdir crack	ljaer@ubuntu:~/mingmingMM$ cd crack/	ljaer@ubuntu:~/mingmingMM/crack$ ls	ljaer@ubuntu:~/mingmingMM/crack$ pwd	/home/ljaer/mingmingMM/crack
	
	
##WIFI密码破解
1、查看本机电脑无线网卡
	
	iwconfig或者ifconfig
	在这里由于使用的是虚拟机（可以在虚拟机的网络设置，设置wifi连接eth0）

2、激活网卡到monitor模式，得到监控模式下的设备名为wlan0mon
	
	sudo airmon-ng start wlan0
	
3、探查无线网络，选取破解路由器对象

	sudo airodump-ng wlan0mon
	
4、设置监控频道，抓取被选定路由器的数据包，始终运行
	
	sudo airodump-ng --ivs -w linuxcpp -c 6 wlan0mon
	捕获的包存入linuxcp-0x.ivs文件，x为序号
	
5、新开终端，发起Deauth攻击，破事客户端重新链接路由器
