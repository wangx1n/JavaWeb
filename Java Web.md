# Java Web

## 3. tomcat

tomcat 改端口：

1.打开server.xml，修改connector

```xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />    
```

默认端口：8080

http:80

https:443



2配置主机名称:

* 默认主机名 localhost
* 默认应用存放位置 webapps

```xml
<Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
```



## 4. http

### 4.2 http 1.0和http 2.0

* http 1.0

  http/1.0 客户端和web服务器连接后，只能获得一个web资源，断开连接

* http 2.0

  http/1.1 客户端和web服务器连接后，可以获得多个web资源

### 4.3 http请求

```java
Request URL: https://www.baidu.com/
Request Method: GET
Status Code: 200 OK
Remote Address: 14.215.177.38:443
Referrer Policy: strict-origin-when-cross-origin
```

#### **1.请求行：**

1.get和post

get：参数有限制，大小有限制，不安全，高效

post：参数无限制，大小无限制，安全，低效

#### 2.消息头

```java
Accept:  支持的数据类型
Accept-Encoding:	支持哪种编码格式
Accept-Language:	语言环境
Connection: keep-alive 告诉浏览器，请求完是断开还是连接
```



### 4.4 http响应

```java
Cache-Control: no-cache
Connection: keep-alive
Content-Type: text/html;charset=utf-8
```

#### 2.状态码

200：成功

3** 重定向

4** 资源不存在

5** 服务器代码错误

502 网关错误



## 6. Servlet

把实现了servlet接口的程序，叫做servlet

servlet默认实现类：httpservlet, genericservlet

实现httpservlet时，重写doGet和doPost



导入 servlet 4.0.1

```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```



更改web.xml为最新

servlet 4.1

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
     http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
</web-app>
```

4.编写servlet程序

​	1.编写一个类

​	2.实现servlet接口

```java
package com.javatest.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * @program: CodingInterview
 * @description:
 * @author: wang xin
 * @create: 2021/3/11 20:34
 **/
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter writer = resp.getWriter(); //响应流
        writer.println("hello servlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

5.编写servlet映射

​	为什么需要映射：服务器需要知道请求需要进入到哪一个servlet

```xml
    <servlet>
        <servlet-name>HellpServlet</servlet-name>
        <servlet-class>com.wangxin.servlet.HelloServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>HellpServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
```



### 6.3 servlet原理

负责从处理web容器发过来的请求，并返回response对象给web容器

![image-20210312143540080](C:\Users\luccifer\AppData\Roaming\Typora\typora-user-images\image-20210312143540080.png)

### 6.4 Mapping问题

1.一个servlet可以指定一个映射路径

```xml
    <servlet-mapping>
        <servlet-name>HellpServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
```

2.一个servlet可以指定多个路径

```xml
    <servlet-mapping>
        <servlet-name>HellpServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>  <servlet-mapping>
        <servlet-name>HellpServlet</servlet-name>
        <url-pattern>/hello2</url-pattern>
    </servlet-mapping>
```

3.一个servlet可以指定默认路径

```xml
  <servlet-mapping>
        <servlet-name>HellpServlet</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
```

4.可以自定义后缀实现请求映射

*前面不能加映射路径（/）

```xml
  <servlet-mapping>
        <servlet-name>HellpServlet</servlet-name>
        <url-pattern>*.wangxin</url-pattern>
    </servlet-mapping>
```

5.优先级问题

指定了固有的映射路径，优先级最高，如果找不到，就会走默认处理请求

```xml
    <servlet>
        <servlet-name>ErrorServlet</servlet-name>
        <servlet-class>com.wangxin.servlet.ErrorServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ErrorServlet</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
```

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.setContentType("text/html");
    resp.setCharacterEncoding("utf-8");

    PrintWriter writer = resp.getWriter();
    writer.println("<h1>404</h1>");
}
```



遇到的问题：

如果部署到tomcat时，没有artifacts，首先检查webapp文件夹是否有圆点，如果没有圆点，那么就在module下添加web reference:

![image-20210313194453353](C:\Users\luccifer\AppData\Roaming\Typora\typora-user-images\image-20210313194453353.png)

![image-20210313194529010](C:\Users\luccifer\AppData\Roaming\Typora\typora-user-images\image-20210313194529010.png)

注意web resource directory要填webapp文件夹的路径。





### 6.5 servlet context

代表当前web应用

* #### 共享数据

servlet之间可以共享数据

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = "王鑫";

        ServletContext servletContext = this.getServletContext();

        servletContext.setAttribute("username",username);

        resp.setCharacterEncoding("utf-8");

        resp.setContentType("text/html");

        System.out.println("hello servlet");
    }
```

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html");
        resp.getWriter().println(this.getServletContext().getAttribute("username"));
    }
```

这里遇到一个问题，即先生成了writer,再设置resp的属性为utf-8等，还是乱码。 应该先设置，再获取writer。

* #### 获取初始化参数

```xml
  <servlet>
    <servlet-name>GetInitialParam</servlet-name>
    <servlet-class>com.servlet.GetInitParam</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>GetInitialParam</servlet-name>
      <url-pattern>/GetInitialParam</url-pattern>
    <url-pattern></url-pattern>
  </servlet-mapping>
```

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.getServletContext().getRequestDispatcher("/GetInitialParam").forward(req, resp);
    }
```

* ### 转发

```java
 @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.getServletContext().getRequestDispatcher("/GetInitialParam").forward(req, resp);
    }
```



* ### 读取Properties

```java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        InputStream resourceAsStream = this.getServletContext().getResourceAsStream("WEB-INF/classes/db.properties");

        Properties properties = new Properties();

        properties.load(resourceAsStream);
        String usename = properties.getProperty("username");
        String pwd = properties.getProperty("password");

        resp.getWriter().println(usename+ ":"+pwd);
    }
```



> 遇到的问题

* 父工程的Pom在子工程里用不了，需要加上parent标签

```xml
  <parent>
    <artifactId>JavaWeb</artifactId>
    <groupId>org.example</groupId>
    <version>1.0-SNAPSHOT</version>
  </parent>
```

* 如果properties在java目录下，需要在pom的build标签下加入

```xml
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>

      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>
    </resources>
```

