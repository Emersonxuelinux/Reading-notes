##socket原生网络库使用server1:原始socket AIP
	
服务器
	#coding:utf­8
	from  socket   import  *
	myhost  =  ''
	myport  =  8080
	sockobj  =   socket(AF_INET,  SOCK_STREAM)
	sockobj.bind((myhost,   myport))
	sockobj.listen(128)
	while  True:
	    connection,address  =  sockobj.accept()
	    print  "connect by",  address
	    while True:
	        data = connection.recv(1024)
	        if not data:
	            break
	        connection.send('echo' + data)
	    connection.close()
	    
	 在终端输入：
	 	nc 127.0.0.1 8080
	 	hello itcast
	server2

服务器
	#coding:utf­8
	
	from SocketServer import TCPServer,BaseRequestHandler
	import traceback
	
	class MyBaseRequestHandlerr(BaseRequestHandler):
	    """
	    #从BaseRequestHandler继承,并重写handler方法
	    """
	
	    def handle(self):
	        #循环监听(读取)来自客户端的数据
	        while True:
	            #当客户端主动断开连接时,self.recv(1024)会抛出异常
	            try:
	                #一次读取1024字节,并去除两端的空白字符(包括空格,TAB,\r,\n)
	                data = self.request.recv(1024).strip()
	
	                #self.client_address是客户端的连接(host,port)的元组
	                print "receive from (%r):%r" % (self.client_address,data)
	
	                #转换成打大写后写回(发生到)客户端
	                self.request.sendall(data.upper()+'\n')
	            except:
	                traceback.print_exc()
	                break
	
	if __name__ == "__main__":
	    #telnet 127.0.0.1 9999
	    host = ""       #主机名,可以使ip,像localhost的主机名,或""
	    port = 8080     #端口
	    addr = (host,port)
	
	    #购置TCPServer对象,
	    server = TCPServer(addr,MyBaseRequestHandlerr)
	
	    #启动服务监听
	    server.serve_forever()
	   
	启动服务后，在终端
	nc 127.0.0.1 8080
	itcast
	ITCAST
server3:多线程TCPserver

	#coding:utf­8
	
	from SocketServer import ThreadingTCPServer,StreamRequestHandler
	import traceback
	
	class MyStreamRequestHandlerr(StreamRequestHandler):
	    def handle(self):
	        while True:
	            try:
	                data = self.rfile.readline().strip()
	                print "receive from (%r):%r" % (self.client_address,data)
	                self.wfile.write(data.upper())
	            except:
	                traceback.print_exc()
	                break
	
	if __name__ == "__main__":
	    host = ""       #主机名,可以是ip,像localhost的主机名,或""
	    port = 8080     #端口
	    addr = (host,port)
	
	    #ThreadingTCPServer从ThreadingMixIn和TCPServer继承
	    #class ThreadingTCPServer(ThreadingMixIn,TCPServer):pass
	    server = ThreadingTCPServer(addr,MyStreamRequestHandlerr)
	    server.serve_forever()
	