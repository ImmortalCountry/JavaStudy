# Spring 的基于 `AspectJ` 的 `AOP` 开发

## 注解的入门

* 引入aop的包
* 引入context约束

	* <context:component-scan />
	
* 使用注解开发

	* **@Component**           **：定义Bean**
		* **@Controller**    **：WEB层**
		* **@Service**          **：Service层**
		* **@Repository**  **：DAO层**
	* **属性注入：**
		* **普通属性**        **：@Value**
		* **对象属性**        **：@Resource**
			* **@Autowired**   **：按类型注入属性，按名称@Qulifier**

## XML方式和注解方式比较

* **XML 方式**        **：适用性更广，结构更加清晰。**
* **注解方式**        **：适用类是自己定义，开发更方便。**

## **XML 和注解的整合开发**

* **XML 定义类**
* **注解属性注入**

# Spring的AOP的基于AspectJ的XML的开发

## AOP的概述

* AOP：面向切面编程，是OOP的扩展和延伸，是用来解决OOP遇到问题。

## Spring的AOP

* 底层的实现

	* JDK的动态代理
	* Cglib的动态代理

* AOP的相关术语

* 连接点：可以被拦截的点。
* 切入点：真正被拦截的点。
* 通知：增强方法
* 引介：类的增强
* 目标：被增强的对象
* 织入：将增强应用到目标的过程。
* 代理：织入增强后产生的对象
* 切面：切入点和通知的组合

## AOP的入门开发

* 引入jar包
* 编写目标类并配置
* 编写切面类并配置
* 进行aop的配置

```xml
<aop:config>

​         <aop:pointcut expression=”execution(表达式)” id=”pc1”/>

<aop:aspect >

​         <aop:before method=”” pointcut-ref=”pc1”/>

</aop:aspect>

</aop:config>
```

## 通知类型

* 前置通知 aop:before
* 后置通知 aop:after-returning
* 环绕通知 aop:around
* 异常抛出通知 aop:after-throwing
* 最终通知 aop:after
* 切入点表达式写法

### `AOP` 的开发中的相关术语

> - `Joinpoint`(连接点):所谓连接点是指那些被拦截到的点。在`spring`中,这些点指的是方法,因为 `spring` 只支持方法类型的连接点. 
> - `Pointcut`(切入点):所谓切入点是指我们要对哪些`Joinpoint`进行拦截的定义. 
> - Advice(通知/增强):所谓通知是指拦截到 `Joinpoint` 之后所要做的事情就是通知.通知分为前置通知,后置通知,异常通知,最终通知,环绕通知(切面要完成的功能) 
> - `Introduction` (引介):引介是一种特殊的通知在不修改类代码的前提下,  `Introduction` 可以在运行期为类动态地添加一些方法或Field. 
> - Target(目标对象):代理的目标对象 
> - Weaving(织入):是指把增强应用到目标对象来创建新的代理对象的过程. 
> - spring采用动态代理织入，而`AspectJ`采用编译期织入和类装在期织入 
> - Proxy（代理）:一个类被`AOP`织入增强后，就产生一个结果代理类 
> - Aspect(切面): 是切入点和通知（引介）的结合

| 项            | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| Aspect        | 一个模块具有一组提供横切需求的 APIs。例如，一个日志模块为了记录日志将被 AOP 方面调用。应用程序可以拥有任意数量的方面，这取决于需求。 |
| Join point    | 在你的应用程序中它代表一个点，你可以在插件 AOP 方面。你也能说，它是在实际的应用程序中，其中一个操作将使用 Spring AOP 框架。 |
| Advice        | 这是实际行动之前或之后执行的方法。这是在程序执行期间通过 Spring AOP 框架实际被调用的代码。 |
| `Pointcut`    | 这是一组一个或多个连接点，通知应该被执行。你可以使用表达式或模式指定切入点正如我们将在` AOP `的例子中看到的。 |
| Introduction  | 引用允许你添加新方法或属性到现有的类中。                     |
| Target object | 被一个或者多个方面所通知的对象，这个对象永远是一个被代理对象。也称为被通知对象。 |
| Weaving       | Weaving 把方面连接到其它的应用程序类型或者对象上，并创建一个被通知的对象。这些可以在编译时，类加载时和运行时完成。 |

## 通知的类型

Spring 方面可以使用下面提到的五种通知工作：

| 通知           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| 前置通知       | 在一个方法执行之前，执行通知。                               |
| 后置通知       | 在一个方法执行之后，不考虑其结果，执行通知。                 |
| 返回后通知     | 在一个方法执行之后，只有在方法成功完成时，才能执行通知。     |
| 抛出异常后通知 | 在一个方法执行之后，只有在方法退出抛出异常时，才能执行通知。 |
| 环绕通知       | 在建议方法调用之前和之后，执行通知。                         |

## 实现自定义方面

Spring 支持 **@`AspectJ` annotation style** 的方法和**基于模式**的方法来实现自定义方面。这两种方法已经在下面两个子节进行了详细解释。

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [XML Schema based](https://www.w3cschool.cn/wkspring/omps1mm6.html) | 方面是使用常规类以及基于配置的 XML 来实现的。                |
| [@AspectJ based](https://www.w3cschool.cn/wkspring/k4q21mm8.html) | @`AspectJ` 引用一种声明方面的风格作为带有 Java 5 注释的常规 Java 类注释。 |

![AOP相关术语](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/AOP%E7%9B%B8%E5%85%B3%E6%9C%AF%E8%AF%AD.png)

#### Spring 使用 `AspectJ` 进行 `AOP` 的开发 XML 的方式

1. 引入相应的 jar 包
2. 引入 Spring 的配置文件约束
3. 编写目标类

```java
	// 创建接口和类
    public interface OrderDa o {
        public void save();
        public void update();
        public void delete();
        public void find
    }
    
    public class OrderDaoImpl implements OrderDao {
    
    	@Override
    	public void save() {
    		System.out.print("保存订单")
    	}
    	
    	@Override
		public void update() {
			Syste m. out . 修改订单
		}
		
		@Override
		public void delete() {
			System. out . 删除订单
		}
		
		@Override
		public void find() {
			System. out . 查询订单
    	}
```

4. 目标类的配置

```xml
	<!--目标类-->
<bean> id ==" class =="cn.itcast. demo3.OrderDaoImpl" />
```

5. 整合 `Junit` 单元测试

```java
	
	// 引入 spring test.jar
	
    @RunWith ( SpringJunit4ClassRunner.class)
    @ContextConfiguration (("classpath:applicationContext.xml)
    public class SpringDemo3 {
        
    	@Resource ( name="orderDao")
		private OrderDao orderDao
            
    	@Test
    	public void demo1(){
            orderDao .save();
            orderDao .update();
            orderDao .delete();
            orderDao .find();
		}
	}
```

6. 通知类型

- 前置通知 ：在目标方法执行之前执行； 可获得切入点的信息
- ![获取切入点信息](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E8%8E%B7%E5%8F%96%E5%88%87%E5%85%A5%E7%82%B9%E4%BF%A1%E6%81%AF.png)
- 后置通知 ：在目标方法执行之后执行；获得方法的返回值

![后置通知01](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E5%90%8E%E7%BD%AE%E9%80%9A%E7%9F%A501.png)

![后置通知02](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E5%90%8E%E7%BD%AE%E9%80%9A%E7%9F%A502.png)

- 环绕通知 ：在目标方法执行前和执行后执行；阻止目标方法执行

![环绕01](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E7%8E%AF%E7%BB%9501.png)

![环绕02](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E7%8E%AF%E7%BB%9502.png)

- 异常抛出通知：在目标方法执行出现 异常的时候 执行
- 最终通知 ：无论目标方法是否出现异常 最终通知都会 执行

7. 切入点表达式

```
	execution( 表达式)
    表达式
    [方法访问修 饰符 ] 方法返回值 包名 类名 方法名 方法的参数
    public * cn.study.spring.dao.*.*(..)
    1. cn.study.spring.
    2.  cn.study.spring.dao.
    3. cn.istudy.spring.
```

8. 编写一个切面类

```
	public class MyAspectXml {
        // 前置增强
        public void before(){
        	System. out . 前置增强
    	}

	}
```

9. 配置完成增强

```xml
	<!-- 配置目标对象 : 被增强的对象 -->
	<bean id="productDao" class="com.study.productDaoImp" />
	
	<!-- 将切面类交给Spring管理 -->
	<bean id="myAspect" class="com.study.MyAspectXML" />
	
	<!-- 通过AOP的配置完成对目标类产生代理 --><!-- 配置目标对象 : 被增强的对象 -->
	<aop:config>
		<!-- 表达式配置哪些类的哪些方法需要进行增强 -->
		<aop:pointcut expression="execution(* com.study.productDaoImp.save(..))" id="pointcut1" />
		<!-- 配置切面 -->
		<aop:aspect ref="myAspect"> 
			<aop:before method="checkPri" point-ref="pointcut1" />
		</aop:aspect>
	</aop:config>
```

## 基于`exction`的函数完成

- 语法（基于execution的函数完成）
  - [访问修饰符]	方法返回值	包名.类名.方法名（参数）
    - `public void com.study.CustomerDao.save(..)`
    - `* *.*.*.*Dao.save(..)`
    - `* com.stduy.spring.CustomerDao+.save(.. )`// +代表当前类和其子类
    - `* com.stduy.spring..*.*(..)` // spring 包下的所有类所有方法。

