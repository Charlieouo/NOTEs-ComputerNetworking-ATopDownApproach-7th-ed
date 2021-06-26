# TCP套接字编程

## 描述

《自顶向下方法（原书第7版）》第2.7.2节给出了一个使用Python的TCP套接字编程实例。实现了一个简单的TCP通信程序。

![img](https://gitee.com/Charlieouo/pic_bed/raw/master/img/425762-20151212160019653-1079393936.png)

## 代码

客户端程序`TCPClient.py`创建一个TCP套接字，然后向指定服务器地址和端口发起连接，等待服务器连接后，再将用户输入的字符串通过套接字发送，其后将服务器返回的消息显示出来。

服务端程序`TCPServer.py`一直保持一个TCP欢迎套接字，可接收任何客户端的连接请求。在接收到客户端的连接请求后，创建一个新的TCP连接套接字用于单独与该客户通信，同时显示客户端地址和端口。在接收到客户端发来的字符串后，将其改为大写，然后向客户端返回修改后的字符串。 最后，关闭TCP连接套接字。

**TCPClient.py**

```python
# coding=gbk
from socket import *
serverName = '192.168.43.174' # 指定服务器地址
serverPort = 12000
clientSocket = socket(AF_INET, SOCK_STREAM) # 建立TCP套接字，使用IPv4协议
clientSocket.connect((serverName,serverPort)) # 向服务器发起连接
'''
在客户能够通过一个人TCP套接字向服务器发送数据之前，必须在客户与服务器之间创建一个TCP连接，
connect()方法就发起了这条TCP连接。
'''

sentence = input('Input lowercase sentence:').encode() # 用户输入信息，并编码为bytes以便发送
clientSocket.send(sentence) # 将信息发送到服务器
'''
此处程序并未像UDP套接字那样显式地创建一个分组并为该分组附上目的地址：
如clientSocket.sendto(sentence, (serverName, serverPort)) # 将信息发送到服务器。
而只是像上面那样：将字符串sentence中的字节放入该TCP链接中去(send()方法)，然后客户等待来自服务器的字节。
'''
modifiedSentence = clientSocket.recvfrom(1024) # 从服务器接收信息
print(modifiedSentence[0].decode()) # 显示信息
clientSocket.close() # 关闭套接字
```

**TCPServer .py**

```python
# coding=gbk
from socket import *
serverPort = 12000
'''创建欢迎套接字'''
serverSocket = socket(AF_INET, SOCK_STREAM) # 创建TCP欢迎套接字，使用IPv4协议
serverSocket.bind(('',serverPort)) # 将TCP欢迎套接字绑定到指定端口
serverSocket.listen(1) # 最大连接数为1
print("The server in ready to receive")

'''为每个客户端请求创建TCP连接套接字'''
while True:
	connectionSocket, addr = serverSocket.accept() # 接收到客户连接请求后，建立新的TCP连接套接字
	print('Accept new connection from %s:%s...' % addr)
	sentence = connectionSocket.recv(1024) # 获取客户发送的字符串
	capitalizedSentence = sentence.upper() # 将字符串改为大写
	connectionSocket.send(capitalizedSentence) # 向用户发送修改后的字符串
	connectionSocket.close() # 关闭TCP连接套接字
```



## 运行效果

先在一台机器上启动服务器程序，然后在另一台机器上启动客户端程序。运行效果如下：

**服务器端(在windows10下的vscode)：**

![image-20210626100517410](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626100517410.png)



**客户端(在linux下的vscode)：**

![image-20210626100434170](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626100434170.png)



## 修改

```python
#TCPClient.py 这样的修改并未能从服务器接收多个返回的字符串，如何修改留坑
# coding=gbk
from socket import *
serverName = '192.168.43.174' # 指定服务器地址
serverPort = 12000
clientSocket = socket(AF_INET, SOCK_STREAM) # 建立TCP套接字，使用IPv4协议
clientSocket.connect((serverName,serverPort)) # 向服务器发起连接
'''
在客户能够通过一个人TCP套接字向服务器发送数据之前，必须在客户与服务器之间创建一个TCP连接，
connect()方法就发起了这条TCP连接。
'''

while True:
    sentence = input('Input lowercase sentence:').encode() # 用户输入信息，并编码为bytes以便发送
    clientSocket.send(sentence) # 将信息发送到服务器
    '''
    此处程序并未像UDP套接字那样显式地创建一个分组并为该分组附上目的地址：
    如clientSocket.sendto(sentence, (serverName, serverPort)) # 将信息发送到服务器。
    而只是像上面那样：将字符串sentence中的字节放入该TCP链接中去(send()方法)，然后客户等待来自服务器的字节。
    '''
    modifiedSentence = clientSocket.recvfrom(1024) # 从服务器接收信息
    print(modifiedSentence[0].decode()) # 显示信息
    
#TCPServer.py
# coding=gbk
from socket import *
serverPort = 12000
'''创建欢迎套接字'''
serverSocket = socket(AF_INET, SOCK_STREAM) # 创建TCP欢迎套接字，使用IPv4协议
serverSocket.bind(('',serverPort)) # 将TCP欢迎套接字绑定到指定端口
serverSocket.listen(1) # 最大连接数为1
print("The server in ready to receive")

'''为每个客户端请求创建TCP连接套接字'''
while True:
	connectionSocket, addr = serverSocket.accept() # 接收到客户连接请求后，建立新的TCP连接套接字
	print('Accept new connection from %s:%s...' % addr)
	sentence = connectionSocket.recv(1024) # 获取客户发送的字符串
	capitalizedSentence = sentence.upper() # 将字符串改为大写
	connectionSocket.send(capitalizedSentence) # 向用户发送修改后的字符串
    
```

效果如下：

![image-20210626101207255](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626101207255.png)



![image-20210626101130706](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210626101130706.png)

