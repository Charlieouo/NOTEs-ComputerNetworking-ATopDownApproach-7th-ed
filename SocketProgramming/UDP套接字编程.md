# UDP套接字编程

## 描述
《自顶向下方法（原书第7版）》第2.7.1节给出了一个使用Python的UDP套接字编程实例，实现了一个简单的UDP通信程序。



## 代码

客户端程序`UDPClient.py`创建一个UDP套接字，并在用户输入一段小写字母组成的字符串后，发送到指定服务器地址和对应端口，等待服务器返回消息后，将消息显示出来。

服务端程序`TCPServer.py`一直保持一个可连接的UDP套接字，在接收到字符串后，将其改为大写，然后向客户端返回修改后的字符串。 

**UDPClient.py**
```python
from socket import *
serverName = '192.168.43.174' # 服务器地址，本例中使用一台远程主机
serverPort = 12000 # 服务器指定的端口
clientSocket = socket(AF_INET, SOCK_DGRAM) # 创建UDP套接字，使用IPv4协议
message = input('Input lowercase sentence:').encode() # 用户输入信息，并编码为bytes以便发送
clientSocket.sendto(message, (serverName, serverPort)) # 将信息发送到服务器
modifiedMessage, serverAddress = clientSocket.recvfrom(2048) # 从服务器接收信息，同时也能得到服务器地址
print(modifiedMessage.decode()) # 显示服务器返回的信息
clientSocket.close() # 关闭套接字
```


**UDPServer .py**
```python
from socket import *
serverPort = 12000 # 服务器指定的端口
serverSocket = socket(AF_INET, SOCK_DGRAM) # 创建UDP套接字，使用IPv4协议
serverSocket.bind(('',serverPort)) # 将套接字绑定到之前指定的端口
print("The server in ready to receive")
while True: # 服务器将一直接收UDP报文
	message, clientAddress = serverSocket.recvfrom(2048) # 接收客户端信息，同时获得客户端地址
	modifiedMessage = message.decode().upper() # 将客户端发来的字符串变为大写
	serverSocket.sendto(modifiedMessage.encode(), clientAddress) # 通过已经获得的客户端地址，将修改后的字符串发回客户端
```



## 运行

先在一台机器上启动服务器程序，然后在另一台机器上启动客户端程序。运行效果如下：

**服务器端(在windows10下的vscode)：**

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625193508777.png" alt="image-20210625193508777" style="zoom: 50%;" />

**客户端(在linux下的vscode)：**

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625193417075.png" alt="image-20210625193417075" style="zoom:67%;" />



## 修改

### 1. 修改客户端程序，使其在收到一个大写的句子后，用户能够向服务器继续发送更多的句子。

``` Python
# UDPClient.py 实现客户端发送多个字符串并从服务端接收多个字符串
# coding=gbk

from socket import *
serverName = '192.168.43.174' # 服务器地址，本例中使用一台远程主机
serverPort = 12000 # 服务器指定的端口
clientSocket = socket(AF_INET, SOCK_DGRAM) # 创建UDP套接字，使用IPv4协议

while True:
    message = input('Input lowercase sentence:').encode() # 用户输入信息，并编码为bytes以便发送
    clientSocket.sendto(message, (serverName, serverPort)) # 将信息发送到服务器
    modifiedMessage, serverAddress = clientSocket.recvfrom(2048) # 从服务器接收信息，同时也能得到服务器地址
    print(modifiedMessage.decode()) # 显示服务器返回的信息
```

效果如下：

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625122746531.png" alt="image-20210625122746531" style="zoom: 67%;" />





### 2.修改服务端程序， 不必将所有字母转换为大写，服务器可以计算字母s出现的次数并返回该数字。

``` python
#UDPServer.py 服务端计算来自客户端的字符串中字母s的出现次数
# coding=gbk
from socket import *
from typing import Counter
serverPort = 12000 # 服务器指定的端口
serverSocket = socket(AF_INET, SOCK_DGRAM) # 创建UDP套接字，使用IPv4协议
serverSocket.bind(('',serverPort)) # 将套接字绑定到之前指定的端口
print("The server in ready to receive")

while True: # 服务器将一直接收UDP报文
	message, clientAddress = serverSocket.recvfrom(2048) # 接收客户端信息，同时获得客户端地址
	s = message.decode()
	counter = s.count('s')
	serverSocket.sendto(str(counter).encode(), clientAddress) # 通过已经获得的客户端地址，将修改后的字符串发回客户端
```

效果如下：

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625135159543.png" alt="image-20210625135159543" style="zoom:67%;" />



#### 遇到的问题：

##### 1. **AttributeError: 'bytes' object has no attribute 'encode'**

没有注意到从客户端接收到的message已经是bytes类型的，而不用再次进行编码encode了。

而是应该将其解码decode为字符串类型(str)



<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625144204431.png" alt="image-20210625144204431" style="zoom: 67%;" />

​       解决：

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625142751677.png" alt="image-20210625142751677" style="zoom:50%;" />



##### 2. **AttributeError: ‘int‘ object has no attribute ‘encode‘**

向客户端返回int类型的数字时，应先将该int类型转换为str类型后，再进行编码encode为bytes类型



<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625144657126.png" alt="image-20210625144657126" style="zoom: 67%;" />



​       解决：

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/20201203174537856.png" alt="在这里插入图片描述" style="zoom: 67%;" />



##### 3. **TypeError: 'str' object is not callable**

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625145128111.png" alt="image-20210625145128111" style="zoom:67%;" />

这里是由于在用str方法之前声明了一个str的字符串变量，所以会说类型错误。修改的方法只需在前面的字符串变量名改成其他的就好。

<img src="https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210625145331283.png" alt="image-20210625145331283" style="zoom:67%;" />

