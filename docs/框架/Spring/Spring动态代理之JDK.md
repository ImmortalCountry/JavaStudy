# Spring 动态代理之JDK

动态代理（以下称代理），利用Java的反射技术(Java Reflection)，在运行时创建一个实现某些给定接口的新类（也称“动态代理类”）及其实例（对象）

(Using Java Reflection to create dynamic implementations of interfaces at runtime)。

代理的是接口(Interfaces)，不是类(Class)，更不是抽象类。

* 代理对象是由JDK动态生成的，而不像静态代理方式写死代理对象和被代理类，不灵活。
* JDK动态代理基于拦截器和反射来实现。
* JDK代理是不需要第三方库支持的，只需要JDK环境就可以进行代理。

**更多关于代理模式内容请[点这里](docs/设计模式/代理模式.md)**

## 使用条件

1. 必须实现InvocationHandler接口（代理类实现、或者匿名内部类）；

2. 使用Proxy.newProxyInstance产生代理对象；

3. 被代理的对象必须要实现接口；

## 步骤

1. 通过实现InvocationHandler接口来自定义自己的InvocationHandler；
2. 通过Proxy.newProxyInstance获得动态代理类；
3. 传入参数
4. 返回代理类
5. 通过代理对象调用目标方法；

## 代码

### UserDao 接口

```java
public interface UserDao {
	public void save();
	public void delete();
	public void update();
	public void find();
}
```

### 实现类UserDaoImpl

```java
/**
 * 使用JDK对UserDao产生动态代理
 * @author 18846
 *
 */
public class JdkProxy implements InvocationHandler{
	// 将被增强的对象传递到代理中
	private UserDao userDao;
	
	public JdkProxy(UserDao userDao) {
		this.userDao = userDao;
	}
	
	/**
	 * 返回代理对象
	 * @return
	 */
	public UserDao createProxy() {
		UserDao userDaoProxy = (UserDao) Proxy.newProxyInstance(userDao.getClass().getClassLoader(), userDao.getClass().getInterfaces(), this);
		return userDaoProxy;
		
	}
	
	
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		// 判断方法名是不是save
		if ("save".equals(method.getName())) {
			// 增加
			System.out.println("权限校验");
			return method.invoke(userDao, args);
		}
		// invoke:调用
		return method.invoke(userDao, args);
	}
}
```

### 代理类

```java
/**
 * 使用JDK对UserDao产生动态代理
 * @author 18846
 *
 */
public class JdkProxy implements InvocationHandler{
	// 将被增强的对象传递到代理中
	private UserDao userDao;
	
	public JdkProxy(UserDao userDao) {
		this.userDao = userDao;
	}
	
	/**
	 * 返回代理对象
	 * @return
	 */
	public UserDao createProxy() {
		UserDao userDaoProxy = (UserDao) Proxy.newProxyInstance(userDao.getClass().getClassLoader(), userDao.getClass().getInterfaces(), this);
		return userDaoProxy;
		
	}
	
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		// 判断方法名是不是save
		if ("save".equals(method.getName())) {
			// 增加
			System.out.println("权限校验");
			return method.invoke(userDao, args);
		}
		// invoke:调用
		return method.invoke(userDao, args);
	}
}
```

#### Proxy.newProxyInstance()方法有三个参数：

1. 类加载器(Class Loader)

2. 需要实现的接口数组

3. InvocationHandler接口。所有动态代理类的方法调用，都会交由InvocationHandler接口实现类里的invoke()方法去处理。这是动态代理的关键所在。

#### invoke()方法同样有三个参数：

1. 动态代理类的引用，通常情况下不需要它。但可以使用getClass()方法，得到proxy的Class类从而取得实例的类信息，如方法列表，annotation等。

2. 方法对象的引用，代表被动态代理类调用的方法。从中可得到方法名，参数类型，返回类型等等

3. args对象数组，代表被调用方法的参数。注意基本类型(int,long)会被装箱成对象类型(Interger, Long)

### 测试

```java
public class ProxyJdkTest {

	@Test
	public void demo01() {
		UserDao userDao = new UserDaoImpl();
		
		// 创建代理
		UserDao proxy = new JdkProxy(userDao).createProxy();
		proxy.save();
		proxy.delete();
		proxy.update();
		proxy.find();
	}
}
	
```