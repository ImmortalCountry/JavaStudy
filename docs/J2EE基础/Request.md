### ServletContext

>servlet上下文
>每个web工程只有一个ServletContext对象。也就是不管在哪个servlet里面，获取到的这个类的对象都是同一个。
>

### 如何得到对象

```java
	// 获取对象
	ServletContext context = getServletContext();
```

### 有什么作用

1. 获取全局配置参数
2. 获取web工程中的资源
3. 读取数据，servlet间共享数据、域对象。

#### 可以获取全局配置参数

```xml
	<!-- 全局参数 : 哪个 servlet 都可以拿, ServletContext>
	<context-param>
		<param-name>address</param-name>
		<param-value>哈尔滨</param-value>
	<context-param>
```

* 获取全局参数

```java
	protected void doGet(HttpServletRequest req, HttpServletResponse resp){
		// 获取对象
		ServletContext context = getServletContext();
		String address = context.getInitParameter("address")
	}
```

####  可以获取Web应用中的资源

```java
	// 1. 获取资源在tomcat里面的绝对路径; 先得到路径，然后自己new InpuStream
	// 这里得到的是项目在tomcat里面的根目录.
	context.getRealPath("");
	// D:\tomcat\apache-tomcat-7.0.52\apache-tomcat-7.0.52\workspace\StudyJ2EE\
	
	String path = context.getRealPath("file/config.properties");
	
	// D:\tomcat\apache-tomcat-7.0.52\apache-tomcat-7.0.52\workspace\StudyJ2EE\file\config.properties
	
	// 2. getResourceAsStream 获取资源流对象, 直接给相对的路径，然后获取流对象。
```

### 使用ServletContext存取数据

1. 定义一个登录的HTML页面, 定义一个form表单

```HTML
	<body>
	<form action="LoginServlet" method="post">
		账号：<input type="text" name="username" />
		密码：<input type="password" name="username" />
		<input type="submit" value="登录" />
	</form>
</body>
```

2. 定义一个Servlet，名为LoginServlet

```java
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		// 1. 获取数据
		String username = request.getParameter("username");
		String password = request.getParameter("password");
		
		// 校验数据
		
		// 向客户端输出内容
		PrintWriter pw = response.getWriter();
		
		if("admin".equals(username) && "123".equals(password)) {
			System.out.println("success");
			pw.write("login sucess");
			// 成功就跳转到login_sucess.html
			
			// 设置状态码？重新定位 状态码
			response.setStatus(302);
			
			response.setHeader("Location", "login_success.html");
		}else {
			pw.write("login failed.. ");
		}
	}
```

![img09](\resource\img09.png)

** ServletContext 的方法 setAttribute(key[String],value)**

### ServletContext 何时创建, 何时销毁?

服务器启动的时候，会为托管的每一个web应用程序，创建一个ServletContext对象
从服务器移除托管，或者是关闭服务器时销毁.

* ServletContext 的作用范围

>只要在这个项目里面，都可以取。 只要同一个项目。 A项目存， 在B项目取，是取不到的! ServletContext对象不同。
>
