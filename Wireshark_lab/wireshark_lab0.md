## 0. 配置Wireshark

![image-20210615202453587](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615202453587.png)

## 1. 打开谷歌浏览器并将给定的URL输入到浏览器中

当Wireshark正在运行时，输入URL：http://gaia.cs.umass.edu/wireshark-labs/INTRO-wireshark-file1.html ，并在浏览器中显示该页面。

![image-20210615202247989](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615202247989.png)

##  2. 利用Wireshark进行抓包

在主Wireshark窗口顶部的分组显示过滤器窗口中，键入“http”（不含引号，且小写 - Wireshark中的所有协议名称均为小写）。


![image-20210615202927822](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615202927822.png)



##  3. 解答问题

###  1. 列出上述步骤2中出现在未过滤的分组列表窗口的协议列中的3种不同的协议

![image-20210615203030075](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615203030075.png)

在上图中未过滤分组列表中我们看到有**ARP、TCP、HTTP、SSDP**等不同的协议。

- **ARP**协议：地址解析协议，即ARP（Address Resolution Protocol），是**根据IP地址获取物理地址**的一个TCP/IP协议。

- **TCP**协议：传输控制协议，即TCP（Transmission Control Protocol），是一种面向连接的、可靠的、基于字节流的**传输层**通信协议

- **HTTP**协议：超文本传输协议，即HTTP（Hypertext Transfer Protocol），是一个简单的请求-响应协议，它通常运行在TCP之上。

- **SSDP**协议：简单服务发现协议，即SSDP，是**应用层**协议，是构成UPnP（通用即插即用）技术的核心协议之一。



###  2.  从HTTP GET消息发送到HTTP OK回复需要多长时间？

![image-20210615204646710](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615204646710.png)

这个就比较有意思了，因为题目要求是HTTP GET 消息发送到HTTP OK回复需要多长时间。而根据我的多次打开参考URL(上图，三次)，发现每次返回的状态码都不是200 OK，而是304 NOT Modified，我心里一惊，认为自己是不是做错了，是不是自己的“打开方式”不对，我又去试了几遍，结果发现返回的状态码都是304 NOT Modified(BTW，不是每次打开该URL都能抓到包，我也很好奇为什么，按照目前的知识还不足以解决，留坑)。然后我就去搜了一下200 OK 和 304 NOT Modified 是什么，又有什么区别，结果如下：

- **2xx**

  2xx 类状态码表示服务器**成功**处理了客户端的请求，也是我们最愿意看到的状态。

  - 「**200 OK**」是最常见的成功状态码，表示⼀切正常。如果是非HEAD 请求，服务器返回的响应头都会有 body数据。

  - 「**204 No Content**」也是常见的成功状态码，与 200 OK 基本相同，但响应头没有 body 数据。

- **3xx**

    3xx 类状态码表示客户端请求的资源发送了变动，需要客户端用新的 URL 重新发送请求获取资源，也就是**重定向**。

   -  「**301 Moved Permanently**」表示永久重定向，说明请求的资源已经不存在了，需改用新的 URL 再次访问。

   -  「**302 Found**」表示临时᯿定向，说明请求的资源还在，但暂时需要用另⼀个 URL 来访问。

         > 301 和 302 都会在响应头里使用字段 Location ，指明后续要跳转的 URL，浏览器会自动重定向新的 URL。

    - 「**304 Not Modified**」不具有跳转的含义，表示资源未修改，重定向已存在的缓冲文件，也称缓存重定向，⽤于缓存控制。

- **区别**
  
  - 200 OK (from memory cache)或者200 OK (from disk cache)都是直接读取客户端的缓存，**无需再请求服务器**。
  - 304 Not Modified 缓存和上面最大的区别是**浏览器需要向服务器询问一次**，如果服务器端认为没有内容更新，直接返回304状态码，无需返回body内容，浏览器就会直接取缓存内容输出，这样省掉了没必要的数据传输，也就提升了访问速度。

所以，总的来说，无论是200 OK 还是 304 Not Modified 都意味着访问正确(从客户端和服务器端来看都没有出错)，只不过**304需要向服务器询问一次确认缓存有效性，而200则是直接读取客户端(浏览器)的缓存而不进行询问**。

> 参考链接：**[使用HTTP缓存状态码200和304提升网站访问速度]**https://www.huoduan.com/statuscode-200-304.html

![image-20210615211249363](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615211249363.png)

回到正题，根据上图，我们很容易算出**GET到304的时间为0.944s - 0.663s = 0.281s**



##  3. gaia.cs.umass.edu的Internet地址是什么？您的计算机的Internet地址是什么？

###  两种 HTTP 请求方法：GET 和 POST

在客户机和服务器之间进行请求-响应时，两种最常被用到的方法是：GET 和 POST。

- **GET** - 从指定的资源请求数据。
- **POST** - 向指定的资源提交要被处理的数据。

![image-20210615211249363](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615211249363.png)

根据GET方法的定义结合上图，我们可以直到，通过GET方法，我们从source(客户端)向指定的资源(服务器端)请求数据。所以**source对应的Internet地址即为主机的Internet地址：10.63.83.103**。而**destination对应的Internet地址即为服务器端(gaia.cs.umass.edu)的Internet地址：128.119.245.12**。当返回304时，是由服务器端向客户端传送数据，所以Internet的地址刚好相反。客户端的Internet地址很容易验证，因为我在开始捕获分组前就**将捕获的接口仅限于WLAN**，如下图：

![image-20210615212710221](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210615212710221.png)

这样就使得有且仅有一个接口会被捕获，而且我们可以看到该WLAN的地址正是10.63.83.103，也就是我们客户端的Internet地址，所以便验证了我们上述的说法，即我们客户端的Internet地址为10.63.83.103，服务器端(gaia.cs.umass.edu)的Internet地址为128.119.245.12。



## 4. 打印问题2提到的两个HTTP消息(GET和OK)。

很遗憾，我在打印的时候，wireshark软件直接崩溃了，暂时弄不了TAT。



##   5. 加餐

### 1. Packet Details Pane(数据包详细信息)

![image-20210616105614122](https://gitee.com/Charlieouo/pic_bed/raw/master/img/image-20210616105614122.png)



在数据包列表中选择指定数据包，在数据包详细信息中会显示数据包的所有详细信息内容。数据包详细信息面板是最重要的，用来查看协议中的每一个字段。各行信息分别为

 （1）Frame:  物理层的数据帧概况

 （2）Ethernet II: 数据链路层以太网帧头部信息

 （3）Internet Protocol Version 4: 网络层IP包头部信息

 （4）Transmission Control Protocol: 传输层T的数据段头部信息，此处是TCP

 （5）Hypertext Transfer Protocol: 应用层的信息，此处是HTTP协议



###  2. TCP包的具体内容

 从下图可以看到Wireshark捕获到的TCP包中的每个字段。





























