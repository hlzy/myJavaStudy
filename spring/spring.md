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

控制反转的过程：

**控制**：谁来控制对象的创建，传统的应用程序对象有程序控制创建，使用Spring后，对象由Spring创建

反转：程序本身不创建对象，变为接手对象

依赖注入: 使用Set方法进行注入

使用spring，彻底不用在去程序中改动，要实现不同的操作，只需要在xml配置文件中修改，IOC一句话总结：对象由Spring来创建，管理，装配。



### 3. HelloSpring

以第二节的系统为例进行spring相关工程配置

#### 3.1 编辑xml配置

resources 下编辑配置xml配置脚本，取名叫bean.xml(这个名字可以随便取)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id = "userDaoImpl" class="com.helian.dao.UserDaoImpl" />
    <bean id="userDaoMysqlImpl" class="com.helian.dao.UesrDaoMysqlImpl" />
    <bean id = "userService" class="com.helian.UserService.UserServiceImpl">
        <!--ref表示引用spring中创建的对象-->
        <property name="userDao" ref="userDaoImpl"></property>
    </bean>

</beans>
```

#### 3.2 调用程序如下：

```Java
public class MyTest {
    public static void main(String[] args) {
        //使用普通Java调用方式
//        UserService userService = new UserServiceImpl();
//        userService.setUserDao(new UesrDaoMysqlImpl());
//        userService.getUser();
        //使用Spring方式调用

        //获取spring的容器，本例子中是在xml中获取，可以通过ApplicationContext的引用中查到有很多种类的容器。
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //对象再spring中管理
        UserService hello = (UserService) context.getBean("userService");
        hello.getUser();
    }
}
```



## 4. IOC创建对象的方式

* 写在XML中的无论是否使用均被创造
* 默认采用无参创建对象

具体可以参考

> https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-instantiation

* 有参数的构造

```Java
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```



## 5. Spring配置

### 5.1 别名

```xml
<alias name="user" alias="myUser"/>
```

### 5.2 Bean配置

* name别名，可以用空格和，进行多个

### 5.3 Import配置

假设项目现在多个人开发，这三个人负责不同的类开发，不同类注册在不同的bean下，可以利用import将所有beans.xml合并一个总的applicationContxt.xml,(内容相同的会被自动合并)

```xml
<import resource="beans1.xml" />
<import resource="beans2.xml" />
<import resource="beans3.xml" />
```



## DI依赖注入

### 6.1 构造器注入

### 6.2 Set方式注入【重点】

* 依赖注入：Set注入
  * 依赖：bean对象的创建依赖于容器
  * 注入：bean对象的所有属性，有容器来注入

### 6.3 扩展方式注入



