# Spring 的工厂类

## 结构图

![工厂类结构图](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E5%B7%A5%E5%8E%82%E7%B1%BB%E7%BB%93%E6%9E%84%E5%9B%BE.png)


## 老版本的工厂类：BeanFactory

- **BeanFactory：调用getBean的时候，才会生成类的实例。**

## 新版本的工厂类：ApplicationContext

- **加载配置文件的时候，就会将Spring管理的类都实例化。**
- **有两个实现类**
  - **ClassPathXmlApplicationContext：加载类路径下的配置文件**
  - **FileSystemXmlApplicationContext：加载文件系统下的配置文件**

Spring 提供了以下两种不同类型的容器。

| 序号 | 容器 & 描述                                                  |
| ---- | ------------------------------------------------------------ |
| 1    | [Spring BeanFactory 容器](https://www.w3cschool.cn/wkspring/j3181mm3.html)它是最简单的容器，给 DI 提供了基本的支持，它用 org.springframework.beans.factory.BeanFactory 接口来定义。BeanFactory 或者相关的接口，如 BeanFactoryAware，InitializingBean，DisposableBean，在 Spring 中仍然存在具有大量的与 Spring 整合的第三方框架的反向兼容性的目的。 |
| 2    | [Spring ApplicationContext 容器](https://www.w3cschool.cn/wkspring/yqdx1mm5.html)该容器添加了更多的企业特定的功能，例如从一个属性文件中解析文本信息的能力，发布应用程序事件给感兴趣的事件监听器的能力。该容器是由 org.springframework.context.ApplicationContext 接口定义。 |

ApplicationContext 容器包括 BeanFactory 容器的所有功能，所以通常建议超过 BeanFactory。BeanFactory 仍然可以用于轻量级的应用程序，如移动设备或基于 applet 的应用程序，其中它的数据量和速度是显著。