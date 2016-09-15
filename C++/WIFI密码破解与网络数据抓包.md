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