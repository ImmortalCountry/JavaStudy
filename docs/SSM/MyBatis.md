# MyBatis

## 介绍

>MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis 。2013年11月迁移到Github。
>
MyBatis是一个优秀的持久层框架，它对jdbc的操作数据库的过程进行封装，使开发者只需要关注 SQL 本身，而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码。
>
>Mybatis通过xml或注解的方式将要执行的各种statement（statement、preparedStatemnt、CallableStatement）配置起来，并通过java对象和statement中的sql进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射成java对象并返回。
>


## 使用jdbc编程

### JDBC编程步骤：

1. 加载数据库驱动
2. 创建并获取数据库链接
3. 创建jdbc statement对象
4. 设置sql语句
5. 设置sql语句中的参数(使用preparedStatement)
6. 通过statement执行sql并获取结果
7. 对sql执行结果进行解析处理
8. 释放资源(resultSet、preparedstatement、connection)

```java
		// 加载数据库驱动
		Class.forName("com.mysql.jdbc.Driver");

		// 通过驱动管理类获取数据库链接
		connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8", "root", "root");

		// 定义sql语句 ?表示占位符
		String sql = "select * from user where username = ?";

		// 获取预处理statement
		preparedStatement = connection.prepareStatement(sql);

		// 设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
		preparedStatement.setString(1, "王五");

		// 向数据库发出sql执行查询，查询出结果集
		resultSet = preparedStatement.executeQuery();

		// 遍历查询结果集
		while (resultSet.next()) {
			System.out.println(resultSet.getString("id") + "  " + resultSet.getString("username"));
		}

```

#### JDBC问题总结

1. 数据库连接创建、释放频繁造成系统资源浪费，从而影响系统性能。如果使用数据库连接池可解决此问题。
2. Sql语句在代码中硬编码，造成代码不易维护，实际应用中sql变化的可能较大，sql变动需要改变java代码。
3. 使用preparedStatement向占有位符号传参数存在硬编码，因为sql语句的where条件不一定，可能多也可能少，修改sql还要修改代码，系统不易维护。
4. 对结果集解析存在硬编码（查询列名）, sql变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成pojo对象解析比较方便。

## Mybatis 架构

![Mybatis架构](..\resource\Mybatis架构.png)

1. mybatis配置

SqlMapConfig.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。  
mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载

2. 通过mybatis环境等配置信息构造SqlSessionFactory即会话工厂
3. 由会话工厂创建sqlSession即会话，操作数据库需要通过sqlSession进行。
4. mybatis底层自定义了Executor执行器接口操作数据库，Executor接口有两个实现，一个是基本执行器、一个是缓存执行器。
5. Mapped Statement也是mybatis一个底层封装对象，它包装了mybatis配置信息及sql映射信息等。mapper.xml文件中一个sql对应一个Mapped Statement对象，sql的id即是Mapped statement的id。
6. Mapped Statement对sql执行输入参数进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql前将输入的java对象映射至sql中，输入参数映射就是jdbc编程中对preparedStatement设置参数。
7. Mapped Statement对sql执行输出结果进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql后将输出结果映射至java对象中，输出结果映射过程相当于jdbc编程中对结果的解析处理过程。

## 使用过程

1. SqlMapConfig.xml

* 在config下创建SqlMapConfig.xml

>SqlMapConfig.xml是mybatis核心配置文件，配置文件内容为数据源、事务管理。
>
2. 创建pojo

>pojo类作为mybatis进行sql映射使用，po类通常与数据库表对应
>
3. sql映射文件

>在config下的sqlmap目录下创建sql映射文件User.xml
>
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace：命名空间，用于隔离sql，还有一个很重要的作用，后面会讲 -->
<mapper namespace="test"></mapper>
```

4. 加载映射文件

>mybatis框架需要加载Mapper.xml映射文件
将users.xml添加在SqlMapConfig.xml，如下：

在SqlMapConfig.xml文件中添加

```xml
<mappers>
	<mapper resource="sqlmap/User.xml"><mapper>
</mappers>
```

5. 实现根据id查询用户

使用的sql:`SELECT * FROM user WHERE id = 1`

6. 映射文件

>在user.xml中添加select标签，编写sql：
>
```xml
<mapper namespace="test">

	<!-- id:statement的id 或者叫做sql的id-->
	<!-- parameterType:声明输入参数的类型 -->
	<!-- resultType:声明输出结果的类型，应该填写pojo的全路径 自动映射 -->
	<!-- #{}：输入参数的占位符，相当于jdbc的？ -->
	<select id="queryUserById" parameterType="int"
		resultType="com.syudy.mybatis.pojo.User">
		SELECT * FROM user WHERE id  = #{id}
	</select>

</mapper>

```
## 测试程序

* 测试程序步骤：

1. 创建SqlSessionFactoryBuilder对象
2. 加载SqlMapConfig.xml配置文件
3. 创建SqlSessionFactory对象
4. 创建SqlSession对象
5. 执行SqlSession对象执行查询，获取结果User
6. 打印结果
7. 释放资源

```java
	public class MybatisTest {
	private SqlSessionFactory sqlSessionFactory = null;

	@Before
	public void init() throws Exception {
		// 1. 创建SqlSessionFactoryBuilder对象
		SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();

		// 2. 加载SqlMapConfig.xml配置文件
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");

		// 3. 创建SqlSessionFactory对象
		this.sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
	}

	@Test
	public void testQueryUserById() throws Exception {
		// 4. 创建SqlSession对象
		SqlSession sqlSession = sqlSessionFactory.openSession();

		// 5. 执行SqlSession对象执行查询，获取结果User
		// 第一个参数是User.xml的statement的id，第二个参数是执行sql需要的参数；
		Object user = sqlSession.selectOne("queryUserById", 1);

		// 6. 打印结果
		System.out.println(user);

		// 7. 释放资源
		sqlSession.close();
	}
}

```

### 实现根据用户名模糊查询用户

查询sql：`SELECT * FROM user WHERE username LIKE '%王%'`

 #### 方法一

 ##### 映射文件

 * 在User.xml配置文件中添加如下内容：

 ```xml
 	<!-- 如果返回多个结果，mybatis会自动把返回的结果放在list容器中 -->
	<!-- resultType的配置和返回一个结果的配置一样 -->
	<select id="queryUserByUsername1" parameterType="string"
		resultType="com.study.mybatis.pojo.User">
		SELECT * FROM `user` WHERE username LIKE #{username}
	</select>

 ```

#### 测试程序

```java
	@Test
	public void testQueryUserByUsername1() throws Exception {
		// 4. 创建SqlSession对象
		SqlSession sqlSession = sqlSessionFactory.openSession();

		// 5. 执行SqlSession对象执行查询，获取结果User
		// 查询多条数据使用selectList方法
		List<Object> list = sqlSession.selectList("queryUserByUsername1", "%王%");

		// 6. 打印结果
		for (Object user : list) {
			System.out.println(user);
		}

		// 7. 释放资源
		sqlSession.close();
	}

```

### 方法二

#### 映射文件

 * 在User.xml配置文件中添加如下内容：

```xml
	<!-- 如果传入的参数是简单数据类型，${}里面必须写value -->
	<select id="queryUserByUsername2" parameterType="string"
		resultType="com.study.mybatis.pojo.User">
		SELECT * FROM `user` WHERE username LIKE '%${value}%'
	</select> 

```

**#{}与${}区别**
1. `#{}  select * from user where id = ? ` ： 占位符 | 预编译 | 防sql注入 == '五'
2. `${}  select * from user where username like '%${value}%' ` ： 占位符 | 非预编译 | 不防sql注入 == '%五%'
3. 若想用#来防注入,可以这么实现 : `select * from user where username like "%#{名字随便}"%"`

### 小结

* #{}与${}

```
一
1. #{}表示一个占位符号，通过#{}可以实现preparedStatement向占位符中设置值，自动进行java类型和jdbc类型转换。
2. #{}可以有效防止sql注入。 
3. #{}可以接收简单类型值或pojo属性值。 如果parameterType传输单个简单类型值，#{}括号中可以是value或其它名称。

二
1. ${}表示拼接sql串，通过${}可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换
2. ${}可以接收简单类型值或pojo属性值，如果parameterType传输单个简单类型值
3. ${}括号中只能是value。
```

* parameterType和resultType

```
1. parameterType：指定输入参数类型，mybatis通过ognl从输入对象中获取参数值拼接在sql中。
2. resultType：指定输出结果类型，mybatis将sql查询结果的一行记录数据映射为resultType指定类型的对象。如果有多条数据，则分别进行映射，并把对象放到容器List中

```

* selectOne和selectList

```
1. selectOne查询一条记录，如果使用selectOne查询多条记录则抛出异常：
org.apache.ibatis.exceptions.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 3
at org.apache.ibatis.session.defaults.DefaultSqlSession.selectOne(DefaultSqlSession.java:70)

2. selectList可以查询一条或多条记录。
```

## MySQL自增主键返回

* 查询id的sql

`SELECT LAST_INSERT_ID()`

通过修改User.xml映射文件，可以将mysql自增主键返回：
如下添加selectKey 标签

```xml
	<!-- 保存用户 -->
	<insert id="saveUser" parameterType="com.study.mybatis.pojo.User">
	<!-- selectKey 标签实现主键返回 -->
	<!-- keyColumn:主键对应的表中的哪一列 -->
	<!-- keyProperty：主键对应的pojo中的哪一个属性 -->
	<!-- order：设置在执行insert语句前执行查询id的sql，还是在执行insert语句之后执行查询id的sql -->
	<!-- resultType：设置返回的id的类型 -->
	<selectKey keyColumn="id" keyProperty="id" order="AFTER" resultType="int">
		SELECT LAST_INSERT_ID()
	</selectKey>
	INSERT INTO user (username,birthday,sex,address) VALUES (#{username},#{birthday},#{sex},#{address})
</insert>
```

**MySQL自增长:数据库先存数据,再生成自增长id,所以order="AFTER"
**AST_INSERT_ID():是mysql的函数，返回auto_increment自增列新记录id值。**

## MySQL使用uuid实现主键

* 需要增加通过select uuid()得到uuid值

```xml
	<!-- 保存用户 -->
	<insert id="saveUser" parameterType="com.study.mybatis.pojo.User">
	<!-- selectKey 标签实现主键返回 -->
	<!-- keyColumn:主键对应的表中的哪一列 -->
	<!-- keyProperty：主键对应的pojo中的哪一个属性 -->
	<!-- order：设置在执行insert语句前执行查询id的sql，还是在执行insert语句之后执行查询id的sql -->
	<!-- resultType：设置返回的id的类型 -->
	<selectKey keyColumn="id" keyProperty="id" order="BEFORE"
		resultType="string">
		SELECT LAST_INSERT_ID()
	</selectKey>
	INSERT INTO `user`
	(username,birthday,sex,address) VALUES
	(#{username},#{birthday},#{sex},#{address})
</insert>

```

**注意这里使用的order是“BEFORE”**

## 删除

**需要事物提交**

```java
	@Test
	public void testDeleteUserById() {
		// 4. 创建SqlSession对象
		SqlSession sqlSession = sqlSessionFactory.openSession();

		// 5. 执行SqlSession对象执行删除
		sqlSession.delete("deleteUserById", 48);

		// 需要进行事务提交
		sqlSession.commit();

		// 7. 释放资源
		sqlSession.close();
	}
```

## Mybatis解决jdbc编程的问题

1. 数据库连接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库连接池可解决此问题。
解决：在SqlMapConfig.xml中配置数据连接池，使用连接池管理数据库链接。
2. Sql语句写在代码中造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变java代码。
解决：将Sql语句配置在XXXXmapper.xml文件中与java代码分离。
3. 向sql语句传参数麻烦，因为sql语句的where条件不一定，可能多也可能少，占位符需要和参数一一对应。
解决：Mybatis自动将java对象映射至sql语句，通过statement中的parameterType定义输入参数的类型。
4. 对结果集解析麻烦，sql变化导致解析代码变化，且解析前需要遍历，如果能将数据库记录封装成pojo对象解析比较方便。
解决：Mybatis自动将sql执行结果映射至java对象，通过statement中的resultType定义输出结果的类型。

## mybatis与hibernate不同

1. Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。mybatis可以通过XML或注解方式灵活配置要运行的sql语句，并将java对象和sql语句映射生成最终执行的sql，最后将sql执行的结果再映射生成java对象。

2. Mybatis学习门槛低，简单易学，程序员直接编写原生态sql，可严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，例如互联网软件、企业运营类软件等，因为这类软件需求变化频繁，一但需求变化要求成果输出迅速。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件则需要自定义多套sql映射文件，工作量大。

3. Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件（例如需求固定的定制化软件）如果用hibernate开发可以节省很多代码，提高效率。但是Hibernate的学习门槛高，要精通门槛更高，而且怎么设计O/R映射，在性能和对象模型之间如何权衡，以及怎样用好Hibernate需要具有很强的经验和能力才行。

**总之，按照用户的需求在有限的资源环境下只要能做出维护性、扩展性良好的软件架构都是好架构，所以框架只有适合才是最好。**

## Dao开发方法

>使用MyBatis开发Dao，通常有两个方法，即原始Dao开发方法和Mapper动态代理开发方法。
>
### 需求

1. 使用MyBatis开发DAO实现以下的功能：
2. 根据用户id查询一个用户信息
3. 根据用户名称模糊查询用户信息列表
4. 添加用户信息

### SqlSession的使用范围

1. SqlSession 中封装了对数据库的操作，如：查询、插入、更新、删除等。
2. SqlSession 通过 SqlSessionFactory 创建。
3. SqlSessionFactory 是通过 SqlSessionFactoryBuilder 进行创建。

### SqlSessionFactoryBuilder

1. SqlSessionFactoryBuilder 用于创建 SqlSessionFacoty
2. SqlSessionFacoty 一旦创建完成就不需要 SqlSessionFactoryBuilder了, 因为SqlSession 是通过 SqlSessionFactory 创建的。所以可以将SqlSessionFactoryBuilder 当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

### SqlSessionFactory

1. SqlSessionFactory 是一个接口，接口中定义了 openSession 的不同重载方法
2. SqlSessionFactory 的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用
3. 通常以单例模式管理 SqlSessionFactory

### SqlSession

1. SqlSession 是一个面向用户的接口，sqlSession 中定义了数据库操作方法。
2. 每个线程都应该有它自己的 SqlSession 实例。
3. SqlSession 的实例不能共享使用，它也是线程不安全的。
4. 因此最佳的范围是请求或方法范围。绝对不能将 SqlSession 实例的引用放在一个类的静态字段或实例字段中。
5. 打开一个 SqlSession；使用完毕就要关闭它。通常把这个关闭操作放到 finally 块中以确保每次都能执行关闭。如下：

```java
	SqlSession session = sqlSessionFactory.openSession();
	try {
	 	// do work
	} finally {
		session.close();
}

```

### 原始Dao开发中存在以下问题：

* Dao方法体存在重复代码：通过SqlSessionFactory创建SqlSession，调用SqlSession的数据库操作方法
* 调用sqlSession的数据库操作方法需要指定statement的id，这里存在硬编码，不得于开发维护

### Mapper动态代理方式

#### 开发规范

Mapper接口开发方法只需要程序员编写Mapper接口（相当于Dao接口），由Mybatis框架根据接口定义创建接口的动态代理对象，代理对象的方法体同上边Dao接口实现类方法。

Mapper接口开发需要遵循以下规范：

1. Mapper.xml文件中的namespace与mapper接口的类路径相同。
2. Mapper接口方法名和Mapper.xml中定义的每个statement的id相同 
3. Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同
4. Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同

#### Mapper.xml(映射文件)

义mapper映射文件UserMapper.xml
将UserMapper.xml放在config下mapper目录下，效果如下：

![mapper](..\resource\mapper.png)

* UserMapper.xml配置文件内容：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace：命名空间，用于隔离sql -->
<!-- 还有一个很重要的作用，使用动态代理开发DAO，1. namespace必须和Mapper接口类路径一致 -->
<mapper namespace="com.study.mybatis.mapper.UserMapper">
	<!-- 根据用户id查询用户 -->
	<!-- 2. id必须和Mapper接口方法名一致 -->
	<!-- 3. parameterType必须和接口方法参数类型一致 -->
	<!-- 4. resultType必须和接口方法返回值类型一致 -->
	<select id="queryUserById" parameterType="int"
		resultType="cn.itcast.mybatis.pojo.User">
		select * from user where id = #{id}
	</select>

	<!-- 根据用户名查询用户 -->
	<select id="queryUserByUsername" parameterType="string"
		resultType="com.study.mybatis.pojo.User">
		select * from user where username like '%${value}%'
	</select>

	<!-- 保存用户 -->
	<insert id="saveUser" parameterType="com.study.mybatis.pojo.User">
		<selectKey keyProperty="id" keyColumn="id" order="AFTER"
			resultType="int">
			select last_insert_id()
		</selectKey>
		insert into user(username,birthday,sex,address) values
		(#{username},#{birthday},#{sex},#{address});
	</insert>

</mapper>

```

* UserMapper(接口文件)

![Mapper四大原则](..\resource\Mapper四大原则.png)

```java
	public interface UserMapper {
	/**
	 * 根据id查询
	 * 
	 * @param id
	 * @return
	 */
	User queryUserById(int id);

	/**
	 * 根据用户名查询用户
	 * 
	 * @param username
	 * @return
	 */
	List<User> queryUserByUsername(String username);

	/**
	 * 保存用户
	 * 
	 * @param user
	 */
	void saveUser(User user);
}
```

* serMapper.xml文件

修改SqlMapConfig.xml文件，添加以下所示的内容：
```xml
	<!-- 加载映射文件 -->
	<mappers>
		<mapper resource="sqlmap/User.xml" />
		<mapper resource="mapper/UserMapper.xml" />
	</mappers>
```

* 测试

```java
	public class UserMapperTest {
	private SqlSessionFactory sqlSessionFactory;

	@Before
	public void init() throws Exception {
		// 创建SqlSessionFactoryBuilder
		SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
		// 加载SqlMapConfig.xml配置文件
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
		// 创建SqlsessionFactory
		this.sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
	}

	@Test
	public void testQueryUserById() {
		// 获取sqlSession，和spring整合后由spring管理
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 从sqlSession中获取Mapper接口的代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		// 执行查询方法
		User user = userMapper.queryUserById(1);
		System.out.println(user);

		// 和spring整合后由spring管理
		sqlSession.close();
	}

	@Test
	public void testQueryUserByUsername() {
		// 获取sqlSession，和spring整合后由spring管理
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 从sqlSession中获取Mapper接口的代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		// 执行查询方法
		List<User> list = userMapper.queryUserByUsername("张");
		for (User user : list) {
			System.out.println(user);
		}

		// 和spring整合后由spring管理
		sqlSession.close();
	}

	@Test
	public void testSaveUser() {
		// 获取sqlSession，和spring整合后由spring管理
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 从sqlSession中获取Mapper接口的代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		// 创建保存对象
		User user = new User();
		user.setUsername("刘备");
		user.setBirthday(new Date());
		user.setSex("1");
		user.setAddress("蜀国");
		// 执行查询方法
		userMapper.saveUser(user);
		System.out.println(user);


		// 和spring整合后由spring管理
		sqlSession.commit();
		sqlSession.close();
	}
}

```

### 小结

* selectOne和selectList

  动态代理对象调用 sqlSession.selectOne() 和 sqlSession.selectList() 是根据 mapper 接口方法的返回值决定，如果返回 list 则调用 selectList 方法，如果返回单个对象则调用 selectOne 方法。

* namespace
mybatis 官方推荐使用 mapper 代理方法开发 mapper 接口，程序员不用编写 mapper 接口实现类，使用mapper 代理方法时，输入参数可以使用 pojo 包装对象或 map 对象，保证 dao 的通用性。

### SqlMapConfig.xml配置文件

#### 配置内容

* SqlMapConfig.xml中配置的内容和顺序如下

1. **properties（属性）**
2. settings（全局配置参数）
3. **typeAliases（类型别名）**
4. typeHandlers（类型处理器）
5. objectFactory（对象工厂）
6. plugins（插件）
7. environments（环境集合属性对象）
8. environment（环境子属性对象）
9. transactionManager（事务管理）
10. dataSource（数据源）
11. **mappers（映射器）**

##### properties（属性）

SqlMapConfig.xml可以引用java属性文件中的配置信息如下：

在config下定义db.properties文件，如下所示：

![properties](..\resource\properties.png)

db.properties配置文件内容如下：

```xml
	db.properties配置文件内容如下：
	jdbc.driver=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8
	jdbc.username=root
	jdbc.password=root/
```

SqlMapConfig.xml引用如下：

```xml
	<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 是用resource属性加载外部配置文件 -->
	<properties resource="db.properties">
		<!-- 在properties内部用property定义属性 -->
		<!-- 如果外部配置文件有该属性，则内部定义属性被外部属性覆盖 -->
		<property name="jdbc.username" value="root123" />
		<property name="jdbc.password" value="root123" />
	</properties>

	<!-- 和spring整合后 environments配置将废除 -->
	<environments default="development">
		<environment id="development">
			<!-- 使用jdbc事务管理 -->
			<transactionManager type="JDBC" />
			<!-- 数据库连接池 -->
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<!-- 加载映射文件 -->
	<mappers>
		<mapper resource="sqlmap/User.xml" />
		<mapper resource="mapper/UserMapper.xml" />
	</mappers>
</configuration>
```

**注意： MyBatis 将按照下面的顺序来加载属性：**

* 在 properties 元素体内定义的属性首先被读取。 
* 然后会读取properties 元素中resource或 url 加载的属性，它会覆盖已读取的同名属性。

##### typeAliases（类型别名）

###### mybatis支持别名：

| 别名       | 映射的类型 |
| ---------- | ---------- |
| _byte      | byte       |
| _long      | long       |
| _short     | short      |
| _int       | int        |
| _integer   | int        |
| _double    | double     |
| _float     | float      |
| _boolean   | boolean    |
| string     | String     |
| byte       | Byte       |
| long       | Long       |
| short      | Short      |
| int        | Integer    |
| integer    | Integer    |
| double     | Double     |
| float      | Float      |
| boolean    | Boolean    |
| date       | Date       |
| decimal    | BigDecimal |
| bigdecimal | BigDecimal |
| map        | Map        |

##### 自定义别名

* 在SqlMapConfig.xml中配置如下

```xml
	<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 是用resource属性加载外部配置文件 -->
	<properties resource="db.properties">
		<!-- 在properties内部用property定义属性 -->
		<property name="jdbc.username" value="root123" />
		<property name="jdbc.password" value="root123" />
	</properties>

	<typeAliases>
		<!-- 单个别名定义 -->
		<typeAlias alias="user" type="cn.itcast.mybatis.pojo.User" />
		<!-- 批量别名定义，扫描整个包下的类，别名为类名（大小写不敏感） -->
		<package name="cn.itcast.mybatis.pojo" />
		<package name="其它包" />
	</typeAliases>

	<!-- 和spring整合后 environments配置将废除 -->
	<environments default="development">
		<environment id="development">
			<!-- 使用jdbc事务管理 -->
			<transactionManager type="JDBC" />
			<!-- 数据库连接池 -->
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<!-- 加载映射文件 -->
	<mappers>
		<mapper resource="sqlmap/User.xml" />
		<mapper resource="mapper/UserMapper.xml" />
	</mappers>
</configuration>


```

* 在mapper.xml配置文件中，就可以使用设置的别名了,别名大小写不敏感

![别名](..\resource\别名.png)





##### mappers（映射器）

Mapper配置的几种方法：

1. `<mapper resource=" " />`

使用相对于类路径的资源（现在的使用方式）
如：`<mapper resource="sqlmap/User.xml" />`

2.` <mapper class=" " />`

使用mapper接口类路径
如：`<mapper class="cn.itcast.mybatis.mapper.UserMapper"/>`

**注意：此种方法要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中。**

3. `<package name=""/>`

注册指定包下的所有mapper接口
如：`<package name="cn.itcast.mybatis.mapper"/>`

**注意：此种方法要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中。**

## 进阶学习

### 输入映射和输出映射

#### parameterType(输入类型)

##### 传递简单类型

参考第一天内容。
使用#{}占位符，或者${}进行sql拼接。

##### 传递pojo对象

参考第一天的内容。
Mybatis使用ognl表达式解析对象字段的值，#{}或者${}括号中的值为pojo属性名称。

##### 传递pojo包装对象

开发中通过可以使用pojo传递查询条件。

查询条件可能是综合的查询条件，不仅包括用户查询条件还包括其它的查询条件（比如查询用户信息的时候，将用户购买商品信息也作为查询条件），这时可以使用包装对象传递输入参数。
包装对象：Pojo类中的一个属性是另外一个pojo。

需求：根据用户名模糊查询用户信息，查询条件放到QueryVo的user属性中。

###### 编写QueryVo

```java
	public class QueryVo {
	// 包含其他的pojo
	private User user;

	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}
}
```

###### Sql语句

* `SELECT * FROM user WHERE username LIKE '%张%'`

###### Mapper.xml文件

在UserMapper.xml中配置sql，如下图:

```xml
	<!-- 使用包装类型查询用户 -->
	<select id="queryUserByQueryVo" parameterType="queryVo" resultType="user">
	select * from user where username like '%${user.username}%'
	</select>
```

###### Mapper接口

在UserMapper接口中添加方法，如下图：

`List<User> queryUserByQueryVo(QueryVo queryVo);`

###### 测试方法

在UserMapeprTest增加测试方法，如下：

```java
	@Test
	public void testQueryUserByQueryVo() {
        // mybatis和spring整合，整合之后，交给spring管理
        SqlSession sqlSession = this.sqlSessionFactory.openSession();
        // 创建Mapper接口的动态代理对象，整合之后，交给spring管理
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

        // 使用userMapper执行查询，使用包装对象
        QueryVo queryVo = new QueryVo();
        // 设置user条件
        User user = new User();
        user.setUsername("张");
        // 设置到包装对象中
        queryVo.setUser(user);

        // 执行查询
        List<User> list = userMapper.queryUserByQueryVo(queryVo);
        for (User u : list) {
            System.out.println(u);
        }

        // mybatis和spring整合，整合之后，交给spring管理
        sqlSession.close();
}
```

#### resultType(输出类型)

##### 输出简单类型

需求:查询用户表数据条数

`SELECT count(*) FROM user`

###### Mapper.xml文件

在UserMapper.xml中配置sql，如下：

```xml
	<!-- 查询用户查询条数 -->
	<select id="queryUserCount" resultType="int">
		select count(*) from user 
	</select>
```

###### Mapper接口

在UserMapper添加方法，如下：

`int queryUserCount();`

###### 测试方法

在UserMapeprTest增加测试方法，如下：

```java
	@Test
	public void testQueryUserCount() {
        // mybatis和spring整合，整合之后，交给spring管理
        SqlSession sqlSession = this.sqlSessionFactory.openSession();
        // 创建Mapper接口的动态代理对象，整合之后，交给spring管理
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        // 使用userMapper执行查询用户数据条数
		int count = userMapper.queryUserCount();
		System.out.println(count);

		// mybatis和spring整合，整合之后，交给spring管理
		sqlSession.close();
    }
```

#### 输出pojo对象

参考第一天内容

#### 输出pojo列表

参考第一天内容。

#### resultMap

1. resultType 可以指定将查询结果映射为 pojo，但需要 pojo 的属性名和 sql 查询的列名一致方可映射成功。
2. 如果 sql 查询字段名和 pojo 的属性名不一致，可以通过 resultMap 将字段名和属性名作一个对应关系, resultMap 实质上还需要将查询结果映射到 pojo 对象中。
3. resultMap 可以实现将查询结果映射为复杂类型的 pojo，比如在查询结果映射对象中包括pojo 和 list 实现一对一查询和一对多查询。

#### 声明pojo对象

* Order对象

```java
	public class Order {
        // 订单id
        private int id;
        // 用户id
        private Integer userId;
        // 订单号
        private String number;
        // 订单创建时间
        private Date createtime;
        // 备注
        private String note;
        // get/set。。。
}
```

* Mapper.xml文件

创建OrderMapper.xml配置文件，如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace：命名空间，用于隔离sql，还有一个很重要的作用，Mapper动态代理开发的时候使用，需要指定Mapper的类路径 -->
<mapper namespace="com.study.mybatis.mapper.OrderMapper">
	<!-- 查询所有的订单数据 -->
	<select id="queryOrderAll" resultType="order">
		SELECT id, user_id,
		number,
		createtime, note FROM `order`
	</select>
</mapper>
```

* Mapper接口

编写接口如下

```java
public interface OrderMapper {
	/**
	 * 查询所有订单
	 * 
	 * @return
	 */
	List<Order> queryOrderAll();
}
```

* 测试方法

编写测试方法OrderMapperTest如下：

```java
	public class OrderMapperTest {
	private SqlSessionFactory sqlSessionFactory;

	@Before
	public void init() throws Exception {
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
		this.sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}

	@Test
	public void testQueryAll() {
		// 获取sqlSession
		SqlSession sqlSession = this.sqlSessionFactory.openSession();
		// 获取OrderMapper
		OrderMapper orderMapper = sqlSession.getMapper(OrderMapper.class);

		// 执行查询
		List<Order> list = orderMapper.queryOrderAll();
		for (Order order : list) {
			System.out.println(order);
		}
	}
}
```

发现userId为null
解决方案：使用resultMap

##### 使用resultMap

由于上边的mapper.xml中sql查询列(user_id)和Order类属性(userId)不一致，所以查询结果不能映射到pojo中。
需要定义resultMap，把orderResultMap将sql查询列(user_id)和Order类属性(userId)对应起来

改造OrderMapper.xml，如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace：命名空间，用于隔离sql，还有一个很重要的作用，Mapper动态代理开发的时候使用，需要指定Mapper的类路径 -->

<mapper namespace="com.study.mybatis.mapper.OrderMapper">

	<!-- resultMap最终还是要将结果映射到pojo上，type就是指定映射到哪一个pojo -->
	<!-- id：设置ResultMap的id -->
	<resultMap type="order" id="orderResultMap">
		<!-- 定义主键 ,非常重要。如果是多个字段,则定义多个id -->
		<!-- property：主键在pojo中的属性名 -->
		<!-- column：主键在数据库中的列名 -->
		<id property="id" column="id" />

		<!-- 定义普通属性 -->
		<result property="userId" column="user_id" />
		<result property="number" column="number" />
		<result property="createtime" column="createtime" />
		<result property="note" column="note" />
	</resultMap>

	<!-- 查询所有的订单数据 -->
	<select id="queryOrderAll" resultMap="orderResultMap">
		SELECT id, user_id,
		number,
		createtime, note FROM `order`
	</select>

</mapper>

```

## 动态sql

* 通过mybatis提供的各种标签方法实现动态拼接sql。

需求：根据性别和名字查询用户
查询sql：
`SELECT id,username,birthday,sex,address FROM user WHERE sex = 1 AND username LIKE '%张%'`

### If标签

#### Mapper.xml文件

UserMapper.xml配置sql，如下：

```xml
<!-- 根据条件查询用户 -->
<select id="queryUserByWhere" parameterType="user" resultType="user">
	SELECT id, username, birthday, sex, address FROM user
	WHERE sex = #{sex} AND username LIKE 
    '%${username}%'
</select>
```

#### Mapper接口

编写Mapper接口，如下：

`List<User> query UserByWhere(User user);`

#### 测试方法

在UserMapperTest添加测试方法，如下：

```java
@Test
public void testQueryUserByWhere() {
	// mybatis和spring整合，整合之后，交给spring管理
	SqlSession sqlSession = this.sqlSessionFactory.openSession();
	// 创建Mapper接口的动态代理对象，整合之后，交给spring管理
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

	// 使用userMapper执行根据条件查询用户
	User user = new User();
	user.setSex("1");
	user.setUsername("张");

	List<User> list = userMapper.queryUserByWhere(user);

	for (User u : list) {
		System.out.println(u);
	}

	// mybatis和spring整合，整合之后，交给spring管理
	sqlSession.close();
}
```



#### 效果

测试效果如下图：

![效果1](..\resource\效果1.png)

如果注释掉	user.setSex("1")，测试结果如下图：

![效果2](..\resource\效果2.png)

测试结果二很显然不合理。
按照之前所学的，要解决这个问题，需要编写多个sql，查询条件越多，需要编写的sql就更多了，显然这样是不靠谱的。

解决方案，使用动态sql的if标签

### 使用if标签

改造UserMapper.xml，如下：

```xml
<!-- 根据条件查询用户 -->
<select id="queryUserByWhere" parameterType="user" resultType="user">
	SELECT id, username, birthday, sex, address FROM user
	WHERE 1=1
	<if test="sex != null and sex != ''">
		AND sex = #{sex}
	</if>
	<if test="username != null and username != ''">
		AND username LIKE
		'%${username}%'
	</if>
</select>
```

注意字符串类型的数据需要要做不等于空字符串校验。

### Where标签

上面的sql还有where 1=1 这样的语句，很麻烦
可以使用where标签进行改造

改造UserMapper.xml，如下

```xml
<!-- 根据条件查询用户 -->
<select id="queryUserByWhere" parameterType="user" resultType="user">
	SELECT id, username, birthday, sex, address FROM `user`
<!-- where标签可以自动添加where，同时处理sql语句中第一个and关键字 -->
	<where>
		<if test="sex != null">
			AND sex = #{sex}
		</if>
		<if test="username != null and username != ''">
			AND username LIKE
			'%${username}%'
		</if>
	</where>
</select>
```

### Sql片段

Sql中可将重复的sql提取出来，使用时用include引用即可，最终达到sql重用的目的。

把上面例子中的id, username, birthday, sex, address提取出来，作为sql片段，如下：

```xml	
<!-- 根据条件查询用户 -->
<select id="queryUserByWhere" parameterType="user" resultType="user">
	<!-- SELECT id, username, birthday, sex, address FROM `user` -->
	<!-- 使用include标签加载sql片段；refid是sql片段id -->
	SELECT <include refid="userFields" /> FROM user
	<!-- where标签可以自动添加where关键字，同时处理sql语句中第一个and关键字 -->
	<where>
		<if test="sex != null">
			AND sex = #{sex}
		</if>
		<if test="username != null and username != ''">
			AND username LIKE
			'%${username}%'
		</if>
	</where>
</select>

<!-- 声明sql片段 -->
<sql id="userFields">
	id, username, birthday, sex, address
</sql>
```
如果要使用别的Mapper.xml配置的sql片段，可以在refid前面加上对应的Mapper.xml的namespace

例如下图

![Mapper配置sql片段](..\resource\Mapper配置sql片段.png)

### foreach标签

向sql传递数组或List，mybatis使用foreach解析，如下：

根据多个id查询用户信息
查询sql：
`SELECT * FROM user WHERE id IN (1,10,24)`

### 改造QueryVo

如下图在pojo中定义list属性ids存储多个用户id，并添加getter/setter方法

`private List<Integer> ids;`

### Mapper.xml文件

UserMapper.xml添加sql，如下

```xml
<!-- 根据ids查询用户 -->
<select id="queryUserByIds" parameterType="queryVo" resultType="user">
	SELECT * FROM user
	<where>
		<!-- foreach标签，进行遍历 -->
		<!-- collection：遍历的集合，这里是QueryVo的ids属性 -->
		<!-- item：遍历的项目，可以随便写，但要和后面的#{}里面要一致 -->
		<!-- open：在前面添加的sql片段 -->
		<!-- close：在结尾处添加的sql片段 -->
		<!-- separator：指定遍历的元素之间使用的分隔符 -->
		<foreach collection="ids" item="item" open="id IN (" close=")"
			separator=",">
			#{item}
		</foreach>
	</where>
</select>
```

测试方法如下图：

```java
@Test
public void testQueryUserByIds() {
	// mybatis和spring整合，整合之后，交给spring管理
	SqlSession sqlSession = this.sqlSessionFactory.openSession();
	// 创建Mapper接口的动态代理对象，整合之后，交给spring管理
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

	// 使用userMapper执行根据条件查询用户
	QueryVo queryVo = new QueryVo();
	List<Integer> ids = new ArrayList<>();
	ids.add(1);
	ids.add(10);
	ids.add(24);
	queryVo.setIds(ids);

	List<User> list = userMapper.queryUserByIds(queryVo);

	for (User u : list) {
		System.out.println(u);
	}

	// mybatis和spring整合，整合之后，交给spring管理
	sqlSession.close();
}
```

## 关联查询


### 商品订单数据模型

![订单数据模型](..\resource\订单数据模型.png)

### 一对一查询

需求：查询所有订单信息，关联查询下单用户信息。

注意：
因为一个订单信息只会是一个人下的订单，所以从查询订单信息出发关联查询用户信息为一对一查询。
如果从用户信息出发查询用户下的订单信息则为一对多查询，因为一个用户可以下多个订单。

* sql语句

```sql
	SELECT
	o.id,
	o.user_id userId,
	o.number,
	o.createtime,
	o.note,
	u.username,
	u.address
FROM
	order o
LEFT JOIN user u ON o.user_id = u.id

```

#### 方法一：使用resultType

使用 resultType，改造订单 pojo 类，此 pojo 类中包括了订单信息和用户信息
这样返回对象的时候，mybatis 自动把用户信息也注入进来了

#### 改造pojo类

OrderUser类继承Order类后OrderUser类包括了Order类的所有字段，只需要定义用户的信息字段即可，如下图：

```java
	public class OrderUser extends Order{
		private String username;
		private String address;
	}
	
```
#### Mapper.xml

在UserMapper.xml添加sql，如下

```xml
<!-- 查询订单，同时包含用户数据 -->
<select id="queryOrderUser" resultType="orderUser">
	SELECT
	o.id,
	o.user_id
	userId,
	o.number,
	o.createtime,
	o.note,
	u.username,
	u.address
	FROM
	order o
	LEFT JOIN user u ON o.user_id = u.id
</select>

```

#### Mapper接口

在UserMapper接口添加方法，如下图：

`List<OrderUser> queryOrderUser();`

#### 测试方法

在UserMapperTest添加测试方法，如下：

```java
@Test
public void testQueryOrderUser() {
	// mybatis和spring整合，整合之后，交给spring管理
	SqlSession sqlSession = this.sqlSessionFactory.openSession();
	// 创建Mapper接口的动态代理对象，整合之后，交给spring管理
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

	// 使用userMapper执行根据条件查询用户
	List<OrderUser> list = userMapper.queryOrderUser();

	for (OrderUser ou : list) {
		System.out.println(ou);
	}

	// mybatis和spring整合，整合之后，交给spring管理
	sqlSession.close();
}

```

### 小结

定义专门的 pojo 类作为输出类型，其中定义了 sql 查询结果集所有的字段。此方法较为简单，企业中使用普遍。

#### 方法二：使用resultMap

使用resultMap，定义专门的resultMap用于映射一对一查询结果。

#### 改造pojo类

在Order类中加入User属性，user属性中用于存储关联查询的用户信息，因为订单关联查询用户是一对一关系，所以这里使用单个User对象存储关联查询的用户信息。
改造Order如下图：

```java
	public class Order {
        // 订单id
        private int id;
        // 用户id
        private Integer userId;
        // 订单号
        private String number;
        // 订单创建时间
        private Date createtime;
        // 备注
        private String note;
        
        private User user;

```

#### Mapper.xml

这里resultMap指定orderUserResultMap，如下：

```xml
	
<resultMap type="order" id="orderUserResultMap">
	<id property="id" column="id" />
	<result property="userId" column="user_id" />
	<result property="number" column="number" />
	<result property="createtime" column="createtime" />
	<result property="note" column="note" />

	<!-- association ：配置一对一属性 -->
	<!-- property:order里面的User属性名 -->
	<!-- javaType:属性类型 -->
	<association property="user" javaType="user">
		<!-- id:声明主键，表示user_id是关联查询对象的唯一标识-->
		<id property="id" column="user_id" />
		<result property="username" column="username" />
		<result property="address" column="address" />
	</association>

</resultMap>

<!-- 一对一关联，查询订单，订单内部包含用户属性 -->
<select id="queryOrderUserResultMap" resultMap="orderUserResultMap">
	SELECT
	o.id,
	o.user_id,
	o.number,
	o.createtime,
	o.note,
	u.username,
	u.address
	FROM
	order o
	LEFT JOIN `user` u ON o.user_id = u.id
</select>

```

#### Mapper接口

`List<Order> queryOrderUserResultMap();`

#### 测试方法

在UserMapperTest增加测试方法，如下：

```java
@Test
public void testQueryOrderUserResultMap() {
	// mybatis和spring整合，整合之后，交给spring管理
	SqlSession sqlSession = this.sqlSessionFactory.openSession();
	// 创建Mapper接口的动态代理对象，整合之后，交给spring管理
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    // 使用userMapper执行根据条件查询用户
	List<Order> list = userMapper.queryOrderUserResultMap();

	for (Order o : list) {
		System.out.println(o);
	}

	// mybatis和spring整合，整合之后，交给spring管理
	sqlSession.close();
}
```

## 一对多查询

案例：查询所有用户信息及用户关联的订单信息。
用户信息和订单信息为一对多关系。

* sql语句：

```sql
	SELECT
	u.id,
	u.username,
	u.birthday,
	u.sex,
	u.address,
	o.id oid,
	o.number,
	o.createtime,
	o.note
FROM
	`user` u
LEFT JOIN `order` o ON u.id = o.user_id

```

### 修改pojo类

在User类中加入List<Order> orders属性,如下图：

```java
	private Integer id;
	private String username;// 用户姓名
	private String sex;// 性别
	private Date birthday;// 生日
	private String address;// 地址
	
	private List<Order> orders;
	
```

### Mapper.xml

在UserMapper.xml添加sql，如下：

```xml
<resultMap type="user" id="userOrderResultMap">
	<id property="id" column="id" />
	<result property="username" column="username" />
	<result property="birthday" column="birthday" />
	<result property="sex" column="sex" />
	<result property="address" column="address" />

	<!-- 配置一对多的关系 -->
	<collection property="orders" javaType="list" ofType="order">
		<!-- 配置主键，是关联Order的唯一标识 -->
		<id property="id" column="oid" />
		<result property="number" column="number" />
		<result property="createtime" column="createtime" />
		<result property="note" column="note" />
	</collection>
</resultMap>

<!-- 一对多关联，查询订单同时查询该用户下的订单 -->
<select id="queryUserOrder" resultMap="userOrderResultMap">
	SELECT
	u.id,
	u.username,
	u.birthday,
	u.sex,
	u.address,
	o.id oid,
	o.number,
	o.createtime,
	o.note
	FROM
	`user` u
	LEFT JOIN `order` o ON u.id = o.user_id
</select>

```

### Mapper接口

编写UserMapper接口，如下图：

`List<User> queryUserOrder();`

### 测试方法

在UserMapperTest增加测试方法，如下

```java
@Test
public void testQueryUserOrder() {
	// mybatis和spring整合，整合之后，交给spring管理
	SqlSession sqlSession = this.sqlSessionFactory.openSession();
	// 创建Mapper接口的动态代理对象，整合之后，交给spring管理
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

	// 使用userMapper执行根据条件查询用户
	List<User> list = userMapper.queryUserOrder();

	for (User u : list) {
		System.out.println(u);
	}

	// mybatis和spring整合，整合之后，交给spring管理
	sqlSession.close();
}

```

## Mybatis整合spring

### 整合思路

1. SqlSessionFactory对象应该放到spring容器中作为单例存在。
2. 传统dao的开发方式中，应该从spring容器中获得sqlsession对象。
3. Mapper代理形式中，应该从spring容器中直接获得mapper的代理对象。
4. 数据库的连接以及数据库连接池事务管理都交给spring容器来完成。

### 整合需要的jar包

1. spring的jar包
2. Mybatis的jar包
3. Spring+mybatis的整合包。
4. Mysql的数据库驱动jar包。
5. 数据库连接池的jar包。

### 加入配置文件

1. mybatisSpring的配置文件
2. 配置文件sqlmapConfig.xml

	* 数据库连接及连接池
	* 事务管理（暂时可以不配置）
	* sqlsessionFactory对象，配置到spring容器中
	* mapeer代理对象或者是dao实现类配置到spring容器中。

3. Dao的开发

两种dao的实现方式：

1. 原始dao的开发方式
2. 使用Mapper代理形式开发方式

	* 直接配置Mapper代理
	* 使用扫描包配置Mapper代理

需求：

1. 实现根据用户id查询
2. 实现根据用户名模糊查询
3. 添加用户

### 创建pojo

```java
public class User {
	private int id;
	private String username;// 用户姓名
	private String sex;// 性别
	private Date birthday;// 生日
	private String address;// 地址

get/set。。。
}

```

### 传统dao的开发方式

原始的DAO开发接口+实现类来完成

需要dao实现类需要继承SqlsessionDaoSupport类

### 实现Mapper.xml

编写User.xml配置文件，如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="test">
	<!-- 根据用户id查询 -->
	<select id="queryUserById" parameterType="int" resultType="user">
		select * from user where id = #{id}
	</select>

	<!-- 根据用户名模糊查询用户 -->
	<select id="queryUserByUsername" parameterType="string"
		resultType="user">
		select * from user where username like '%${value}%'
	</select>

	<!-- 添加用户 -->
	<insert id="saveUser" parameterType="user">
		<selectKey keyProperty="id" keyColumn="id" order="AFTER"
			resultType="int">
			select last_insert_id()
		</selectKey>
		insert into user
		(username,birthday,sex,address)
		values
		(#{username},#{birthday},#{sex},#{address})
	</insert>

</mapper>

```

### 加载Mapper.xml

在SqlMapConfig如下图进行配置:

```xml

	<mappers>
		<mapper resource="sqlmap/User.xml" />
	</mappers>
	
```

### 实现UserDao接口

```java
public interface UserDao {
	/**
	 * 根据id查询用户
	 * 
	 * @param id
	 * @return
	 */
	User queryUserById(int id);

	/**
	 * 根据用户名模糊查询用户列表
	 * 
	 * @param username
	 * @return
	 */
	List<User> queryUserByUsername(String username);

	/**
	 * 保存
	 * 
	 * @param user
	 */
	void saveUser(User user);

}

```

### 实现UserDaoImpl实现类

编写DAO实现类，实现类必须集成SqlSessionDaoSupport
SqlSessionDaoSupport提供getSqlSession()方法来获取SqlSession

```java
public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {
	@Override
	public User queryUserById(int id) {
		// 获取SqlSession
		SqlSession sqlSession = super.getSqlSession();

		// 使用SqlSession执行操作
		User user = sqlSession.selectOne("queryUserById", id);

		// 不要关闭sqlSession

		return user;
	}

	@Override
	public List<User> queryUserByUsername(String username) {
		// 获取SqlSession
		SqlSession sqlSession = super.getSqlSession();

		// 使用SqlSession执行操作
		List<User> list = sqlSession.selectList("queryUserByUsername", username);

		// 不要关闭sqlSession

		return list;
	}

	@Override
	public void saveUser(User user) {
		// 获取SqlSession
		SqlSession sqlSession = super.getSqlSession();

		// 使用SqlSession执行操作
		sqlSession.insert("saveUser", user);

		// 不用提交,事务由spring进行管理
		// 不要关闭sqlSession
	}
}

```

### 配置dao

把dao实现类配置到spring容器中，如下图

```xml
	<!-- Dao原始Dao -->
	<bean id="userDao" class="com.study.mybatis.dao.UserDaoImpl">
		<property name="sqlSessionFactory" ref="sqlSessionFactoryBean"/>
	</bean>
```

### 测试方法

```java
	public class UserDaoTest {
	private ApplicationContext context;

	@Before
	public void setUp() throws Exception {
		this.context = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
	}

	@Test
	public void testQueryUserById() {
		// 获取userDao
		UserDao userDao = this.context.getBean(UserDao.class);

		User user = userDao.queryUserById(1);
		System.out.println(user);
	}

	@Test
	public void testQueryUserByUsername() {
		// 获取userDao
		UserDao userDao = this.context.getBean(UserDao.class);

		List<User> list = userDao.queryUserByUsername("张");
		for (User user : list) {
			System.out.println(user);
		}
	}

	@Test
	public void testSaveUser() {
		// 获取userDao
		UserDao userDao = this.context.getBean(UserDao.class);

		User user = new User();
		user.setUsername("曹操");
		user.setSex("1");
		user.setBirthday(new Date());
		user.setAddress("三国");
		userDao.saveUser(user);
		System.out.println(user);
	}
}

```

## Mapper代理形式开发dao

### 实现Mapper.xml

编写UserMapper.xml配置文件，如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.study.mybatis.mapper.UserMapper">
	<!-- 根据用户id查询 -->
	<select id="queryUserById" parameterType="int" resultType="user">
		select * from user where id = #{id}
	</select>

	<!-- 根据用户名模糊查询用户 -->
	<select id="queryUserByUsername" parameterType="string"
		resultType="user">
		select * from user where username like '%${value}%'
	</select>

	<!-- 添加用户 -->
	<insert id="saveUser" parameterType="user">
		<selectKey keyProperty="id" keyColumn="id" order="AFTER"
			resultType="int">
			select last_insert_id()
		</selectKey>
		insert into user
		(username,birthday,sex,address) values
		(#{username},#{birthday},#{sex},#{address})
	</insert>
</mapper>

```

### 实现UserMapper接口

```java
public interface UserMapper {
	/**
	 * 根据用户id查询
	 * 
	 * @param id
	 * @return
	 */
	User queryUserById(int id);

	/**
	 * 根据用户名模糊查询用户
	 * 
	 * @param username
	 * @return
	 */
	List<User> queryUserByUsername(String username);

	/**
	 * 添加用户
	 * 
	 * @param user
	 */
	void saveUser(User user);
}

```

## 方式一 配置mapper代理

在applicationContext.xml添加配置
MapperFactoryBean也是属于mybatis-spring整合包

```xml
<!-- Mapper代理的方式开发方式一，配置Mapper代理对象 -->
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
	<!-- 配置Mapper接口 -->
	<property name="mapperInterface" value="com.study.mybatis.mapper.UserMapper" />
	<!-- 配置sqlSessionFactory -->
	<property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>

```

### 测试方法

```java
public class UserMapperTest {
	private ApplicationContext context;

	@Before
	public void setUp() throws Exception {
		this.context = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
	}

	@Test
	public void testQueryUserById() {
		// 获取Mapper
		UserMapper userMapper = this.context.getBean(UserMapper.class);

		User user = userMapper.queryUserById(1);
		System.out.println(user);
	}

	@Test
	public void testQueryUserByUsername() {
		// 获取Mapper
		UserMapper userMapper = this.context.getBean(UserMapper.class);

		List<User> list = userMapper.queryUserByUsername("张");

		for (User user : list) {
			System.out.println(user);
		}
	}
	@Test
	public void testSaveUser() {
		// 获取Mapper
		UserMapper userMapper = this.context.getBean(UserMapper.class);

		User user = new User();
		user.setUsername("曹操");
		user.setSex("1");
		user.setBirthday(new Date());
		user.setAddress("三国");

		userMapper.saveUser(user);
		System.out.println(user);
	}
}

```


