# struts2-0x02-hello-world-sample

这篇博文我们来学习下如何使用Struts2 编写一个Web应用程序。

1.添加项目依赖
pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--设置本项目的groupId artifactId 和版本信息-->
    <groupId>com.xingyun.struts2</groupId>
    <artifactId>struts2-0x02-hello-world-sample</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <!-- 设置项目所使用的编码为UTF-8 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <!--设置项目所用JDK版本,也可以使用低版本，建议使用JDK1.8以上版本-->
        <java.version>12</java.version>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>
        <maven.compiler.plugin.version>3.8.1</maven.compiler.plugin.version>
        <!--是否跳过测试-->
        <skipTests>true</skipTests>
        <!--设置项目使用第三方依赖包，建议以后都采用这种方式，统一在一个地方声明版本号，这样对项目可以统一管理，方便以后升级-->
        <jetty.maven.plugin.version>9.4.20.v20190813</jetty.maven.plugin.version>
        <struts2.version>2.5.20</struts2.version>
        <log4j2.version>2.12.1</log4j2.version>
    </properties>

    <dependencies>
        <!--添加Struts2核心依赖-->
        <dependency>
            <groupId>org.apache.struts</groupId>
            <artifactId>struts2-core</artifactId>
            <version>${struts2.version}</version>
        </dependency>
        <!--添加Log4j2日志依赖-->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>${log4j2.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>${log4j2.version}</version>
        </dependency>
    </dependencies>

    <build>
        <!-- 定义项目的上下文路径,如果不配置,默认是struts2-0x02-hello-world-sample-1.0-SNAPSHOT -->
        <finalName>hello_world_sample</finalName>

        <!-- 定义插件 -->
        <plugins>
            <!-- https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-maven-plugin -->
            <!--定义这个插件允许我们使用mvn jetty:run命令运行我们当前这个项目-->
            <plugin>
                <groupId>org.eclipse.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>${jetty.maven.plugin.version}</version>
                <configuration>
                    <webApp>
                        <contextPath>/${project.build.finalName}</contextPath>
                    </webApp>
                    <stopKey>CTRL+C</stopKey>
                    <stopPort>8999</stopPort>
                    <scanIntervalSeconds>10</scanIntervalSeconds>
                    <scanTargets>
                        <scanTarget>src/main/webapp/WEB-INF/web.xml</scanTarget>
                    </scanTargets>
                </configuration>
            </plugin>
            <!--该插件限定Maven 打包时所使用的版本,避免出现版本不匹配问题-->
            <!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-compiler-plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven.compiler.plugin.version}</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
2.配置web.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app id="WebApp_ID" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
    <display-name>struts2-0x01-hello-world-sample</display-name>
    <!--欢迎页面,不会被拦截可访问的页面 访问该页面后会自动转向index.action-->
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>

    <!--Add Struts 2 Servlet Filter -->
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <!--Add 拦截所有/*请求 -->
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```
3.添加index.html
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
    <META HTTP-EQUIV="Refresh" CONTENT="0;URL=index.action">
</head>

<body>
<p>Loading ...</p>
</body>
</html>
```
4.创建一个实体类
```
/**
 * @功能
 * @作者 @author星云
 * @日期和时间 9/7/2019 12:38 PM
 */
public class MessageStore {
    private String message;
    public MessageStore() {
        message = "Hello Struts User";
    }

    public String getMessage() {
        return message;
    }
}
```
5.编写一个controller
```
import com.opensymphony.xwork2.ActionSupport;
import com.xingyun.struts2.model.MessageStore;

/**
 * @author 星云
 * @功能
 * @日期和时间 9/7/2019 12:40 PM
 */
public class HelloWorldAction extends ActionSupport {
    private MessageStore messageStore;

    @Override
    public String execute() {
        messageStore = new MessageStore() ;

        return SUCCESS;
    }

    public MessageStore getMessageStore() {
        return messageStore;
    }
}
```
6. struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <!--开发环境设置为true,生产环境需要修改为false-->
    <constant name="struts.devMode" value="true" />
    <!--namespace作用:
    如果namespace="/web" URL访问就是这样
    http://127.0.0.1:8080/hello_world_sample/web/index.action
    如果namespace="/" URL访问就是这样
    http://127.0.0.1:8080/hello_world_sample/index.action
    -->
    <package name="basic-struts2" namespace="/" extends="struts-default">
        <!--当访问http://127.0.0.1:8080/hello_world_sample/时默认跳转到http://127.0.0.1:8080/hello_world_sample/index.action-->
        <default-action-ref name="index" />
        <!--处理拦截http://127.0.0.1:8080/hello_world_sample/index.action-->
        <action name="index">
            <result>/WEB-INF/content/index.jsp</result>
        </action>
        <action name="hello" class="com.xingyun.struts2.controller.HelloWorldAction" method="execute">
            <result name="success">/WEB-INF/content/HelloWorld.jsp</result>
        </action>
    </package>

</struts>
```
7. index.jsp
```
<!DOCTYPE html>
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib prefix="s" uri="/struts-tags" %>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Basic Struts 2 Application - Welcome</title>
</head>
<body>
<h1>Welcome To Struts 2!</h1>
<p><a href="<s:url action='hello'/>">Hello World</a></p>
</body>
</html>
```
8.HelloWorld.jsp
```
<%--
  Created by IntelliJ IDEA.
  User: xingyun
  Date: 9/7/2019
  Time: 12:42 PM
  To change this template use File | Settings | File Templates.
--%>
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib prefix="s" uri="/struts-tags" %>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Hello World!</title>
</head>
<body>
<h2><s:property value="messageStore.message" /></h2>
</body>
</html>
```
9.启动我们的应用程序

pom.xml 根目录输入maven命令如下
```
mvn jetty:run
```
10.访问我们的页面

[http://127.0.0.1:8080/hello_world_sample/](http://127.0.0.1:8080/hello_world_sample/)

# 参考链接
- [Hello World Using Struts 2](https://struts.apache.org/getting-started/hello-world-using-struts2.html)
- [官方 Struts2 examples](https://github.com/apache/struts-examples)
