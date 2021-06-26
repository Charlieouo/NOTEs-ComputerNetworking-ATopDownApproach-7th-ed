# 套接字编程作业1：Web服务器

## 作业描述

《计算机网络：自顶向下方法》中第二章末尾给出了此编程作业的简单描述：

> 在这个编程作业中，你将用Python语言开发一个简单的Web服务器，它仅能处理一个请求。具体而言，你的Web服务器将：
> 1. 当一个客户（浏览器）联系时创建一个连接套接字；
> 2. 从这个连接套接字接收HTTP请求；
> 3. 解释该请求以确定所请求的特定文件；
> 4. 从服务器的文件系统获得请求的文件；
> 5. 创建一个由请求的文件组成的HTTP响应报文，报文前面有首部行；
> 6. 经TCP连接向请求浏览器发送响应。如果浏览器请求一个在该服务器种不存在的文件，服务器应当返回一个“404 Not Found”差错报文。  
>
> 在配套网站中，我们提供了用于该服务器的框架代码，我们提供了用于该服务器的框架代码。你的任务是完善该代码，运行服务器，通过在不同主机上运行的浏览器发送请求来测试该服务器。如果运行你服务器的主机上已经有一个Web服务器在运行，你应当为该服务器使用一个不同于80端口的其他端口。



## 实现

官方给出的代码基于python 2，以下使用python 3，修改了一些细节以处理字符编码问题。

建立一个只允许一个连接的服务器，在指定端口监听客户端的请求，从客户端发送的请求中提取文件名，若该文件存在于服务器上（如下文的"hello_world.html"），则生成一个状态码200的POST报文，并返回该文件；若该文件不存在，则返回一个404 Not Found报文。

![img](https://gitee.com/Charlieouo/pic_bed/raw/master/img/425762-20151212160019653-1079393936.png)



## 代码

**WebServer.py**

``` python
'''
# coding=gbk
#import socket module
from socket import *
serverSocket = socket(AF_INET, SOCK_STREAM) 
#Prepare a server socket 
serverSocket.bind(('', 6789)) # bind()将TCP欢迎套接字绑定到指定端口
serverSocket.listen(1) # listen()欢迎套接字听取敲门。最大连接数为1

while True:
	#Establish the connection
	print('Ready to serve...')
	connectionSocket, addr = serverSocket.accept() # accept()接收到客户连接请求后，建立新的TCP连接套接字
	try:
		message = connectionSocket.recv(1024) # 获取客户发送的报文
		filename = message.split()[1]
		f = open(filename[1:])
		outputdata = f.read();
		#Send one HTTP header line into socket
        #先向连接套接字发送HTTP header
		header = ' HTTP/1.1 200 OK\nConnection: close\nContent-Type: text/html\nContent-Length: %d\n\n' % (len(outputdata))
		connectionSocket.send(header.encode())
        
		#Send the content of the requested file to the client
        #再向连接套接字发送文件内容，注意编码为字节
		#for i in range(0, len(outputdata)):
		#	connectionSocket.send(outputdata[i].encode())
		connectionSocket.send(outputdata.encode())
		connectionSocket.close()
	except IOError:
		#Send response message for file not found
		header = ' HTTP/1.1 404 Not Found'
		connectionSocket.send(header.encode())
		
		#Close client socket
		connectionSocket.close()
serverSocket.close()
'''


# coding=gbk
from socket import *
PORT=6789
serverSocket = socket(AF_INET, SOCK_STREAM) #创建欢迎套接字
serverSocket.bind(('', PORT)) # bind()将TCP欢迎套接字绑定到指定端口
serverSocket.listen(1) # listen()听取敲门。最大连接数为1

while True:
	#Establish the connection
	print('Ready to serve...')
	connectionSocket, addr = serverSocket.accept() # 接收到客户连接请求后，建立新的TCP连接套接字
	try:
		message = connectionSocket.recv(1024) # 获取客户发送的报文
		filename = message.split()[1]
		print(message)#打印客户发送的报文
		if filename=='/':
			raise IOError
		f = open(filename[1:])
		outputdata = f.read()
		#Send one HTTP header line into socket
		#先向连接套接字发送HTTP header
		header = ' HTTP/1.1 200 OK\nConnection: close\nContent-Type: text/html\nContent-Length: %d\n\n' % (len(outputdata))
		connectionSocket.send(header.encode())

		#Send the content of the requested file to the client
		#再向连接套接字发送文件内容
		#for i in range(0, len(outputdata)):
		#	connectionSocket.send(outputdata[i].encode())
		connectionSocket.send(outputdata.encode())
		#两种发送方法都可，显然上一种速度更慢
		connectionSocket.close()
	except IOError:
		#Send response message for file not found
		header = ' HTTP/1.1 404 Not Found'
		connectionSocket.send(header.encode())
		
		#Close client socket
		connectionSocket.close()
serverSocket.close()
```

**hello_world.html**

```html
<head>Hello world!</head>
```



## 运行

**服务器端：**

在一台主机上的同一目录下放入`WebServer.py`和`hello_world.html`两个文件，并运行`WebServer.py`，作为服务器。

![image-20210626143540761](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626143540761.png)

**客户端：**

在另一台主机上打开Firefox浏览器，并输入"http://XXX.XXX.XXX.XXX:6789/hello_world.html" （其中"XXX.XXX.XXX.XXX"是服务器IP地址），我这里是http://192.168.43.174:6789/hello_world.html，以获取服务器上的`hello_world.html`文件。

一切正常的话，可以看到如下页面：

![image-20210626110216751](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626110216751.png)

输入新地址"http://XXX.XXX.XXX.XXX:6789/abc.html"，以获取服务器上不存在的`abc.html`。

将出现以下页面：

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626110732208.png" alt="image-20210626110732208" style="zoom:50%;" />



##  可选练习1

目前，这个Web服务器一次只处理一个HTTP请求。**请实现一个能够同时处理多个请求的多线程服务器**。使用线程，首先创建一个主线程，在固定端口监听客户端请求。当从客户端收到TCP连接请求时，它将通过另一个端口建立TCP连接，并在另外的单独线程中为客户端请求提供服务。这样在每个请求/响应对的独立线程中将有一个独立的TCP连接。

**WebServer_Server.py**

```python
# import socket module
# coding=gbk

from socket import *
from _thread import *
import threading

print_lock = threading.Lock()


def threaded(c):
    try:
        message = c.recv(1024)
        filename = message.split()[1]
        f = open(filename[1:])
        outputdata = f.read()
        header = 'HTTP/1.1 200 OK \nConnection: close\n' + \
                 'Content0Length: {}\n'.format(len(outputdata)) + \
                 'Content-Type: text/html\n\n'
        c.send(header.encode())
        for i in range(0, len(outputdata)):
            c.send(outputdata[i].encode())
        c.close()
    except IOError:
        header = 'HTTP/1.1 404 Not Found'
        c.send(header.encode())
        c.close()


def main():
    serverSocket = socket(AF_INET, SOCK_STREAM)

    # Prepare a server socket
    # Fill in start
    serverSocket.bind(('', 6789))  # 使用 6789 端口
    serverSocket.listen(1)
    # Fill in end

    while True:
        try:
            # Establish the connection
            print('Ready to server...')
            # Fill in start
            connectionSocket, addr = serverSocket.accept()
            # FIll in end
            start_new_thread(threaded, (connectionSocket,))
        except:
            print('Exit')

            break
    # 关闭服务端
    serverSocket.close()


if __name__ == '__main__':
    main()

```

**效果如下：**

![image-20210626144106511](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626144106511.png)

![image-20210626150526365](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626150526365.png)

**html：**

```html
<meta http-equiv=Content-Type content=text/html;charset=gb2312>
<p>向优秀大学生低头</p>
<p><a href="https://imgtu.com/i/R8wlkt"><img src="https://z3.ax1x.com/2021/06/26/R8wlkt.jpg" alt="R8wlkt.jpg" border="0" /></a></p>
```

![image-20210626151212953](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626151212953.png)



##  可选练习2

不使用浏览器，**编写自己的HTTP客户端**来测试你的服务器。您的客户端将使用一个TCP连接用于连接到服务器，向服务器发送HTTP请求，并将服务器响应显示出来。您可以假定发送的HTTP请求将使用GET方法。 客户端应使用命令行参数指定服务器IP地址或主机名，服务器正在监听的端口，以及被请求对象在服务器上的路径。以下是运行客户端的输入命令格式。

> client.py server_host server_port filename

```python
import sys
from socket import *
##to run the programme in cmd:
#python Webserver_Client.py 192.168.43.174 6789 hello_world.html
serverName=sys.argv[1] #sever name,here is my ip address
serverPort=int(sys.argv[2]) #any port consistent with the server end
filename=sys.argv[3]
request_head_1='GET /'
request_head_2=' HTTP/1.1\nHost: 192.168.43.174\nConnection: keep-alive\nUpgrade-Insecure-Requests: 1\n\
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3\n\
Purpose: prefetch\n\
Accept-Encoding: gzip, deflate, br\n\
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8'#How to use 
request_head=request_head_1+filename+request_head_2

clientSocket=socket(AF_INET,SOCK_STREAM)
clientSocket.connect((serverName,serverPort)) #creat connection

clientSocket.send(request_head.encode()) #encoding the message to binary bytes
for i in range(2):
    mod=clientSocket.recv(1024)
    print(mod.decode())
clientSocket.close()
```

![image-20210626141244908](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626141244908.png)

