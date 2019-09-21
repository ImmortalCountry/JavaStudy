



### Web资源

在http协议当中，规定了请求和响应双方， 客户端和服务器端。与web相关的资源。
有两种分类：
1. 静态资源

`HTML、CSS、JS`

2. 动态资源

`Servlet/jsp`

### Servlet介绍

>Servlet（Server Applet），全称Java Servlet，未有中文译文。 是用Java编写的服务器端程序。 其主要功能在于交互式地浏览和修改数据，生成动态Web内容。
>
> 其实就是一个java程序，运行在我们的web服务器上，用于接收和响应 客户端的http请求。 
> 更多的是配合动态资源来做。 当然静态资源也需要使用到servlet，只不过是Tomcat里面已经定义好了一个 DefaultServlet 

### Hello Servlet

新建一个Servlet时需要在webContent/WEB-INF/web.xml里面写配置。

```XML
	<servlet>
  	<!-- servlet自定义的内部名称，要有意义 -->
    <servlet-name>TestServlet</servlet-name>
    <!-- servlet的具体路径：报名+简单类名-->
    <servlet-class>com.study.TestServlet</servlet-class>
  </servlet>
  
  <!-- servlet的映射配置 -->
  <servlet-mapping>
  	<!-- servlet的内部名称，要和上面的内部名称保持一致！！！ -->
    <servlet-name>TestServlet</servlet-name>
    
    <!-- servlet的映射路径（访问servlet的名称） -->
     <!-- 下面的映射路径要加/，否则会出错 -->
    <url-pattern>/TestServlet</url-pattern>
  </servlet-mapping>
  
```

配置映射路径：

* 精确匹配

| url-pattern  |                 浏览器输入                  |
| :----------: | :-----------------------------------------: |
| /TestServlet | http://localhost:8080/StudyJ2EE/TestServlet |

* 模糊匹配

| url-pattern    | 浏览器输入                                        |
| -------------- | ------------------------------------------------- |
| /*             | http://localhost:8080/StudyJ2EE/TestServlet       |
| /Other/*       | http://localhost:8080/StudyJ2EE/Other/TestServlet |
| *.后缀名       | http://localhost:8080/StudyJ2EE/任意.后缀名       |
| *.do           |                                                   |
| *.action       |                                                   |
| *.html(伪静态) |                                                   |

**注意**

1. url-pattern要么以 / 开头，要么以*开头。
2. 不能同时使用两种模糊匹配，例如 /Other/*.do是非法路径
3. 当有输入的URL有多个servlet同时被匹配的情况下：
3.1 精确匹配优先。（长的最像优先被匹配）
3.2  以后缀名结尾的模糊匹配先级最低

### servlet 执行过程

1. 找到Tomcat应用
2. 找到项目
3. 找到web.xml，然后在里面找到url-patten，看有没有哪一个patten的内容是/TestServlet
4. 找到servlet-mapping中的servlet-name
5. 找到上面servlet的元素中的servlet-name中的【TestServlet】
6. 找到servlet-class中的类，然后创建
7. 继而执行servlet中的方法

### servlet的通用写法

```
	Servlet (接口)
		|
		|
	GenericServlet
		|
		|
	HttpServlet （用于处理http的请求）
```

### servlet的生命周期

* 生命周期

>从创建到销毁的一段时间
>
* 生命周期的方法

>从创建到销毁，所调用那些方法
>

* init方法

在创建该Servlet的实例时，就执行它。该方法可以被执行很多次，一次请求，对应一次servlet方法的调用。

* destroy方法

servlet销毁的时候，就会执行该方法。
	何时销毁？
	1. 项目从tomcat中移除。
	2. 正常关闭tomcat就会执行 shutdown.bat

** doGet和doPost方法不算生命周期方法，所谓周期方法是指：从对象的创建到销毁一定会执行的方法。**

### 让servlet创建实例的时机提前。

1. 默认情况下，只有在初次访问servlet的时候，才会执行init方法。 有的时候，我们可能需要在这个方法里面执行一些初始化工作，甚至是做一些比较耗时的逻辑。 
2. 那么这个时候，初次访问，可能会在init方法中逗留太久的时间。 那么有没有方法可以让这个初始化的时机提前一点。 
3. 在配置的时候， 使用load-on-startup元素来指定， 给定的数字越小，启动的时机就越早。 一般不写负数， 从2开始即可。 

```xml
	<servlet>
	  	<servlet-name>TestServle</servlet-name>
	  	<servlet-class>com.study.TestServlet</servlet-class>
	  	<load-on-startup>2</load-on-startup>
	</servlet>
```

## ServletConfig

>Servlet的配置，通过这个对象，可以获取servlet在配置的时候一些信息
>
```java
	// 1. 得到servlet的配置对象，专门用于在配置servlet的信息
	ServletConfig config = getServletConfig();
	
	// 获取到的是配置servlet里面的servlet-name的文本内容
	String servletName = config.getInitParameterName();
	
	// 2. 获取具体的某一个参数
	String address = config.getInitParameter("address");
	
	//3. 获取所有的参数名称
	Enumeration<String> names = config.getInitParameterNames();
	//遍历取出所有的参数名称
	while (names.hasMoreElements()) {
		String key = (String) names.nextElement();
		String value = config.getInitParameter(key);
		System.out.println("key==="+key + "   value="+value);
	}
```

* 为什么用ServletConfig

1. 未来我们自己开发的一些应用，使用到了一些技术，或者一些代码，我们不会。 但是有人写出来了。它的代码放置在了自己的servlet类里面。 
2. 刚好这个servlet 里面需要一个数字或者叫做变量值。 但是这个值不能是固定了。 所以要求使用到这个servlet的公司，在注册servlet的时候，必须要在web.xml里面，声明init-params
** 在开发当中比较少用。** 

## 总结

* HTTP协议

1. 使用HttpWacht 抓包看一看http请求背后的细节。 
2. 基本了解 请求和响应的数据内容：
	请求行、请求头、请求体
	响应行、响应头、响应体
3. Get和Post的区别

* Servlet【重点】

1. 会使用简单的servlet
1.1 写一个类，实现接口Servlet
1.1.1 配置Servlet
1.1.1.1 会访问Setvlet
2. Servlet的生命周期

**init 一次 ：创建对象 默认初次访问就会调用或者可以通过配置，让它提前 load-on-startup
service 多次 ：一次请求对应一次 service
destory 一次 ：销毁的时候 从服务器移除 或者 正常关闭服务器 **

3. ServletConfig

获取配置的信息， params。