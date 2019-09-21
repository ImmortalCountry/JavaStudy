### 介绍

* 什么是协议

>协议，网络协议的简称，网络协议是通信计算机双方必须共同遵从的一组约定。 如怎么样建立连接、怎么样互相识别等。 只有遵守这个约定，计算机之间才能相互通信交流。 它的三要素是：语法、语义、时序。
>
* HTTP协议

>HTTP协议是Hyper Text Transfer Protocol（超文本传输协议）的缩写,是用于从万维网（WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。 HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

### 版本

#### 1.0版本

>请求数据，服务器返回后， 将会断开连接

首先，任何格式的内容都可以发送; 除了`GET`命令，还引入了`POST`命令和`HEAD`命令; 再次，HTTP请求和回应的格式也变了。除了数据部分，每次通信都必须包括头信息（HTTP header），用来描述一些元数据。其他的新增功能还包括状态码（status code）、多字符集支持、多部分发送（multi-part type）、权限（authorization）、缓存（cache）、内容编码（content encoding）等。

* 缺点

每次`TCP`连接只能发送一个请求. 发送完数据,连接就关闭,如果还要请求别的资源,还需要再新建一个链接.

TCP建立连接成本很高,因为需要客户端和服务器三次握手,并且开始发送速率较慢,所以性能较差.

为解决这问题,有些浏览器在请求时,用了一个非标准的Connection字段.

`Connection: keep-alive`

这个字段要求服务器不要关闭TCP连接，以便其他请求复用。服务器同样回应这个字段。

`Connection: keep-alive`

一个可以复用的TCP连接就建立了，直到客户端或服务器主动关闭连接。但是，这不是标准字段，不同实现的行为可能不一致，因此不是根本的解决办法。

#### 1.1版本

>请求数据，服务器返回后， 连接还会保持着。 除非服务器 | 客户端 关掉。 有一定的时间限制，如果都空着这个连接，那么后面会自己断掉。

1997年1月，HTTP/1.1 版本发布，只比 1.0 版本晚了半年。它进一步完善了 HTTP 协议，一直用到了20年后的今天，直到现在还是最流行的版本。

* 持久连接

1.1 版的最大变化，就是引入了持久连接（persistent connection），即TCP连接默认不关闭，可以被多个请求复用，不用声明Connection: keep-alive。

客户端和服务器发现对方一段时间没有活动，就可以主动关闭连接。不过，规范的做法是，客户端在最后一个请求时，发送Connection: close，明确要求服务器关闭TCP连接。

* 管道机制

1.1 版还引入了管道机制（pipelining），即在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。

举例来说，客户端需要请求两个资源。以前的做法是，在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后再发出B请求。管道机制则是允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

### HTTP请求数据解释 

>请求的数据里面包含三个部分内容 ： 请求行 、 请求头 、请求体
>
* 请求行

POST /examples/servlets/servlet/RequestParamExample HTTP/1.1 

```
    POST: 请求方式, 以post去提交数据;
    /examples/servlets/servlet/RequestParamExample: 请求的地址路径, 就是要访问哪个地方。
    HTTP/1.1 协议版本
```
* 请求头

Accept: application/x-ms-application, image/jpeg, application/xaml+xml, image/gif, image/pjpeg, application/x-ms-xbap, */*
	Referer: http://localhost:8080/examples/servlets/servlet/RequestParamExample
	Accept-Language: zh-CN
	User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)
	Content-Type: application/x-www-form-urlencoded
	Accept-Encoding: gzip, deflate
	Host: localhost:8080
	Content-Length: 31
	Connection: Keep-Alive
	Cache-Control: no-cache
	
```
    Accept: 客户端向服务器端表示，我能支持什么类型的数据。 
    Referer ： 真正请求的地址路径，全路径
    Accept-Language: 支持语言格式
    User-Agent: 用户代理 向服务器表明，当前来访的客户端信息。 
    Content-Type： 提交的数据类型。经过urlencoding编码的form表单的数据
    Accept-Encoding： gzip, deflate ： 压缩算法 。 
    Host ： 主机地址
    Content-Length： 数据长度
    Connection : Keep-Alive 保持连接
    Cache-Control ： 对缓存的操作
```

* 请求体

>浏览器真正发送给服务器的数据
>
```
	发送的数据呈现的是key=value ,如果存在多个数据，那么使用 &
	firstname=zhang&lastname=sansan
```

### HTTP响应数据解析

* 响应行

>HTTP/1.1 200 OK

```
	协议版本  
	
	状态码 
  
  	咱们这次交互到底是什么样结果的一个code. 
  
  	200 : 成功，正常处理，得到数据。
  
  	403  : for bidden  拒绝
  	404 ： Not Found
  	500 ： 服务器异常
  
  OK
  
  	对应前面的状态码  
```

* 响应头

```
  Server:  服务器是哪一种类型。  Tomcat
  
  Content-Type ： 服务器返回给客户端你的内容类型
  
  Content-Length ： 返回的数据长度
  
  Date ： 通讯的日期，响应的时间		
```

### Get 和  Post请求区别

* Post:

1. 数据以流方式写过去，不会显示在地址栏上面。
2. 以流方式写数据没有大小限制
3. 因为使用流，所以一定需要一个Content-Length的头来说明数据的长度有多少

* Get

1. 会在地址栏后面拼接数据，所以有安全隐患
2. 一般从服务器获取数据，并且客户端也不用提交上面的数据时候，可以使用GET
3. 能够带的数据有限，1kb大小