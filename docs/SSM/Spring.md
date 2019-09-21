## Spring入门

### IOC（Inversion of Control）(控制反转)

将对象的创建权反转给Spring。

没采用控制反转的缺点：

	1. 接口与实现类有耦合。
	2. 接口 引用 = new 实现类1, 若想换实现类2了，就得必须改源代码，所以切换底层实现类很方便。

* 问题：如果底层的实现切换了，需要修改源代码，能不能不修改程序源代码对程序进行扩展 ？

![aa](https://github.com/ImmortalCountry/JavaStudy/blob/master/docs/resource/aa.png)

如 MySQL换Oracle

### 下一步

**想要用Spring需要一个配置文件，文件名叫什么都行，默认applicationContext.xml**

1. 引用约束

2. Spring简单例子

```java
	// <!-- Spring入门 -->
    
    // 配置文件 <bean id="userDao" class="com.itcast.spring.UserDaoImp"></bean>

    // 创建工厂
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	UserDao userDao = (UserDao) applicationContext.getBean("userDao");
	userDao.save();

```

**将自己写的类配置到applicationContext中，然后通过id调用类。**

### DI

>IOC的别名：依赖注入(DI)
>
>2004年，Martin Fowler探讨了同一个问题，既然IOC是控制反转，那么到底是“哪些方面的控制被反转了呢？”，经过详细地分析和论证后，他得出了答案：“获得依赖对象的过程被反转了”。控制被反转之后，获得依赖对象的过程由自身管理变为了由IOC容器主动注入。于是，他给“控制反转”取了一个更合适的名字叫做“依赖注入（Dependency Injection）”。他的这个答案，实际上给出了实现IOC的方法：注入。所谓依赖注入，就是由IOC容器在运行期间，动态地将某种依赖关系注入到对象之中。
>
>所以，依赖注入(DI)和控制反转(IOC)是从不同的角度的描述的同一件事情，就是指通过引入IOC容器，利用依赖关系注入的方式，实现对象之间的解耦。
>
>我们举一个生活中的例子，来帮助理解依赖注入的过程。大家对USB接口和USB设备应该都很熟悉吧，USB为我们使用电脑提供了很大的方便，现在有很多的外部设备都支持USB接口。
>现在，我们利用电脑主机和USB接口来实现一个任务：从外部USB设备读取一个文件。
>电脑主机读取文件的时候，它一点也不会关心USB接口上连接的是什么外部设备，而且它确实也无须知道。它的任务就是读取USB接口，挂接的外部设备只要符合USB接口标准即可。所以，如果我给电脑主机连接上一个U盘，那么主机就从U盘上读取文件；如果我给电脑主机连接上一个外置硬盘，那么电脑主机就从外置硬盘上读取文件。挂接外部设备的权力由我作主，即控制权归我，至于USB接口挂接的是什么设备，电脑主机是决定不了，它只能被动的接受。电脑主机需要外部设备的时候，根本不用它告诉我，我就会主动帮它挂上它想要的外部设备，你看我的服务是多么的到位。这就是我们生活中常见的一个依赖注入的例子。在这个过程中，我就起到了IOC容器的作用。
>
>通过这个例子,依赖注入的思路已经非常清楚：当电脑主机读取文件的时候，我就把它所要依赖的外部设备，帮他挂接上。整个外部设备注入的过程和一个被依赖的对象在系统运行时被注入另外一个对象内部的过程完全一样。
>
>我们把依赖注入应用到软件系统中，再来描述一下这个过程：
>
>对象A依赖于对象B,当对象 A需要用到对象B的时候，IOC容器就会立即创建一个对象B送给对象A。IOC容器就是一个对象制造工厂，你需要什么，它会给你送去，你直接使用就行了，而再也不用去关心你所用的东西是如何制成的，也不用关心最后是怎么被销毁的，这一切全部由IOC容器包办。
>
>在传统的实现中，由程序内部代码来控制组件之间的关系。我们经常使用new关键字来实现两个组件之间关系的组合，这种实现方式会造成组件之间耦合。IOC很好地解决了该问题，它将实现组件间关系从程序内部提到外部容器，也就是说由容器在运行期将组件间的某种依赖关系动态注入组件中。
>

**依赖注入，前提必须有IOC的环境，Spring管理这个类的时候将类的依赖的属性注入（设置）进来。**

*面向对象：依赖、继承、聚合*

* 假如UserDaoImp中有一个成员变量String name，如何在生成类时给它赋值 ？
	
	**必须设置set方法**
	
	1. 配置文件

		```
            <bean id="userDao" class="com.itcast.spring.UserDaoImp">
            <property name="name" value="赋值" />
            </bean>
		```
	2. 直接用工厂获得类

		`Obejct obj = applicationContext.getBean("id");`

* IOC为我们带来了什么好处

>我们还是从USB的例子说起，使用USB外部设备比使用内置硬盘，到底带来什么好处？
>
第一、USB设备作为电脑主机的外部设备，在插入主机之前，与电脑主机没有任何的关系，只有被我们连接在一起之后，两者才发生联系，具有相关性。所以，无论两者中的任何一方出现什么的问题，都不会影响另一方的运行。这种特性体现在软件工程中，就是可维护性比较好，非常便于进行单元测试，便于调试程序和诊断故障。代码中的每一个Class都可以单独测试，彼此之间互不影响，只要保证自身的功能无误即可，这就是组件之间低耦合或者无耦合带来的好处。
>
第二、USB设备和电脑主机的之间无关性，还带来了另外一个好处，生产USB设备的厂商和生产电脑主机的厂商完全可以是互不相干的人，各干各事，他们之间唯一需要遵守的就是USB接口标准。这种特性体现在软件开发过程中，好处可是太大了。每个开发团队的成员都只需要关心实现自身的业务逻辑，完全不用去关心其它的人工作进展，因为你的任务跟别人没有任何关系，你的任务可以单独测试，你的任务也不用依赖于别人的组件，再也不用扯不清责任了。所以，在一个大中型项目中，团队成员分工明确、责任明晰，很容易将一个大的任务划分为细小的任务，开发效率和产品质量必将得到大幅度的提高。
>
第三、同一个USB外部设备可以插接到任何支持USB的设备，可以插接到电脑主机，也可以插接到DV机，USB外部设备可以被反复利用。在软件工程中，这种特性就是可复用性好，我们可以把具有普遍性的常用组件独立出来，反复利用到项目中的其它部分，或者是其它项目，当然这也是面向对象的基本特征。显然，IOC不仅更好地贯彻了这个原则，提高了模块的可复用性。符合接口标准的实现，都可以插接到支持此标准的模块中。
第四、同USB外部设备一样，模块具有热插拔特性。IOC生成对象的方式转为外置方式，也就是把对象生成放在配置文件里进行定义，这样，当我们更换一个实现子类将会变得很简单，只要修改配置文件就可以了，完全具有热插拨的特性。
以上几点好处，难道还不足以打动我们，让我们在项目开发过程中使用IOC框架吗？

* IOC容器的技术剖析

>IOC中最基本的技术就是“反射(Reflection)”编程，目前.Net C#、Java和PHP5等语言均支持，其中PHP5的技术书籍中，有时候也被翻译成“映射”。**有关反射的概念和用法，大家应该都很清楚，通俗来讲就是根据给出的类名（字符串方式）来动态地生成对象。** 
>
>这种编程方式可以让对象在生成时才决定到底是哪一种对象。反射的应用是很广泛的，很多的成熟的框架，比如象Java中的Hibernate、Spring框架，.Net中 NHibernate、Spring.Net框架都是把“反射”做为最基本的技术手段。
>
反射技术其实很早就出现了，但一直被忽略，没有被进一步的利用。当时的反射编程方式相对于正常的对象生成方式要慢至少得10倍。现在的反射技术经过改良优化，已经非常成熟，反射方式生成对象和通常对象生成方式，速度已经相差不大了，大约为1-2倍的差距。

**我们可以把IOC容器的工作模式看做是工厂模式的升华，可以把IOC容器看作是一个工厂，这个工厂里要生产的对象都在配置文件中给出定义，然后利用编程语言的的反射编程，根据配置文件中给出的类名生成相应的对象。从实现来看，IOC是把以前在工厂方法里写死的对象生成代码，改变为由配置文件来定义，也就是把工厂和对象生成这两者独立分隔开来，目的就是提高灵活性和可维护性。**

* 使用IOC框架应该注意什么

>使用IOC框架产品能够给我们的开发过程带来很大的好处，但是也要充分认识引入IOC框架的缺点，做到心中有数，杜绝滥用框架。
>
第一、软件系统中由于引入了第三方IOC容器，生成对象的步骤变得有些复杂，本来是两者之间的事情，又凭空多出一道手续，所以，我们在刚开始使用IOC框架的时候，会感觉系统变得不太直观。所以，引入了一个全新的框架，就会增加团队成员学习和认识的培训成本，并且在以后的运行维护中，还得让新加入者具备同样的知识体系。
>
第二、由于IOC容器生成对象是通过反射方式，在运行效率上有一定的损耗。如果你要追求运行效率的话，就必须对此进行权衡。
>
第三、具体到IOC框架产品(比如：Spring)来讲，需要进行大量的配制工作，比较繁琐，对于一些小的项目而言，客观上也可能加大一些工作成本。
>
第四、IOC框架产品本身的成熟度需要进行评估，如果引入一个不成熟的IOC框架产品，那么会影响到整个项目，所以这也是一个隐性的风险。
>
我们大体可以得出这样的结论：一些工作量不大的项目或者产品，不太适合使用IOC框架产品。另外，如果团队成员的知识能力欠缺，对于IOC框架产品缺乏深入的理解，也不要贸然引入。最后，特别强调运行效率的项目或者产品，也不太适合引入IOC框架产品，象WEB2.0网站就是这种情况。






### Spring 的工厂类

#### 结构图

![工厂类结构图](..\resource\工厂类结构图.png)

#### 老版本的工厂类：BeanFactory

* BeanFactory：调用getBean的时候，才会生成类的实例。

#### 新版本的工厂类：ApplicationContext

* 加载配置文件的时候，就会将Spring管理的类都实例化。
* 有两个实现类
	
	* ClassPathXmlApplicationContext：加载类路径下的配置文件
	* FileSystemXmlApplicationContext：加载文件系统下的配置文件

Spring 提供了以下两种不同类型的容器。

| 序号 | 容器 & 描述                                                  |
| ---- | ------------------------------------------------------------ |
| 1    | [Spring BeanFactory 容器](https://www.w3cschool.cn/wkspring/j3181mm3.html)它是最简单的容器，给 DI 提供了基本的支持，它用 org.springframework.beans.factory.BeanFactory 接口来定义。BeanFactory 或者相关的接口，如 BeanFactoryAware，InitializingBean，DisposableBean，在 Spring 中仍然存在具有大量的与 Spring 整合的第三方框架的反向兼容性的目的。 |
| 2    | [Spring ApplicationContext 容器](https://www.w3cschool.cn/wkspring/yqdx1mm5.html)该容器添加了更多的企业特定的功能，例如从一个属性文件中解析文本信息的能力，发布应用程序事件给感兴趣的事件监听器的能力。该容器是由 org.springframework.context.ApplicationContext 接口定义。 |

ApplicationContext 容器包括 BeanFactory 容器的所有功能，所以通常建议超过 BeanFactory。BeanFactory 仍然可以用于轻量级的应用程序，如移动设备或基于 applet 的应用程序，其中它的数据量和速度是显著。

### XML的配置

1. 首先要引入约束
2. bean

#### Bean 定义

被称作 bean 的对象是构成应用程序的支柱也是由 Spring IoC 容器管理的。bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。这些 bean 是由用容器提供的配置元数据创建的，例如，已经在先前章节看到的，在 XML 的表单中的 定义。

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
| properties               | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| autowiring mode          | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| lazy-initialization mode | 延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例。 |
| initialization 方法      | 在 bean 的所有必需的属性被容器设置之后，调用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |
| destruction 方法         | 当包含该 bean 的容器被销毁时，使用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |

>标签的id和name的配置:
>*  id: 使用了约束中的唯一约束。里面不能出现特殊字符的。
>*  name: 没有使用约束中的唯一约束（理论上可以出现重复的，但是实际开发不能出现的）。里面可以出现特殊字符。
>什么时候用
>* Spring和Struts1框架整合的时候
>* `<bean name="/user" class=""/>`

#### Bean的生命周期

`<bean id="userDao" class="com.itcast.spring.UserDaoImp" init-method="类中自定义方法" destroy-method="类中自定义方法"></bean>`

* init-method :Bean被初始化的时候执行的方法
* destroy-method :Bean被销毁的时候执行的方法（Bean是单例创建，工厂关闭）

#### Bean的作用范围的配置（重点）

* scope：Bean的作用范围

	* singleton ：**默认的，Spring会采用单例模式创建这个对象。**
	* prototype：**多例模式。（Struts2和Spring整合一定会用到**
	* request：应用在web项目中，Spring创建这个类以后，将这个类存入到request范围中。
	* session：应用在web项目中，Spring创建这个类以后，将这个类存入到session范围中。
	* globalsession：应用在web项目中，必须在porlet环境下使用。但是如果没有这种环境，相对于session。

		* porlet：子系统。比如登陆了百度，那么进入百度地图等就不需要登陆了（全局session）

### Spring的属性注入（DI）

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

* 通过引入p名称空间完成属性的注入：

`xmlns:p="xxxxxxxx"`

	* 写法：
		
		* 普通属性	p:属性名=”值”
		* 对象属性	p:属性名-ref=”值”

4. SpEL的属性注入（Spring3.0以后）

* 语法

	* #{SpEL}

```xml
	<!-- SpEL的属性注入 -->
	<bean id="carInfo" class="com.study.CarInfo">
	</bean>
	
	<bean id="car2" class="com.study.demo4.Car2">
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

1.  在加载配置文件的时候，加载多个

`ApplicationContext ac = new ClassPathXmlApplicationContext("a1,xml","a2.xml");`

2.  在一个配置文件中引入多个配置文件(如在a1.xml中)

`<import resource="a2.xml">`

### Spring整合web项目问题

每次请求都会创建Spring的工厂，浪费服务器资源

* 解决:

1. 在服务器启动的时候创建Spring工厂
2. 将工厂保存到ServletContext中
3. 每次获取工厂的时候从ServletContext中获取

* Spring已经给我们实现了

1. 引入spring_web.jar
2. 配置ContextLoaderListener
	* 默认加载的是/WEB-INF/applicationContext.xml
	* 配置修改加载文件的路径

3. 通过工具类获取工厂

***

## 注解IOC&AOP

### Spring IOC 注解开发入门

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

#### 注解方式设置属性的值

>可以没有set方法, 若有set方法,将属性注解放到set方法上,没有的话放到属性上.
>
1. @Value: 用于注入普通类型

	```java
		@Value("小明")
		private String name;
	```
	
#### Spring 的 Bean 管理的中常用的注解

1. @Component组件作用在类上

Spring 中提供三个衍生注解:(目前来讲功能一致)
	* @Controller : WEB 层
	* @Service : 业务层
	* @Repository : 持久层

2. 属性注入的注解 使用注解注入的方式 可以不用提供 set 方法

	* @Value : 用于普通属性
	* @Autowired : 自动装配:
	
		* 默认按类型进行装配
		* 按名称注入:
		
			* @Qualifier : 强制使用名称注入
		
		* @Resource 相当于:

			* @Autowired 和 @Qualifier 一起使用

3. Bean 的作用范围的注解

* @Scope
	* singleton :  单例
	* prototype : 多例

#### Bean的生命周期的配置

* @PostConstruct : 相当于init-method
* @PreDestroy : 相当于destroy-method

#### Spring 的 Bean 管理的方式的比较

![注解和XML](..\resource\注解和XML.png)

* XML 和注解

	* XML  : 结构清晰
	* 注解  : 开发方便 属性注入

* 实 际开发中还有一种 XML 和注解整合开发,但是用的比较少
	
	* Bean 有 XML 配置 但是使用的属性使用注解注入

	* **必须在配置文件上加入:**

		```xml
			<!-- 无扫描,使用属性注入的注解 -- >
			<context:annotation-config />
		```
	
* 使用场景

	* XML : 使用任何场景

		* 结构清晰

	* 注解 : 类不是自己提供的,因为你不能改源码

		* 开发方便快速
		* 纯注解(SSH)

### Spring AOP 注解开发入门

>Spring 框架的一个关键组件是面向方面的编程(AOP)框架。面向方面的编程需要把程序逻辑分解成不同的部分称为所谓的关注点。跨一个应用程序的多个点的功能被称为横切关注点，这些横切关注点在概念上独立于应用程序的业务逻辑。有各种各样的常见的很好的方面的例子，如日志记录、审计、声明式事务、安全性和缓存等。
>
在 OOP 中，关键单元模块度是类，而在 AOP 中单元模块度是方面。依赖注入帮助你对应用程序对象相互解耦和 AOP 可以帮助你从它们所影响的对象中对横切关注点解耦。AOP 是像编程语言的触发物，如 Perl，.NET，Java 或者其他。
>
Spring AOP 模块提供拦截器来拦截一个应用程序，例如，当执行一个方法时，你可以在方法执行之前或之后添加额外的功能。

**AOP是面向切面编程.AOP是OOP的扩展和延申, 解决OOP开发遇到的问题**

#### 应用场景

如有很多个Dao实现类实现了save方法,突然要求只有管理员能save,如何解决?

**传统情况下,纵向继承**

![纵向继承](..\resource\纵向继承.png)

**而AOP采用横向抽取**

![AOP](..\resource\AOP.png)




#### 为啥学AOP

对程序进行增强:在不改变源码的情况下
AOP可以进行权限进行校验,日志记录, 性能监控, 事务控制.

#### 底层实现

* 动态代理机制:
	* Spring 的 AOP 的底层用到两种代理机制
	* JDK 的动态代理 针对**实现了接口的类**产生代理
	* Cglib 的动态代理 针对没有实现接口的类产生代理, 应用的是底层的字节码增强的技术 生成当前类的子类对象

#### Spring 底层 AOP 的实现原理

##### JDK 动态代理

##### Cglib 动态代理

### Spring 的基于 AspectJ 的 AOP 开发

#### AOP 的开发中的相关术语

>* Joinpoint(连接点):所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法,因为spring只支持方法类型的连接点. 
>* Pointcut(切入点):所谓切入点是指我们要对哪些Joinpoint进行拦截的定义. 
>* Advice(通知/增强):所谓通知是指拦截到Joinpoint之后所要做的事情就是通知.通知分为前置通知,后置通知,异常通知,最终通知,环绕通知(切面要完成的功能) 
>* Introduction(引介):引介是一种特殊的通知在不修改类代码的前提下, Introduction可以在运行期为类动态地添加一些方法或Field. 
>* Target(目标对象):代理的目标对象 
>* Weaving(织入):是指把增强应用到目标对象来创建新的代理对象的过程. 
>* spring采用动态代理织入，而AspectJ采用编译期织入和类装在期织入 
>* Proxy（代理）:一个类被AOP织入增强后，就产生一个结果代理类 
>* Aspect(切面): 是切入点和通知（引介）的结合

| 项            | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| Aspect        | 一个模块具有一组提供横切需求的 APIs。例如，一个日志模块为了记录日志将被 AOP 方面调用。应用程序可以拥有任意数量的方面，这取决于需求。 |
| Join point    | 在你的应用程序中它代表一个点，你可以在插件 AOP 方面。你也能说，它是在实际的应用程序中，其中一个操作将使用 Spring AOP 框架。 |
| Advice        | 这是实际行动之前或之后执行的方法。这是在程序执行期间通过 Spring AOP 框架实际被调用的代码。 |
| Pointcut      | 这是一组一个或多个连接点，通知应该被执行。你可以使用表达式或模式指定切入点正如我们将在 AOP 的例子中看到的。 |
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

Spring 支持 **@AspectJ annotation style** 的方法和**基于模式**的方法来实现自定义方面。这两种方法已经在下面两个子节进行了详细解释。

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [XML Schema based](https://www.w3cschool.cn/wkspring/omps1mm6.html) | 方面是使用常规类以及基于配置的 XML 来实现的。                |
| [@AspectJ based](https://www.w3cschool.cn/wkspring/k4q21mm8.html) | @AspectJ 引用一种声明方面的风格作为带有 Java 5 注释的常规 Java 类注释。 |

![AOP相关术语](..\resource\AOP相关术语.png)

#### Spring 使用 AspectJ 进行 AOP 的开发 XML 的方式

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

5. 整合 Junit 单元测试

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

* 前置通知 ：在目标方法执行之前执行； 可获得切入点的信息

![获取切入点信息](..\resource\获取切入点信息.png)

* 后置通知 ：在目标方法执行之后执行；获得方法的返回值

![后置通知01](..\resource\后置通知01.png)

![后置通知02](..\resource\后置通知02.png)

* 环绕通知 ：在目标方法执行前和执行后执行；阻止目标方法执行

![环绕01](..\resource\环绕01.png)

![环绕02](..\resource\环绕02.png)

* 异常抛出通知：在目标方法执行出现 异常的时候 执行
* 最终通知 ：无论目标方法是否出现异常 最终通知都会 执行

7. 切入点表达式

```
	execution( 表达式)
    表达式
    [方法访问修 饰符 ] 方法返回值 包名 类名 方法名 方法的参数
    public * cn.itcast.spring.dao.*.*(..)
    1. cn.itcast.spring.
    2.  cn.itcast.spring.dao.
    3. cn.itcast.spring.
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

### 基于exction的函数完成

* 语法（基于execution的函数完成）

	* [访问修饰符]	方法返回值	包名.类名.方法名（参数）
	
    	* public void com.study.CustomerDao.save(..)
    	* 
