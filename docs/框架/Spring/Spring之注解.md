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