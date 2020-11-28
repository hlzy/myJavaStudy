## JavaWeb

## 1 基础概念

### 1.1 web 开发：

* web 网页

* 静态web

* 动态web

  * 技术栈 Servlet/Jsp,Asp,PHP

  在java中，动态web资源成为javaweb

### 1.2 web应用程序：

web应用程序，提供浏览器访问的程序

* html,这些web资源
* 统一的web资源会放在同一的文件夹下，web应用程序->Tomcat:服务器
  * html,css,js
  * jsp,servlet
  * java程序
  * jar包
  * 配置文件（Properties）

web程序编写完毕后，提供外界需要，统一服务器。

### 2. web服务器

#### 2.1 相关技术

ASP

PHP

jsp/servlet

* sun公司（被orcal收购）提出的BS架构
* 给予java
* 可以承载三高，高并发，高可用，高并发。

2.2  web服务器

* IIS

* Tomcat

Tomcat是Apache 软件基金会（Apache Software Foundation）的Jakarta 项目中的一个核心项目，由[Apache](https://baike.baidu.com/item/Apache/6265)、Sun 和其他一些公司及个人共同开发而成。由于有了Sun 的参与和支持，最新的Servlet 和JSP 规范总是能在Tomcat 中得到体现，Tomcat 5支持最新的Servlet 2.4 和JSP 2.0 规范。因为Tomcat 技术先进、性能稳定，而且**免费**，因而深受Java 爱好者的喜爱并得到了部分软件开发商的认可，成为目前比较流行的Web 应用服务器。

Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，属于轻量级应用[服务器](https://baike.baidu.com/item/服务器)，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。对于一个初学者来说，可以这样认为，当在一台机器上配置好Apache 服务器，可利用它响应[HTML](https://baike.baidu.com/item/HTML)（[标准通用标记语言](https://baike.baidu.com/item/标准通用标记语言/6805073)下的一个应用）页面的访问请求。实际上Tomcat是Apache 服务器的扩展，但运行时它是独立运行的，所以当你运行tomcat 时，它实际上作为一个与Apache 独立的进程单独运行的。

诀窍是，当配置正确时，Apache 为HTML页面服务，而Tomcat 实际上运行JSP 页面和Servlet。另外，Tomcat和[IIS](https://baike.baidu.com/item/IIS)等Web服务器一样，具有处理HTML页面的功能，另外它还是一个Servlet和JSP容器，独立的Servlet容器是Tomcat的默认模式。不过，Tomcat处理静态[HTML](https://baike.baidu.com/item/HTML)的能力不如Apache服务器。目前Tomcat最新版本为9.0.37**。**



## 2.Tomcat

官网：https://tomcat.apache.org/

启动startup.sh

关闭shutdown.sh

访问端口：localhost:8080

配置：java配置

cof/Server.xml 保存服务相关的核心配置，如主机名称（默认为localhost），端口等,默认网站存放appweb。

网站的目录：

```
--webapps:Tomct服务器web目录
	--lian：网站的目录名
		-web-inf
			-class
			-lib
			-web.xml 网站配置文件
		- index.html
		- static
			-css
				-style.css
			-js
			-img
```



## 3.http

### 3.1 什么是HTTP

HTTP(超文本传输协议)是一个简单的请求-响应协议，它通常运行在TCP之上

* 文本:html,字符串
* 超文本:图片，音乐，视频，定位，地图。。。
* 80端口

https:安全的

* 443

### 3.2 两个时代

* http1.0 : 客户端可以与web服务器连接后，只能获得一个web资源，断开连接
* http1.1：客户端可以与web服务器连接后，获得多个web资源。  

### 3.3 HTTP请求

* 客户端给服务器发送请求

百度

```
Request URL: https://dss0.bdstatic.com/5aV1bjqh_Q23odCf/static/superui/css/ubase_9376fdcf.css
Request Method: GET
Status Code: 200  (from disk cache)
Remote Address: 120.233.49.33:443
Referrer Policy: unsafe-url
```

```
Accept: text/html 告诉浏览器，它所支持的数据类型
Accept-Encoding: gzip, deflate, br  支持的编码格式GDK
Accept-Language: zh-CN,zh;q=0.9     告诉浏览器他的语言环境
Cache-Control: max-age=0  缓存控制
Connection: keep-alive    告诉浏览器，请求完成是断开还是保持连接
```

* 请求行中的请求方式:GET POST,HEAD,DELETE,PUT,TRACT..
  * get:请求携带参数少，会在URL显示
  * post:请求携带的参数没有限制。

### 3.4 HTTP响应

百度

```
Cache-Control: private 缓存控制
Connection: keep-alive 保持连接
Content-Encoding: gzip 
Content-Type: text/html;charset=utf-8 编码类型
```

1. 响应体
2. 响应状态码：

```
200 成功
3×× 请求重定向
	×　重定向：重定向到指定的位置去
4×× 找不到资源 404
5××：服务器代码错误 500 502网关错误 
```



## 4 Maven

### 4.1 为什么使用这个技术？

1. 在javaweb开发中，需要使用大量的jar包，我们手动导入。
2. maven协助自动导入配置。

Maven核心思想：约定大于配置。

* 有约束，不要去违反

Maven会规定号如何编写jAVA代码

官网：https://maven.apache.org/

### 4.2 下载和配置

配置环境变量

```bash
MAVEN_HOME=/home/helian/helian/apache-maven-3.6.3
M2_HOME=${MAVEN_HOME}/bin
PATH=${PATH}:${M2_HOME}
```

修改配置文件更改为阿里云镜像：

* 镜像：mirros

```
    <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
```

本地仓库：存储下载的jar包

```xml
 <localRepository>/home/helian/helian/apache-maven-3.6.3/maven-repo</localRepository>
```

### 4.3 在IDEA中使用maven

创建maven项目，可以使用maven一些项目原型创建

### 4.4 创建普通maven项目

不选中原型，直接走下一步

maven由于约定大于配置，可能会遇到我们自己写的配置文件，无法被导出或者生效的问题，解决方案：百度，maven 资源导出问题

maven上可以直接导入项目上jar之间的依赖关系（我使用的社区版本上没有此功能）

## 5.Servlet实验

### 5.1 Servlet简介

* Servlet是sun公司的开发动态web的技术
* SUn在这些API中提供了借口Servlet,如果你开发servle程序，只需要完成两个步骤
  * 实现Servlet接口
  * 开发好的类不熟都web服务器中

实现Servlet接口的Java程序叫做,Servlet

### 5.2 HelloServlet

### 5.3 Maven父子工程例

父工程会在modules中标记子模块

```xml
<groupId>org.example</groupId>
<artifactId>javaweb_01</artifactId>
<version>1.0-SNAPSHOT</version>
<modules>
    <module>demo1</module>
</modules>
```

子工程会在parent中标记付模块

```xml
    <parent>
        <artifactId>javaweb_01</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
```

父工程中的jar包子工程可以直接使用。

### 5.4 maven工程优化

1.编写普通类

2.实现Servlet接口，一般直接继承HttpServlets

### 5.5 Servlet原理

### 5.5 Mapping问题

有意思的是一个通配符的问题其他没特殊的点：

### 5.6 ServletContext

web容器在启动的时候，它会为每个web程序创建一个对应的ServletContext对象，它代表了当前的web应用。

* 共享数据

  在一个Servlet中保存的数据，在另外一个Servlet中可以使用

* 获取初始化参数

* 请求转发

* 读取资源文件

### 5.7 HttpServletResponse

web服务器接受到客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest和HttpServletResponse;

* 如果要获取客户端过来的请求参数，找Request
* 响应信息，找Response

1.分类

* 负责向浏览器发送数据的方法：

```java
public PrintWriter getWriter() throws IOException;
public ServletOutputStream getOutputStream() throws IOException;
```

* 负责设置各种包头
* 各种响应码

扶着

常见应用

1. 向浏览器输出消息
2. 下载文件
   1. 要获取下载文件的路径
   2. 下载的文件名是什么
   3. 设置想办法让浏览器能够支持我们要下载的东西
   4. 获取下载文件的输入流
   5. 创建缓冲区
   6. 获取OutputStream对象
   7. 将FileOutputStream写入buffer缓冲区
   8. 使用OutputStream将缓冲区数据输出到客户端。

### 5.8 HttpServletReques

