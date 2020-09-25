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



## 6. DI依赖注入

### 6.1 构造器注入

### 6.2 Set方式注入【重点】

* 依赖注入：Set注入
  * 依赖：bean对象的创建依赖于容器
  * 注入：bean对象的所有属性，有容器来注入

### 6.3 扩展方式注入

### 6.4 bean作用域

## 7 Bean的自动装配

* 自动装配是Spring满足bean依赖的一种方式
* Spring在上下文中自动寻找，并自动给bean装配属性！

在Spring中有三种装配方式

1. 在xml中显示配置
2. 在java中显示配置
3. 隐式的自动装配bean【重要！】

#### 7.1 测试

1.  环境搭建

#### 7.2 Byname自动装配

#### 7.4  使用注解来装配

@Autowired,@Resource

要使用注解须知：

1. 导入约束:context约束
2. 配置注解的支持: <context:annotation-config/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    <context:annotation-config/>
</beans>
```

3. 对应Java部分

```Java
public class Person {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```

==注解本身实现究竟是如何完成==

**@Autowired**

可以再属性上使用，也可以使用set方法使用

使用Autowired可以不用Set方法，

科普：

```Java
@Nullable 字段标记了标识字段可以为Null
```

如果@Autowired自动装配的环境复杂，自动装配无法通过一个注解完成,使用Qualifier配合使用

```xml
    <bean id="cat1" class="com.lian.pojo.Cat"/>
    <bean id="cat2" class="com.lian.pojo.Cat"/>
    <bean id="dog1" class="com.lian.pojo.Dog"/>
    <bean id="dog2" class="com.lian.pojo.Dog"/>
```

```Java
public class Person {
    @Autowired
    @Qualifier("cat1")
    private Cat cat;
    @Autowired
    @Qualifier("dog22")
    private Dog dog;
    private String name;
    }
```

@Resource注解

```Java
public class Person {
    @Resource("cat1")
    private Cat cat;
    @Autowired
    @Qualifier("dog22")
    private Dog dog;
    private String name;
    }
```

小结：

@Resource和@Autowired区别

* 都是用来自动装配的，都可以放在属性字段上。
* @Autowired 通过bytype方式实现
* @Resource通过byname方式实现，如果找不到名字就通过byType实现。如果都找不到就报错。

## 8 使用注解开发

1. bean

   * 导入Aop的包

   * 加入对注解的支持

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
             https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/context
             https://www.springframework.org/schema/context/spring-context.xsd">
         <context:annotation-config/>
     </beans>
     ```

   @Component加载类上，说明使用组件来管理此类如：

   ```java
   @Component
   public class User {
       public String name="lian";
   }
   
   ```

   

2. 属性如何注入

```java
@Component
public class User {
    @Value("lian")
    public String name;
}
```

3. 衍生的注解

@Component又几个衍生注解，再Web开发中，会按照mvc三层架构分层

* dao @Repository 功能和component一样
* service @service
* controller @Controller

这个四个注解功能都一样，都是代表将某个类注册到Spring中，装配Bean.

4. 自行装配置

```xml
@Autowired
@Resource
@Nullable 这个标识字段可以为null
```

5. 作用域

```java
@Component
@Scope("singleton")
public class User {
    @Value("lian")
    public String name;
}
```

6. 小结

   xml与注解：

   * xml更加万能，适用于任何场合，维护简单方便
   * 注解不是自己类使用不了，维护相对复杂

   最佳时间：

   	* xml管理bean
   	* 注解只负责完成属性的注入

## 9. 使用Javaconfig开发

建立JavaConfig

```java
//给Srping托管，注册到容器中，和之前的xml形式一样。
@Configuration
@ComponentScan("com.lian.pojo")
public class JavaConfig {
//    使用了Conponent所就可以不用使用下面的Bean填写
//    @Bean
//    public User getUser(){
//        return new User();
//    }
}

```

```Java
@Component
public class User {
    public String name;

    @Value("sunji")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}

```

测试类：获取JavaConfig容器

```java
public class MyTest {
    public static void main(String[] args) {
//        使用AnnotationConfigApplicationContxt方式获取容器，逻辑上和使用xml方式没有区别
        ApplicationContext context =  new AnnotationConfigApplicationContext(JavaConfig.class);
        User user = (User)context.getBean("user");
        System.out.println(user.toString());
    }
}
```

纯Java的配置，在pringBoot中见的较多，SSM(Spring,SpringMVC,MyBatis)

## 10 代理模式

> 面向对象的七大原则  https://blog.csdn.net/qq_41331645/article/details/81102015

为什么学习代理模式？这是SpringAOP的底层！【SpringAOP 和 SpringMVC】

代理模式的分类：

* 静态代理
* 动态代理

### 10.1 静态代理

角色分析：

* 抽象角色：一般会使用接口或者抽象类来解决
* 真实角色：被代理的角色
* 代理角色：代理真实角色，代理真是角色后，我们一般会做一些附属操作
* 客户：访问代理对象的人  

### 10.2 例子：

以数据库的增删改查为例子，正常的开发逻辑未先建立一下角色：

* service

```java
public interface Service {
    public void add();
    public void del();
    public void update();
    public void select();
}
```

* serviceImpl

```Java
public class ServiceImpl implements Service {
    public void add() {
        System.out.println("增加数据");
    }

    public void del() {
        System.out.println("删除数据");
    }

    public void update() {
        System.out.println("更新数据");
    }

    public void select() {
        System.out.println("查询数据");
    }
}
```

如果这个时候需要增加一个功能在Service操作前打出LOG,如果直接去修改ServiceImpl就破坏了现有的功能，为了避免这点就引入了代理

* serviceProxy

  ```Java
  public class ServiceProxy implements Service {
      public Service serviceImpl;
  
      public ServiceProxy(Service serviceImpl) {
          this.serviceImpl = serviceImpl;
      }
      
      public void add() {
          serviceImpl.add();
      }
  
      public void del() {
          this.log();
          serviceImpl.del();
      }
  
      public void update() {
          this.log();
          serviceImpl.update();
      }
  
      public void select() {
          this.log();
          serviceImpl.select();
      }
  
      public void log(){
          System.out.println("日志:");
      }
  }
  ```

这样可以在建立代理就可以自定义一些其他的操作附属于原始操作之上了，如加入了log操作

代理模式的好处：

* 可以使真实的角色只关注自己的具体操作，不用关注到业务层面的操作
* 实现了业务的分工
* 公共业务发证扩展，方便集中管理。

缺点：

* 一个真实角色会产生一个代理角色，代码量翻倍效率降低。

> 在我理解看来就是把业务细化，做到模块解耦。

### 10.3 动态代理

* 动态代理和静态代理角色一样
* 动态代理的代理类是动态生成的，不是直接写好的！（解决了静态代理的缺点）
* 动态代理分为两大类：基于接口的动态代理，基于类的动态代理。
  * 基于接口,JDK动态代理
  * 基于类：cglib
  * java字节码:javasist

需要了解两个类：Proxy(代理),InvocationHandler（调用处理程序）

创建InvocationHandler

```java
public class ServiceInvocationHandler implements InvocationHandler {
//    创建一个target接收真实角色
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

//  InvocationHandler handler = new MyInvocationHandler(...);
//  Foo f = (Foo) Proxy.newProxyInstance(Foo.class.getClassLoader(),
//        new Class<?>[] { Foo.class },
//        handler);

    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }
    /**
     *
     * @param proxy:  the proxy instance that the method was invoked on
     * @param method
     * @param args
     * @return an array of objects containing the values of the arguments passed in the method invocation on the proxy instance,
     * @throws Throwable
     */
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        System.out.println(proxy.getClass().getName());
        Object result = method.invoke(target,args);
        return result;
    }
    public void log(String msg){
        System.out.println("日志:"+msg);
    }
}

```

调用部分

```java
ServiceInvocationHandler handler =  new ServiceInvocationHandler();
handler.setTarget(service);
Service service2 = (Service)handler.getProxy();
service2.add();
```

动态代理的好处：

* 一个动态代理代理的是一个接口，一般对应一类业务
* 一个动态代理类可以代理多个类，只要实现了同一个接口

## 11 AOP

### 11.1 什么是AOP

AOP(Aspect Oriented Programming) 面向切面编程，通过预编译方法和运行期动态代理实现程序功能的统一维护的一种技术，AOP是OOP的延续，是软件开发的一个热点，也是Spring框架中的一个重要内容，是函数式变成的一种衍生范型，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各个部分之间的耦合度降低，提高程序的可重用性，提高开发的效率

![image-20200924175609159](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200924175609159.png)

### 11.2 Aop在Spring中的作用

提供声明式事务，允许用户自定义切面

* 横切面关注点：跨越应用程序多个模块的方法或功能。也就是，与我们业务逻辑无关系，但我们关注的部分，就是横切面关注点，如：日志，安全，缓存，事务等。
* 切面(ASPECT)：切面关注被模块化的特殊对象，即，他是一个类(如：Log)
* 通知(Advice)：切面必须要完成的工作，即，他是类中的一个方法*（如 Log中的方法）
* 目标(Target): 被通知的对象(如刚才10.2中的target)
* 代理(Proxy): 向目标通知后创建的对象(getProxy获取的对象)
* 切入点，
* 连接点