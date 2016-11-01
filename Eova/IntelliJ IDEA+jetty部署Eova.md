##IntelliJ IDEA + jetty部署Eova
###1、环境
windows10、IntelliJ IDEA2016.2.4、JDK7、jetty8

###2、下载Eova1.5稳定版
下载地址：http://git.oschina.net/eova/eova

![eova下载](http://oe53dpmqz.bkt.clouddn.com/20161101001.png)

###3、新建数据库
压缩包中sql文件夹中包含了数据库导入文件eova和demo。将数据库导入到mysql中。

![数据库sql](http://oe53dpmqz.bkt.clouddn.com/20161101002.png)

###4、新建web项目
新建项目
![新建项目](http://oe53dpmqz.bkt.clouddn.com/20161101003.png)

只勾选J2EE下的web application
![web application](http://oe53dpmqz.bkt.clouddn.com/20161101004.png)

输入项目名称eovademo，然后finish
![eovademo](http://oe53dpmqz.bkt.clouddn.com/20161101005.png)

###5、idea的目录结构与eclipse有细微区别。对于习惯eclipse 目录结构的同学，可以将idea的目录结构修改为eclipse样式。方法如下

  1）在web-inf 目录下 新增classes 和 lib目录

  2）选中项目文件夹，按F4开打设置界面。选择modules下的paths。将path修改为classes目录  

  ![classes](http://oe53dpmqz.bkt.clouddn.com/20161101006.png)

  3)还是设置界面 添加lib目录为jar dir。

  ![lib1](http://oe53dpmqz.bkt.clouddn.com/20161101007.png)

  ![lib2](http://oe53dpmqz.bkt.clouddn.com/20161101008.png)

  ![lib3](http://oe53dpmqz.bkt.clouddn.com/20161101009.png)

  点击ok保存

##复制压缩包文件到项目
###6、将压缩包中lib文件夹中的文件复制到 web/web-inf/lib 下
![libcopy](http://oe53dpmqz.bkt.clouddn.com/20161101010.png)

###7、压缩包src 下的文件 复制到项目 src目录
![com](http://oe53dpmqz.bkt.clouddn.com/20161101011.png)

###8、压缩包resources下的文件 复制到项目 src目录
![resources](http://oe53dpmqz.bkt.clouddn.com/20161101012.png)

###9、压缩包webapp下的文件 复制到项目 web目录
![webapp](http://oe53dpmqz.bkt.clouddn.com/20161101013.png)

##配置工作
###10、点击ide右上角运行图标左边的 edit config……
![config1](http://oe53dpmqz.bkt.clouddn.com/20161101014.png)

![config2](http://oe53dpmqz.bkt.clouddn.com/20161101015.png)

![config3](http://oe53dpmqz.bkt.clouddn.com/20161101016.png)

###11、修改配置文件Src下 Beet.properties eovaconfig log4j  需要修改。如下

![jdbc](http://oe53dpmqz.bkt.clouddn.com/20161101018.png)

###12、根据需要修改log文件路径
![log](http://oe53dpmqz.bkt.clouddn.com/20161101019.png)

###13、最后一点：修改com.eova.eovamain.java文件
![项目路径](http://oe53dpmqz.bkt.clouddn.com/20161101022.png)

###14、大功告成点击run按钮运行项目吧  
输入127.0.0.1访问
![run](http://oe53dpmqz.bkt.clouddn.com/20161101021.png)



















