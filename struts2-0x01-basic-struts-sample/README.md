# struts2-0x01-basic-struts-sample
## 1.1 Struts 2 使用须知
如果想使用Struts2,必须满足如下条件:
- Servlet API 2.4 +
- JSP 2.0+
- JDK 7+
## 1.2 如何创建一个Struts2 Web 应用程序
1.添加依赖

pom.xml 配置如下:
```xml 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--设置本项目的groupId artifactId 和版本信息-->
    <groupId>com.xingyun.struts2</groupId>
    <artifactId>struts2-0x01-basic-struts-sample</artifactId>
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
        <jetty.maven.plugin.version>9.4.7.v20170914</jetty.maven.plugin.version>
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
        <!-- 定义项目的上下文路径,如果不配置,默认是struts2-0x01-basic-struts-sample-1.0-SNAPSHOT -->
        <finalName>basic_struts_sample</finalName>
        
        <!-- 定义插件 -->
        <plugins>
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
> pom.xml引入了内嵌容器Jetty插件并配置了上下文

2.web.xml 
```xml
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
3.index.html
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
4.struts.xml配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <!--开发环境设置为true,生产环境需要修改为false-->
    <constant name="struts.devMode" value="true" />
    <!--namespace作用:
    如果namespace="/web" URL访问就是这样
    http://127.0.0.1:8080/basic_struts_sample/web/index.action
    如果namespace="/" URL访问就是这样
    http://127.0.0.1:8080/basic_struts_sample/index.action
    -->
    <package name="basic-struts2" namespace="/" extends="struts-default">
        <!--当访问http://127.0.0.1:8080/basic_struts_sample/时默认跳转到http://127.0.0.1:8080/basic_struts_sample/index.action-->
        <default-action-ref name="index" />
        <!--处理拦截http://127.0.0.1:8080/basic_struts_sample/index.action-->
        <action name="index">
            <result>/WEB-INF/content/index.jsp</result>
        </action>
    </package>

</struts>
```
> index.jsp 一般不能直接访问,需要放到WEB-INF/content/文件夹下被保护起来

5.添加index.jsp
```html
<!DOCTYPE html>
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Basic Struts 2 Application - Welcome</title>
  </head>
  <body>
    <h1>Welcome To Struts 2!</h1>
  </body>
</html>
```
6. 添加下我们的日志配置

log4j2.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.opensymphony.xwork2" level="debug"/>
        <Logger name="org.apache.struts2" level="debug"/>
        <Root level="warn">
            <AppenderRef ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```
7.启动我们的应用程序

pom.xml 根目录输入maven命令如下
```
mvn jetty:run
```
8.访问我们的页面

[http://127.0.0.1:8080/basic_struts_sample/](http://127.0.0.1:8080/basic_struts_sample/)

## 参考资料
- [How To Create A Struts 2 Web Application](https://struts.apache.org/getting-started/how-to-create-a-struts2-web-application.html)
- [官方 Struts2 examples](https://github.com/apache/struts-examples)