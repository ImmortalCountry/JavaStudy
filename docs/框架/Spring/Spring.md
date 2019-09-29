# Spring

## 介绍

## 为了降低开发Java开发复杂性，Spring采取以下4种关键策略

* 基于 POJO 的轻量级和最小侵入性编程
* 通过依赖注入和面向接口实现松耦合
* 基于切面和惯例进行声明式编程
* 通过切面和模板减少样板式代码

## 想要用Spring需要一个配置文件，文件名叫什么都行，默认applicationContext.xml

1. 引用约束
2. Spring简单例子

配置文件 : `<bean id="userDao" class="com.itcast.spring.UserDaoImp"></bean>`

```java
// 创建工厂
ApplicationContext applicationContext = new 

ClassPathXmlApplicationContext("applicationContext.xml");

UserDao userDao = (UserDao) applicationContext.getBean("userDao");

userDao.save();
```

**将自己写的类配置到applicationContext中，然后通过id调用类。**

### 假如UserDaoImp中有一个成员变量String name，如何在生成类时给它赋值 ？

**必须有set方法**

1. 配置文件

```xml
<bean id="userDao" class="com.itcast.spring.UserDaoImp">
    <property name="name" value="赋值" />
</bean>
```
2. 直接用工厂获得类

`Obejct obj = applicationContext.getBean("id");`












