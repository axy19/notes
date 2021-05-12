[TOC]
# 基于Maven的ssm整合

## 一、创建项目

创建一个Maven项目，采用web app骨架。

检查项目结构。创建缺失的源码文件夹。


## 二、配置pom文件

#### 1.配置插件，使用jdk8进行编译

1. 配置pom.xml增加插件配置

   ```xml
   <plugin>
   	<groupId>org.apache.maven.plugins</groupId>
   	<artifactId>maven-compiler-plugin</artifactId>
   	<configuration>
   		<source>1.8</source>
   		<target>1.8</target>
   		<encoding>UTF-8</encoding>
   	</configuration>
   </plugin>
   ```

2. 增加完插件后，发现项目报错

   ### 插入图片报错

   ```
   Description	Resource	Path	Location	Type
   
   Project configuration is not up-to-date with pom.xml. Select: Maven->Update Project... from the project context menu or use Quick Fix.	ssmdemo01		line 1	Maven Configuration Problem
   ```

   此时，右键点击项目名执行maven->update project即可使配置执行生效，解决报错。

   ### 插入图片正确

## 三、整合SpingMVC

#### 1.导入springmvc的相关依赖

```xml
<!-- 导入SpringMVC -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>
```

发现maven导入了相关依赖，并通过依赖传递导入其他相关包

如果需要使用@ResponseBody还要再导入jackson相关依赖

```xml
<!-- 导入jackson相关 -->
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-core</artifactId>
	<version>2.8.8</version>
</dependency>
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.8.8</version>

</dependency>
```

发现maven导入了相关依赖，并通过依赖传递导入其他相关包

#### 2.配置前端控制器

配置web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
	<!-- 配置SpringMVC的前端控制器 -->
	<servlet>
		<servlet-name>springmvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>ContextConfigLocation</param-name>
			<param-value>classpath:/springmvc.xml</param-value>
		</init-param>
	</servlet>  
	<servlet-mapping>
		<servlet-name>springmvc</servlet-name>
		<url-pattern>*.action</url-pattern>	
	</servlet-mapping>
</web-app>

```

#### 3.创建springmvc配置文件，配置视图包扫描、注解方式配置、视图解析器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	<!-- 配置包扫描 -->
	<context:component-scan base-package="cn.tedu.controller"></context:component-scan>
	<!-- 配置注解方式mvc -->
	<mvc:annotation-driven></mvc:annotation-driven>
	<!-- 配置视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>	

</beans
```

#### 4.开发控制器

```Java
package cn.tedu.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/first")
public class FirstController {
	@RequestMapping("/test01.action")
	public String test01(){
		System.out.println("first...test01...");
		return "firsttest01";
	}
}

```

#### 5.开发试图

/WEB-INF/jsp/fisttest01.jsp

```jsp
<%@ page language="java" pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
	<head>
	</head>
	<body>
		this is first.jsp....
	</body>
</html>
```

## 四、整合Spring

#### 1.导入相关依赖

```xml
<!--Spring依赖-->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.7.RELEASE</version>
</dependency>
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-aop</artifactId>
	<version>4.3.7.RELEASE</version>
</dependency>
<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjrt</artifactId>
	<version>1.8.0</version>
</dependency>
<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
	<version>1.8.0</version>

</dependency>
```

#### 2.配置web.xml使spring随tomcat启动初始化

```xml
<!-- 配置Spring启动的监听器 -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:/applicationContext.xml</param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

#### 3.开发applicationContext.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:util="http://www.springframework.org/schema/util" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/util 
        http://www.springframework.org/schema/util/spring-util.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	<!-- 配置包扫描 -->
	<context:component-scan base-package="cn.tedu.service"></context:component-scan>
	
	<!-- 配置注解方式IOC -->
	<context:annotation-config></context:annotation-config>
	
</beans>

```

#### 4.开发Service

```java 
package cn.tedu.service;

import cn.tedu.domain.User;

public interface FirstService {
	public void first(User user);
}
```

```java 
package cn.tedu.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import cn.tedu.domain.User;
import cn.tedu.mapper.UserMapper;

@Service
public class FirstServiceImpl implements FirstService {
	
	@Autowired
	UserMapper userMapper = null;
	
	@Transactional
	@Override
	public void first(User user) {
		System.out.println("service..first...test01...");
		userMapper.insertOne(user);
		//int i = 1/0;
	}


}
```

#### 五、整合MyBatis

#### 1.引入依赖

MySQL驱动

```xml
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.0.8</version>

</dependency>
```

c3p0/DRUID数据源

```xml
<!-- datasource pool c3p0-->
<dependency>
	<groupId>com.mchange</groupId>
	<artifactId>c3p0</artifactId>
	<version>0.9.5.2</version>

</dependency>
```

```xml
<!-- datasource pool druid-->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.0.14</version>
</dependency>
```

mybatis相关约束

```xml
<!-- myBatis核心包 -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.4.5</version>
</dependency>
<!-- myBatis整合Spring用的包 -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>1.3.1</version>
</dependency>
```

#### 2.整合到Spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:util="http://www.springframework.org/schema/util" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/util 
        http://www.springframework.org/schema/util/spring-util.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	<!-- 配置包扫描 -->
	<context:component-scan base-package="cn.tedu.service"></context:component-scan>
	
	<!-- 配置注解方式IOC -->
	<context:annotation-config></context:annotation-config>
	
	<!-- 配置数据源 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql:///ssmdb"></property>
		<property name="user" value="root"></property>
		<property name="password" value="root"></property>
	</bean>
	
	<!-- 整合MyBatis -->
	<!-- 配置SqlSessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<property name="configLocation" value="classpath:/sqlMapConfig.xml"></property>
		<property name="mapperLocations" value="classpath:/mappers/*.xml"></property>
	</bean>
	<!-- MyBatis MapperBean扫描器，负责为MapperBean生成实现类 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="cn.tedu.mapper"></property>
	</bean>
	
	<!-- 声明式事务处理 -->
	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	<!-- 开启注解方式控制事务 -->
	<tx:annotation-driven/>
	
</beans>

```

#### 3.开发sqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
</configuration>
```

#### 4.开发userMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.tedu.mapper.UserMapper">
	<insert id="insertOne">
		insert into user values (null,#{name},#{age})
	</insert>	

</mapper>
```

#### 5.开发对应接口

```java 
package cn.tedu.mapper;

import cn.tedu.domain.User;

public interface UserMapper {
	public void insertOne(User user);
}
```

