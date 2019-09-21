## Cookie

>饼干、 其实是一份小数据， 是服务器给客户端，并且存储在客户端上的一份小数据
>
## 应用场景

>自动登录、浏览记录、购物车。
>
### 为什么要有这个Cookie

>HTTP的请求是无状态。 客户端与服务器在通讯的时候，是无状态的，其实就是客户端在第二次来访的时候，服务器根本就不知道这个客户端以前有没有来访问过。 为了更好的用户体验，更好的交互 [自动登录]，其实从公司层面讲，就是为了更好的收集用户习惯[大数据]
>
### Cookie怎么用

#### 简单使用：

* 添加Cookie给客户端

1. 在响应的时候

```java
	Cookie cookie = new Cookie("aa", "bb");
	// 给响应添加一个cookie
	response.addCookie(cookie);
```

2. 客户端收到的信息里面，响应头中多了一个字段 Set-Cookie

`Set-Cookie : aa=bb`

* 获取客户端带过来的Cookie

```java
	// 获取客户端带过来的Cookie
	Cookie[] cookies = request.getCookies();
	if(cookies != null){
		for(Cookie c : cookies){
			String cookieName = c.getName();
			String cookieValue = c.getValue();
			System.out.println(cookieName + " = "+ cookieValue);
		}
	}
```

* 常用方法

```java
	// 关闭浏览器后，cookie就没有了。--->针对没有设置cookie的有效期。
	// expiry： 有效， 以秒计算
	// 正值：表示在这个数字过后，cookie就会失效
	// 负值：关闭浏览器，那么cookie就失效，默认值是-1
	cookie.setMaxAge(60*60*24*7);
	
	// 赋值新的值
	// cookie.setValue(newValue);
	
	// 用于指定只有请求了指定的域名，才会带上该cookie
	cookie.setDomain(".study.com");
	
	// 只有访问该域名下的cookieDemo的这个路径地址才会带cookie
	cookie.setPath("/cookieDemo");
```

#### 例子一 显示最近访问的时间。

1. 判断账号是否正确
2. 如果正确，则获取cookie。 但是得到的cookie是一个数组， 我们要从数组里面找到我们想要的对象。
3. 如果找到的对象为空，表明是第一次登录。那么要添加cookie
4. 如果找到的对象不为空， 表明不是第一次登录。 

```JAVA
	if("admin".equals(userName) && "123".equals(password)){
		//获取cookie last-name --- >
		Cookie [] cookies = request.getCookies();
		
		//从数组里面找出我们想要的cookie
		Cookie cookie = CookieUtil.findCookie(cookies, "last");
		
		//是第一次登录，没有cookie
		if(cookie == null){
			Cookie c = new Cookie("last",System.currentTimeMillis()+"");
			c.setMaxAge(60*60); //一个小时
			response.addCookie(c);
			response.getWriter().write("欢迎您, "+userName);
		}else{
			//1. 去以前的cookie第二次登录，有cookie
			long lastVisitTime = Long.parseLong(cookie.getValue());
			//2. 输出到界面，
			response.getWriter().write("欢迎您, "+userName +",上次来访时间是："+new Date(lastVisitTime));
		
			//3. 重置登录的时间
			cookie.setValue(System.currentTimeMillis()+"");
			response.addCookie(cookie);
		}
	}else{
		response.getWriter().write("登陆失败 ");
	}
			
```

##### `CookieUtil`

```java
	import javax.servlet.http.Cookie;

	public class CookieUtil {
        /**
         * 从一个cookie数组中找到具体我们想要的cookie对象
         * @param cookies
         * @param name
         * @return
         */
        public static Cookie findCookie(Cookie[] cookies , String name){
        	
            if(cookies != null){
            	for (Cookie cookie : cookies) {
                	if(name.equals(cookie.getName())){
                    	return cookie;
                    }
                }
            }
            return null;
        }
    }
```

####  例子二： 显示商品浏览记录。

##### 分析

![img03](\resource\img03.png)

* JSP

>Java Server Pager ---> 最终会翻译成一个类， 就是一个Servlet
>

* 定义全局变量

  <%! int a = 99; %>

* 定义局部变量

  <% int b = 999; %>

* 在jsp页面上，显示 a 和 b的值，

  <%=a %> 
  <%=b E%>

##### 

```jsp
	<ul style="list-style: none;">
		<%
			Cookie[] cookie = request.getCookies();
			Cookie cook = 	CookieUtil.findCookie(cookie, "history");
			if(cook == null){
		%>
			<h2>您还没浏览任何商品</h2>
					
		<%
			}else{
				String [] id =	cook.getValue().split("#");
				for(String i : id){
		%>
			<li style="width: 150px;height: 216;float: left;margin: 0 8px 0 0;padding: 0 18px 15px;text-align: center;"><img src="products/1/cs1000<%=i %>.jpg" width="130px" height="130px" /></li>
		<%			
				}
			}
				
		%>
	</ul>
```

##### 清除浏览记录

>其实就是清除Cookie， 删除cookie是没有什么delete方法的。只有设置maxAge 为0 。
>
```JAVA
	Cookie cookie = new Cookie("history","");
	cookie.setMaxAge(0); //设置立即删除
	cookie.setPath("/CookieDemo02");
	response.addCookie(cookie);
```

### Cookie总结

1. 服务器给客户端发送过来的一小份数据，并且存放在客户端上。

2. 获取cookie

	`request.getCookie();`
3. 添加cookie

	`response.addCookie();`

4. Cookie分类

	会话Cookie ：默认情况下，关闭了浏览器，那么cookie就会消失。

	持久Cookie ：
	```java
		// 在一定时间内，都有效，并且会保存在客户端上。 
		cookie.setMaxAge(0); //设置立即删除
		cookie.setMaxAge(100); //100 秒
	```

5. Cookie的安全问题。

>由于Cookie会保存在客户端上, 所以有安全隐患问题. 还有一个问题, Cookie的大小与个数有限制. 为了解决这个问题 ---> Session .
>

## Session

>会话, Session是基于Cookie的一种会话机制。 Cookie是服务器返回一小份数据给客户端，并且存放在客户端上。 Session是数据存放在服务器端。

* API

```java
	//得到会话ID
	String id = session.getId();
	
	//存值
	session.setAttribute(name, value);
	
	//取值
	session.getAttribute(name);
	
	//移除值
	session.removeAttribute(name);
```

* Session何时创建，何时销毁?
* 创建

>如果有session, 在servlet里面调用了 request.getSession()
>
* 销毁

>session 是存放在服务器的内存中的一份数据。 当然可以持久化. Redis . 即使关了浏览器，session也不会销毁。
>只有在：
> 1. 关闭服务器
> 2. session会话时间过期。有效期过了，默认有效期：30分钟

### 简单购物车。

![img05](../resource/img05.png)

### CartServlet

```java
	response.setContentType("text/html;charset=utf-8");
	
	// 1. 获取要添加到购物车的商品id
	int id = Integer.parseInt(request.getParameter("id")); // 0、1、2、3、
	String [] names = {"Iphone7","小米6","三星Note8","魅族7" , "华为9"};
	
	// 取到id对应的商品名称
	String name = name[id];
	
	// 2. 获取购物车存放东西的session Map<String, Integer> iphone7 3
	Map<String, Integer> map = (Map<string, Integer>) request.getSession().getAttribute("cart");
	
	// session里面没有放过任何东西
	if(map == null){
		map = new LinkedHashMap<String , Integer>();
		request.getSession().setAttribute("cart", map);
	}
	
	// 3. 判断购物车里面有该商品
	if(map.containsKey(name)){
	
		// 在原来基础上加一
		map.put(name, map.get(name) + 1);
	}else{
		map.put(name, 1);
	}
	
	// 输出界面
	response.getWriter().write("<a href='product_list.jsp'><h3>继续购物	</h3></a><br>");
	response.getWriter().write("<a href='cart.jsp'><h3>去购物车结算</h3></a>");
```

### 移除Session中的元素

```java
	//强制干掉会话，里面存放的任何数据就都没有了。
	session.invalidate();
	
	//从session中移除某一个数据
	//session.removeAttribute("cart");
```

## 总结

* 请求转发和重定向（面试经常问。）
* Cookie

	服务器给客户端发送一小份数据， 存放在客户端上。
		基本用法：
			添加cookie
			获取cookie。
		演练例子
			1. 获取上一次访问时间
			2. 获取商品浏览记录。

* 什么时候有cookie

`response.addCookie(new Cookie());`

* Cookie 分类

	1. 会话Cookie : 关闭浏览器，就失效
	2. 持久cookie : 存放在客户端上。 在指定的期限内有效。用`setMaxAge()`设置 默认-1;

* Session

也是基于cookie的一种会话技术，数据存放存放在服务器端

```
    1. 会在cookie里面添加一个字段 JSESSIONID，是tomcat服务器生成。 

    2. setAttribute 存数据

    3. getAttribute 取数据

    4. removeAttribute  移除数据

    5. getSessionId();  获取会话id

    6. invalidate() 强制让会话失效。
```

* 创建和销毁

	1. 调用`equest.getSesion`建 
	2. 销毁 ：服务器关闭，会话超时（30分）

* `setAttribute` 存放的值， 在浏览器关闭后，还有没有？

	**存在了服务器上，和本地无关，必须有**