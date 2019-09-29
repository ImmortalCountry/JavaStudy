# XML 配置

1. 首先引入约束
2. bean

## Bean 定义

被称作 bean 的对象是构成应用程序的支柱也是由 Spring IOC 容器管理的。bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。这些 bean 是由用容器提供的配置元数据创建的。

bean 定义包含称为**配置元数据**的信息，下述容器也需要知道配置元数据：

- 如何创建一个 bean
- bean 的生命周期的详细信息
- bean 的依赖关系

上述所有的配置元数据转换成一组构成每个 bean 定义的下列属性。

| 属性                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| class                    | 这个属性是强制性的，并且指定用来创建 bean 的 bean 类。       |
| name                     | 这个属性指定唯一的 bean 标识符。在基于 XML 的配置元数据中，你可以使用 ID 和/或 name 属性来指定 bean 标识符。 |
| scope                    | 这个属性指定由特定的 bean 定义创建的对象的作用域，它将会在 bean 作用域的章节中进行讨论。 |
| constructor-arg          | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| properties               | 它是用来注入依赖关系的                                       |
| autowiring mode          | 它是用来注入依赖关系的                                       |
| lazy-initialization mode | 延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例。 |
| initialization 方法      | 在 bean 的所有必需的属性被容器设置之后，调用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |
| destruction 方法         | 当包含该 bean 的容器被销毁时，使用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |

> 标签的id和name的配置:
>
> - id: 使用了约束中的唯一约束。里面不能出现特殊字符的。
> - name: 没有使用约束中的唯一约束（理论上可以出现重复的，但是实际开发不能出现的）。里面可以出现特殊字符。
>   什么时候用
> - Spring和Struts1框架整合的时候
> - `<bean name="/user" class=""/>`

## Bean的生命周期

`<bean id="userDao" class="com.itcast.spring.UserDaoImp" init-method="类中自定义方法" destroy-method="类中自定义方法"></bean>`

- init-method :Bean被初始化的时候执行的方法
- destroy-method :Bean被销毁的时候执行的方法（Bean是单例创建，工厂关闭）

## Bean的作用范围的配置（重点）

- scope：Bean的作用范围
  - singleton ：**默认的，Spring会采用单例模式创建这个对象。**
  - prototype：**多例模式。（Struts2和Spring整合一定会用到的）**
  - request：应用在web项目中，Spring创建这个类以后，将这个类存入到request范围中。
  - session：应用在web项目中，Spring创建这个类以后，将这个类存入到session范围中。
  - globalsession：应用在web项目中，必须在porlet环境下使用。但是如果没有这种环境，相对于session。
    - porlet：子系统。比如登陆了百度，那么进入百度地图等就不需要登陆了（全局session）

## 属性注入

1. 构造方法的方式的属性注入

类要实现有参的构造方法，然后xml配置如下：

```xml
	<bean id="userDao" class="com.study.UserDaoImp">
		<constructor name="name" value="大G" />
		<constructor name="price" value="5000000">
		<!-- 成员变量里有类，用ref -->
		<constructor name="User" ref="另一个类的id" />
	</bean>
```

2. set方法的方式的属性注入

提供set方法，配置文件与1相似，只不过使用<property>标签

3. P名称空间的属性注入（Spring2.5以后）

- 通过引入p名称空间完成属性的注入：

`xmlns:p="xxxxxxxx"`

```
* 写法：
	
	* 普通属性	p:属性名=”值”
	* 对象属性	p:属性名-ref=”值”
```

4. SpEL的属性注入（Spring3.0以后）

- 语法
  - #{SpEL}

```xml
	<!-- SpEL的属性注入 -->
	<bean id="carInfo" class="com.study.CarInfo">
	</bean>
	
	<bean id="car2" class="com.study.Car2">
		<property name="name" value="#{carInfo.name}"></property>
		<property name="price" value="#{carInfo.calculatorPrice()}"></property>
	</bean>
	
	<bean id="employee" class="com.study.Employee">
		<property name="name" value="#{'赵洪'}"></property>
		<property name="car2" value="#{car2}"></property>
	</bean>
```

**要有get方法，方法要有返回值**

#### 集合类型属性注入(了解)

```xml
	<!-- Spring的集合属性的注入============================ -->
	<!-- 注入数组类型 -->
	<bean id="collectionBean" class="com.study.CollectionBean">
		<!-- 数组类型 -->
		<property name="arrs">
			<list>
				<value>王东</value>
				<value>赵洪</value>
				<value>李冠希</value>
			</list>
		</property>
		
		<!-- 注入list集合 -->
		<property name="list">
			<list>
				<value>李兵</value>
				<value>赵如何</value>
				<value>邓凤</value>
			</list>
		</property>
		
		<!-- 注入set集合 -->
		<property name="set">
			<set>
				<value>aaa</value>
				<value>bbb</value>
				<value>ccc</value>
			</set>
		</property>
		
		<!-- 注入Map集合 -->
		<property name="map">
		
			<map>
				<entry key="aaa" value="111"/>
				<entry key="bbb" value="222"/>
				<entry key="ccc" value="333"/>
			</map>
		</property>
	</bean>
```

### 分模块配置

1. 在加载配置文件的时候，加载多个

`ApplicationContext ac = new ClassPathXmlApplicationContext("a1,xml","a2.xml");`

2. 在一个配置文件中引入多个配置文件(如在a1.xml中)

`<import resource="a2.xml">`

### Spring整合web项目问题

每次请求都会创建Spring的工厂，浪费服务器资源

- 解决:

1. 在服务器启动的时候创建Spring工厂

2. 将工厂保存到ServletContext中

3. 每次获取工厂的时候从ServletContext中获取

   **使用ServletContextListener**

   **监听ServletContext对象的创建和销毁**

- Spring已经给我们实现了

1. 引入spring_web.jar

2. 配置ContextLoaderListener

   - 默认加载的是/WEB-INF/applicationContext.xml
   - 配置修改加载文件的路径

   ```xml
   <!-- 配置Spring的核心监听器 -->
   <listener>
   	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   </listener>
     
   <context-param>
     	<param-name>contextConfigLocation</param-name>
     	<param-value>classpath:applicationContext.xml</param-value>
   </context-param>
   ```

   

3. 通过工具类获取工厂

# 注解IOC

## Spring IOC 注解开发入门

**注解开发让你不用配置复杂的xml文件了**

1. 引入jar包
2. 引入相关配置文件(引入context约束)
3. 编写相关的类

```java
	public interface UserDao{
		public void test();
	}
	
	public class UserDaoImp implements UserDao{
		@Override
		public void test(){
			System.out.printlnI("Hello Wolrd!");
		}
	}
```

4. 配置注解扫描

```xml
	<!-- Spring 的注解开发: 组件扫描(类上注解:可以直接使用属性注入的注解) -->
	<!-- 哪个包下的类需要使用注解 -->
	<context:component-scan base-package="com.study.demo01" />
```

5. 在相关的类上添加注解

```java
	// 也能省略value 相当于<bean id="userDao" class="com.study.demo01.UserDaoImp" />
	@Component(value="userDao") 
	public class UserDaoImp implements UserDao{
		@Override
		public void test(){
			System.out.printlnI("Hello Wolrd!");
		}
	}
```

## 注解方式设置属性的值

**可以没有set方法, 若有set方法,将属性注解放到set方法上,没有的话放到属性上**

1. @Value: 用于注入普通类型

   ```java
   	@Value("小明")
   	private String name;
   ```

## Spring 的 Bean 管理中常用的注解

1.  @Component组件作用在类上

* Spring 中提供三个衍生注解:(目前来讲功能一致)

	* @Controller : WEB 层
	* @Service : 业务层
	* @Repository : 持久层

2. 属性注入的注解 使用注解注入的方式 可以不用提供 set 方法
   - @Value : 用于普通属性
   - @Autowired : 自动装配:类的属性
     - 默认按类型进行装配
     - 按名称注入:
       - @Qualifier : 强制使用名称注入
     - @Resource 相当于:
       - @Autowired 和 @Qualifier 一起使用
3. Bean 的作用范围的注解

- @Scope
  - singleton :  单例
  - prototype : 多例

## Bean的生命周期的配置

- @PostConstruct : 相当于init-method
- @PreDestroy : 相当于destroy-method

## Spring 的 Bean 管理的方式的比较

![注解和XML](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E6%B3%A8%E8%A7%A3%E5%92%8CXML.png)

- XML 和注解

  - XML  : 结构清晰
  - 注解  : 开发方便 属性注入

- 实 际开发中还有一种 XML 和注解整合开发,但是用的比较少

  - Bean 有 XML 配置 但是使用的属性使用注解注入

  - **必须在配置文件上加入:**

    ```xml
    	<!-- 无扫描,使用属性注入的注解 -- >
    	<context:annotation-config />
    ```

- 使用场景

  - XML : 使用任何场景
    - 结构清晰
  - 注解 : 类是自己提供的,因为你不能改源码
    - 开发方便快速
    - 纯注解(SSH)




