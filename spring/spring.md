## 1. spring

### 1.1简介

Spring是一个轻量级控制反转(IoC)和面向切面(AOP)的容器框架。

* 2004年3月24日诞生，以interface21为框架为基础重新设计过来的([Rod Johnson]([https://baike.baidu.com/item/Rod%20Johnson/1423612?fr=aladdin](https://baike.baidu.com/item/Rod Johnson/1423612?fr=aladdin)))创始人

* spring理念：使现有技术更加容易，本身是一个大杂烩，整合现有技术框架

* SSH : struct2 + spring + hibernate

* SSM:SPringMvc + Spring + Mybatis

官网：https://spring.io/projects/spring-framework

官方下载地址：https://repo.spring.io/release/org/springframework/spring/

GIthub地址：https://github.com/spring-projects/spring-framework

maven:https://mvnrepository.com/artifact/org.springframework

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.8.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.8.RELEASE</version>
</dependency>

```

### 1.2 优点

* Spring是一个开源免费的容器！（框架）
* Spring是一个轻量级，非入侵式的框架！
  * 非入侵指的是引入不会对原项目产影响
* ***控制反转(IOC),面向切面变成(AOP)***
* 支持事务处理,对框架整合的支持(Java的框架都能整合)

== Spring是轻量级的控制反转(IOC)和面向切面片成(AOP)的框架 ==

### 1.3 组成

![image-20200915145002680](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200915145002680.png)

### 1.4 拓展

Spring官网介绍：现代化的Java开发，就是基于Spring的开发

![image-20200915145245960](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200915145245960.png)

* Spring Boot

  * 快速开发的脚手架
  * 基于SpringBoot可以快速开发单个微服务
  * 约定大于配置！

* Sprint Cloud

  * 基于SpringBoot实现的。（协调的）

  弊端：发展了久了，违背了原来的理论，（使现有技术变得简单）配置十分繁琐，人称“配置地狱”

  

## 2. IOC理论推导

以写一个简单的用户服务为例，需要以下4个结构

1. UserDao
2. UserDaoImpl
3. UserService
4. UserServiceImpl

如果用户需要修改需求如增加一个数据库实现UserMysqlDaoImpl若UerServiceImpl采用以下方法实现：

```java
public class UserServiceImpl implements UserService{
    //每次用户修改需求均需要做更改
    //private UserDao userDao = new UserDaoImpl();
    private UserDao userDao = new UserMysqlDaoImpl();
  
    public void getUser() {
        userDao.getUser();
    }
}
```

如果代码量庞大，则修改代价昂贵

使用set接口更改为以下实现

```Java
public class UserServiceImpl implements UserService{
    private UserDao userDao;
    //利用set实现用户选择
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void getUser() {
        userDao.getUser()
    }
}
```

* 原来从程序主动创建对象，控制权再程序员手上
* 更改为用户Set注入后，程序员不在使用具有主动性，变成了被动接收对象。

这种将主动权移交给客户的思想，降低了***系统耦合性***，程序员不用再管理对象的创建。程序员可以将精力投入到业务实现上。此为IOC(控制反转)的原型。



***控制反转是一种通过描述(XML或者注释)并通过第三方区生产或者获取特定对象的方式。再Spring中实现控制反转的是IoC容器，其实现方法是依赖注入(Dependency Injection ,DI)***

### 3. HelloSpring