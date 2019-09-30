# MyBatis进阶学习

## 输入映射和输出映射

### parameterType(输入类型)

#### 传递简单类型

参考第一天内容。

使用#{}占位符，或者${}进行sql拼接。

#### 传递pojo对象

参考第一天的内容。

Mybatis使用ognl表达式解析对象字段的值，#{}或者${}括号中的值为pojo属性名称。

#### 传递pojo包装对象

开发中通过可以使用pojo传递查询条件。

查询条件可能是综合的查询条件，不仅包括用户查询条件还包括其它的查询条件（比如查询用户信息的时候，将用户购买商品信息也作为查询条件），这时可以使用包装对象传递输入参数。
包装对象：Pojo类中的一个属性是另外一个pojo。

需求：根据用户名模糊查询用户信息，查询条件放到QueryVo的user属性中。

### 编写QueryVo

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

#### Sql语句

- `SELECT * FROM user WHERE username LIKE '%张%'`

#### Mapper.xml文件

在UserMapper.xml中配置sql，如下图:

```xml
	<!-- 使用包装类型查询用户 -->
	<select id="queryUserByQueryVo" parameterType="queryVo" resultType="user">
	select * from user where username like '%${user.username}%'
	</select>
```

#### Mapper接口

在UserMapper接口中添加方法，如下图：

`List<User> queryUserByQueryVo(QueryVo queryVo);`

#### 测试方法

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

### resultType(输出类型)

#### 输出简单类型

需求:查询用户表数据条数

`SELECT count(*) FROM user`

##### Mapper.xml文件

在UserMapper.xml中配置sql，如下：

```xml
	<!-- 查询用户查询条数 -->
	<select id="queryUserCount" resultType="int">
		select count(*) from user 
	</select>
```

##### Mapper接口

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

- Order对象

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

- Mapper.xml文件

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

- Mapper接口

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

- 测试方法

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

- 通过mybatis提供的各种标签方法实现动态拼接sql。

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

![效果1](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E6%95%88%E6%9E%9C1.png)

如果注释掉	user.setSex("1")，测试结果如下图：

![效果2](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E6%95%88%E6%9E%9C2.png)

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

![Mapper配置sql片段.png](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/Mapper%E9%85%8D%E7%BD%AEsql%E7%89%87%E6%AE%B5.png)

### foreach标签

向sql传递数组或List，MyBatis使用foreach解析，如下：

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

![订单数据模型](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E8%AE%A2%E5%8D%95%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B.png)

### 一对一查询

需求：查询所有订单信息，关联查询下单用户信息。

注意：
因为一个订单信息只会是一个人下的订单，所以从查询订单信息出发关联查询用户信息为一对一查询。
如果从用户信息出发查询用户下的订单信息则为一对多查询，因为一个用户可以下多个订单。

- sql语句

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
这样返回对象的时候，MyBatis 自动把用户信息也注入进来了

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

定义专门的 pojo 类作为输出类型，其中定义了 sql 查询结果集所有的字段。**此方法较为简单，企业中使用普遍。**

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

- sql语句：

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

