##requests网络库使用
虽然Python的标准库中urllib2模块已经包含了平常我们使用的大多数功能，但是它的API使用起来让人实在感觉不好。它已经不适合现在的时代，不适合现代的互联网了。而Requests的诞生让我们有了更好的选择。正像它的名称所说的，HTTP for Humans,给人类使用的HTTP库！在Python的世界中，一切都应该简单。Requests使用的是urllib3，拥有了它的所有特性，Requests支持  HTTP连接保持和连接池，支持使用   cookie保持会话，支持文件上传，支持自动确定响应内容的编码，支持国际化的  URL和  POST数据自动编码。现代、国际化、人性化。###1、发送无参数的get请求
	import  requests
	r = requests.get('http://httpbin.org/get')
	print r.text
	以上代码执行结果：
	
	{
	  "args": {}, 
	  "headers": {
	    "Accept": "*/*", 
	    "Accept-Encoding": "gzip, deflate", 
	    "Host": "httpbin.org", 
	    "User-Agent": "python-requests/2.11.1"
	  }, 
	  "origin": "124.202.220.42", 
	  "url": "http://httpbin.org/get"
	}
	
###2、带参数的get请求，将key与value放入一个字典中，通过params参数来传递
	import requests
	myparams={'q':'linux'}
	r=requests.get('https://www.haosou.com/s',params=myparams)
	print r.content

###3、发送post请求，通过data参数来传递
	import requests
	mydata={'wd':'Linux','name':'xwp'}
	r = requests.post("http://httpbin.org/post",data=mydata)
	print r.text
	
	以上代码执行结果：
	{
	  "args": {}, 
	  "data": "", 
	  "files": {}, 
	  "form": {
	    "name": "xwp", 
	    "wd": "Linux"
	  }, 
	  "headers": {
	    "Accept": "*/*", 
	    "Accept-Encoding": "gzip, deflate", 
	    "Content-Length": "17", 
	    "Content-Type": "application/x-www-form-urlencoded", 
	    "Host": "httpbin.org", 
	    "User-Agent": "python-requests/2.11.1"
	  }, 
	  "json": null, 
	  "origin": "124.202.220.42", 
	  "url": "http://httpbin.org/post"
	}
	
###4、发送post请求，通过json参数来传递
	import json
	import requests
	mydata={'wd':'Linux','name':'xwp'}
	r = requests.post("http://httpbin.org/post",data=json.dumps(mydata))
	print r.text
	以上代码执行结果：
	{
	  "args": {}, 
	  "data": "{\"wd\": \"Linux\", \"name\": \"xwp\"}", 
	  "files": {}, 
	  "form": {}, 
	  "headers": {
	    "Accept": "*/*", 
	    "Accept-Encoding": "gzip, deflate", 
	    "Content-Length": "30", 
	    "Host": "httpbin.org", 
	    "User-Agent": "python-requests/2.11.1"
	  }, 
	  "json": {
	    "name": "xwp", 
	    "wd": "Linux"
	  }, 
	  "origin": "124.202.220.42", 
	  "url": "http://httpbin.org/post"
	}


###5、上传文件
上传一张照片，这是就要使用files参数

	import requests
	files = {'file':open('8.jpg','rb')}
	r = requests.post("http://httpbin.org/post",files=files)
	print r.text
	
###6、实战案例
人人网最近访客

	#! /usr/bin/env python
	# coding:utf-8
	
	import requests
	import re
	url = r'http://www.renren.com/ajaxLogin/login'
	
	user  =  {'email':'username','password':'passwd'}
	s  =  requests.Session()
	r  =  s.post(url,data   =  user)
	
	html  =  r.text
	visit  =  []
	first  =  re.compile(r'</span><span    class="time­tip   first­tip"><span   class="tip­content">(.*?)</span>')
	second  =  re.compile(r'</span><span    class="time­tip"><span   class="tip­content">(.*?)</span>')
	third  =  re.compile(r'</span><span    class="time­tip   last­second­tip"><span   class="tip­content">(.*?)</span>')
	last  =  re.compile(r'</span><span    class="time­tip   last­tip"><span   class="tip­content">(.*?)</span>')
	visit.extend(first .findall(html))
	visit.extend(second .findall(html))
	visit.extend(third .findall(html))
	visit.extend(last.findall(html))
	for  i  in   visit:
	    print  i
	    
	print  '以下是更多的最近来访 '
	vm  =  s.get('http://www.renren.com/myfoot/whoSeenMe')
	fm  =  re.compile(r'"name":"(.*?)"')
	visitmore  =   fm.findall(vm.text)
	for  i  in   visitmore:
	    print  i
		
	
	




	