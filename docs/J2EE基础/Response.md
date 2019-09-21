###  HttpServletRequest

>这个对象封装了客户端提交过来的一切数据。 
>
1. 可以获取客户端请求头信息

```java
	// 得到一个枚举集合
	Enumeration<String> headerNames = request.getHeaderNames();
	while (headerNames.hasMoreElements()) {
		String name = (String) headerNames.nextElement();
		String value = request.getHeader(name);
		System.out.println(name+"="+value);
	}
```

2. 获取客户端提交过来的数据

```java
	String name = request.getParameter("name");
	String address = request.getParameter("address");
	
	________________________________________________________
	
	// name=zhangsan&name=lisi&name=wangwu 一个key可以对应多个值。
	
	Map<String, String[]> map = request.getParameterMap();
	
	Set<String> keySet = map.keySet();
	Iterator<String> iterator = keySet.iterator();
	while (iterator.hasNext()) {
		String key = (String) iterator.next();
		System.out.println("key="+key + "--的值总数有："+map.get(key).length);
		String value = map.get(key)[0];
		String value1 = map.get(key)[1];
		String value2 = map.get(key)[2];
		
		System.out.println(key+" ======= "+ value + "=" + value1 + "="+ value2);
	}
```

3. 获取中文数据

>客户端提交数据给服务器端，如果数据中带有中文的话，有可能会出现乱码情况，那么可以参照以下方法解决。
>
* 如果是GET方式

1. 代码转码

```java
	String username = request.getParameter("username");
	
	String password = request.getParameter("password");
	
	System.out.println("userName="+username+"==password="+password);

	//get请求过来的数据，在url地址栏上就已经经过编码了，所以我们取到的就是乱码，
	//tomcat收到了这批数据，getParameter 默认使用ISO-8859-1去解码

	//先让文字回到ISO-8859-1对应的字节数组 ， 然后再按utf-8组拼字符串
	username = new String(username.getBytes("ISO-8859-1") , "UTF-8");
	System.out.println("userName="+username+"==password="+password);
```

**直接在tomcat里面做配置，以后get请求过来的数据永远都是用UTF-8编码.**

2. 可以在tomcat里面做设置处理 conf/server.xml 加上URIEncoding="utf-8"

```xml
	<Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443" URIEncoding="UTF-8"/>
```

* 如果是POST方式

这个说的是设置请求体里面的文字编码。  get方式，用这行，有用吗？ ---> 没用

```request.setCharacterEncoding("UTF-8");```

**这行设置一定要写在getParameter()方法之前。**

### HttpServletResponse

>负责返回数据给客户端。
>
* 输出数据到页面上

```java
	// 以字符流的方式写数据	
	// response.getWriter().write("<h1>hello response...</h1>");
	// 以字节流的方式写数据 
	response.getOutputStream().write("hello response...".getBytes());
```

#### 响应的数据中有中文，那么有可能出现中文乱码

* 以字符流输出

>response.getWriter()
>
```java
	//1. 指定输出到客户端的时候，这些文字使用UTF-8编码
	response.setCharacterEncoding("UTF-8");
	
	//2. 直接规定浏览器看这份数据的时候，使用什么编码来看。
	response.setHeader("Content-Type", "text/html; charset=UTF-8");
	
	response.getWriter().write("你好...");
```

* 以字节流输出 

>response.getOutputStream()
>
```java
	//1. 指定浏览器看这份数据使用的码表
	response.setHeader("Content-Type", "text/html; charset=UTF-8");
	
	//2. 指定输出的中文用的码表
	response.getOutputStream().write("你好...".getBytes("UTF-8"));
```

### 不管是字节流还是字符流，直接使用一行代码就可以了。

```response.setContentType("text/html;charset=UTF-8");```
然后在写数据即可。

#### 演练下载资源

1. 直接以超链接的方式下载，不写任何代码。 也能够下载东西下来。 

```HTML
	让tomcat的默认servlet去提供下载：<br>
	<a href="download/aa.jpg">aa.jpg</a><br>
	<a href="download/bb.txt">bb.txt</a><br>
	<a href="download/cc.rar">cc.rar</a><br>
```

>原因是tomcat里面有一个默认的Servlet -- DefaultServlet 。这个DefaultServlet 专门用于处理放在tomcat服务器上的静态资源。
>

```java
	// `1. 获取要下载的文件名字aa.jpg --- inputStream
	String fileName = request.getParameter("filename");
	
	// 2. 获取这个文件在tomcat里面的绝对路径地址
	String path = getServletContext().getRealPath("download/" + fileName)
	// 让浏览器收到这份资源的时候, 以下载的方式提醒用户, 而不是直接展示.
	response.setHeader("Content-Disposition", "attachment; filename=" + path);
	
	// 3. 转化成输入流
	InputStream is = new FileInputStream(path);
	OutputStream os = response.getOutputStream();
	
	int len = 0;
    byet[] buffer = new byte[1024];
    while((len = is.read(buffer)) != -1){
    	os.write(buffer, 0, len);
    }
    os.close();
    is.close();
```
##### 中文文件下载

>针对浏览器类型，对文件名字做编码处理 Firefox (Base64) , IE、Chrome ... 使用的是URLEncoder
>
```java
	/*
	 * 如果文件的名字带有中文，那么需要对这个文件名进行编码处理
	 * 如果是IE ，或者  Chrome （谷歌浏览器） ，使用URLEncoding 编码
	 * 如果是Firefox ， 使用Base64编码
	 */
	//获取来访的客户端类型
	String clientType = request.getHeader("User-Agent");
	
	if(clientType.contains("Firefox")){
		fileName = DownLoadUtil.base64EncodeFileName(fileName);
	}else{
		//IE ，或者  Chrome （谷歌浏览器） ，
		//对中文的名字进行编码处理
		fileName = URLEncoder.encode(fileName,"UTF-8");
	}
```


## 总结

1. Servlet注册方式 
2. ServletContext【重点】
	作用:
	```HTML
		1. 获取全局参数

		2. 获取工程里面的资源。

		3. 资源共享。  ServletContext 域对象

	有几个?
		一个
	什么时候创建 ？什么时候销毁
		服务器启动的时候给每一个应用都创建一个ServletContext对象;
		服务器关闭的时候销毁.
	```
	
## 总结
1. HttpServletRequest【重点】

	获取请求头
		获取提交过来的数据
	
2. HttpServletResponse【重点】

	负责输出数据到客户端，其实就是对之前的请求作出响应
	
3. 中文乱码问题。【重点】
4. 下载