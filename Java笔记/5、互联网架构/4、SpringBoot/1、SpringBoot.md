[TOC]

# SpringBoot

## 一、相关介绍

#### 1.背景

开发环境的特点（开发人员喜欢什么样的开发环境）：

1. 做复杂的配置，做步骤繁琐的发布安装（Spring框架）。
2. 配置，安装相对简单（SpringBoot）。

简单，快的特点。使Spring出现了新的框架SpringBoot。

#### 2.SpringBoot

基于Spring实现快速配置，方便搭建的一个工具框架。

#### 3.SpringBoot的特点

##### 3.1 独立运行的Spring容器

回忆：Spring框架运行必须依赖于第三方的Tomcat容器才可以实现运行启动。

springboot可以像开发代码一样，使用main方法开启一个程序的入口。实现spring容器独立运行。

##### 3.2 内嵌的servlet容器

回忆：在开发web应用时，打成war包，放到tomcat运行。

springboot由于存在独立运行、自动配置的特点，可以专门为web容器做单独配置，配合第一个特点独立运行的spring容器，依然可以使用main方法启动一个web应用。默认情况下使用的tomcat，可以换成其他容器jetty undertow。

##### 3.3 springboot简化依赖

springboot基于spring，扩展了大量的代码，这些代码都需要非常复杂非常多的jar包支持。springboot方便依赖的使用，实现了利用maven这种项目管理工具的简化依赖，例如，依赖了spring-boot-starter-web,之前使用的所有spring  springmvc  jackson等相关的依赖jar包全部导入到工程里来。而且简化依赖根据使用场景不同，使用功能不同区分的。

##### ==3.4 自动配置==

springboot提倡0配置文件（xml文件消失了）。封装了大量自动配置代码。可以根据项目中使用的依赖，实现底层自动配置。

例如：

以前使用ssm框架配置持久层（dao层）。

准备xml配置文件，xml配置文件中有很多的bean标签

datasource：username，password, driverclass,url

sqlSession:别名包，扫描mybatis配置文件，加载的映射文件mapper.xml注入datasource等等

mapperScan：扫描接口mapper的所在包路径

springboot不需要配置任何xml文件为持久层mybatis整合做准备。只需要提供对应的属性值即可。但是并不是所有应用场景都需要持久层支持。当我们依赖了spring-boot-starter-jdbc时候springboot可以判断即将使用持久层，然后会创建对应的配置内容。

## 二、手动搭建springboot工程

#### 1.maven的继承

##### 1.1 继承的意义

在maven的项目管理工具中，提供继承的特性，可以利用继承实现所有项目==资源版本统一==的问题。

![](https://note.youdao.com/yws/api/personal/file/FAC88C2F54904C7AB428668B8B4900C7?method=download&shareKey=c723c9e9703bf6805afa77163b6cb0aa)

##### 1.2 maven工程实现继承

1. 准备父级工程定义一些继承资源（都是标签）

   - ==注意：所有可以被继承的父级工程 packaging类型（打包类型）必须是pom类型==

     ```
     <packaging>pom</packaging>
     ```

   - 搭建配置步骤

     - 使用quickstart骨架创建maven工程 parent

     - 实现一些依赖资源让子工程测试中继承

       ```
       <dependency> 
       	<groupId>org.springframework</groupId>
       	<artifactId>spring-beans</artifactId> 
       	<version>4.3.7.RELEASE</version>
       </dependency>
       ```

     - 使用install命令将parent安装到本地库

   - 可以搭建子工程

     - 继承parent，获取继承的资源（dependency就会继承）

     - 搭建配置步骤

       1. 使用quickstarter骨架创建maven工程 child

       2. 实现继承子工程配置parent标签，使用三个坐标标签指向parent工程

          ```
          <parent> 
          	<groupId>cn.tedu</groupId> 
          	<artifactId>parent</artifactId> 
          	<version>1.0-SNAPSHOT</version> 
          </parent>
          ```

2. 继承资源

   - dependency，相当于强制继承

   - groupId

   -  version

   - properties：父级工程定义的变量

   - dependencyManagement：常用的一种统一资源的继承方式，只会继承版本而不会强制让子工程依赖资源

     ```
     <dependencyManagement>
     	<dependencies>
      		<dependency>
     			<groupId>org.springframework</groupId>
      			<artifactId>spring-beans</artifactId>
      			<version>4.3.7.RELEASE</version>
     		</dependency>
     	</dependencies>
     </dependencyManagement>
     ```

#### 2.maven聚合

maven在管理大量的工程时，每一个工程都可能要执行编译，打包，安装，测试，部署等生命周期的命令，一个一个去执行这些命令效率非常低。maven提供了聚合的配置，实现统一执行命令。

![](https://note.youdao.com/yws/api/personal/file/0648C34ED7B84071AC8316C15A4855E7?method=download&shareKey=185c363bfad8af7001dd7e3029a08ae6)

实现聚合的配置

挑选一个聚合工程（parent）在聚合工程中添加标签module指向管理的所有聚合子工程

```
<modules>
 <module>../child1</module>
 <module>../child2</module>
 <module>../child3</module>
 </modules>
```

![](https://note.youdao.com/yws/api/personal/file/F8C514654D1547039D7180A183F7DD96?method=download&shareKey=3408bc748d5b13322b82fd20bde12a7b)

聚合需要创建工程，配置相对路径的modules标签，比较繁琐，实际上所有的开发工具都具备直接支持聚合工程，聚合子工程的创建，idea直接在当前工程右键创建module即可实现聚合的管理，并不会将聚合子工程存放到独立的workspace而是会以当前聚合工程目录为根目录建聚合子工程

#### 3.总结

可以通过对继承、聚合的了解，创建idea的maven工程时，同一个大的项目完全可以通过一个父工程/聚合工程实现管理工作（maven管理工具）

#### 4.手动搭建一个spring boot工程

1. 创建一个父级工程、聚合工程实现springboot所有demo案例的测试编写

   - 使用quickstart骨架创建工程

     ![](https://note.youdao.com/yws/api/personal/file/8CF95E1434CC44CDBE4261E57F4E4DE5?method=download&shareKey=de8e589e68e0d3accc4e32157ea665e5)

   - 修改添加packaging类型为pom

     ```
     <packaging>pom</packaging>
     ```

   - 父级工程继承spring-boot-starter-parent资源

     ```
     <parent>
     	<groupId>org.springframework.boot</groupId>
     	<artifactId>spring-boot-starter-parent</artifactId>
     	<version>1.5.9.RELEASE</version>
     </parent>
     ```

2. 使用聚合功能实现第一个测试案例测试搭建

   - 右键springboot-parent选择new 一个module创建新工程quickstart骨架

   - 使用demo案例工程编写配置一个web应用实现访问localhost:8080/hello?name=wang返回响应 hello springboot I am wang

   - 需要搭建一个web应用 springboot已经通过自动配置可以实现内嵌servlet容器调用，只需要添加一个简化依赖即可

     ```
     <dependency>
     		<groupId>org.springframework.boot</groupId>
     		<artifactId>spring-boot-starter-web</artifactId>
     </dependency>
     ```

   - 编写启动类

     - 对于springboot来讲，能够实现独立运行，基于spring完成了一套方法的封装，最终只需要调用run方法，在main中执行，即可实现单独启动spring springmvc框架进程

       ```
       @SpringBootApplication
       public class StarterDemo01 {
       	public static void main(String[] args) {
       		//调用springboot的静态run方法传递当前启动类
       		//反射对象
       		SpringApplication.run(StarterDemo01.class,args);
       	}
       }
       ```

     - 实现controller编写（controller service注入依赖，控制反转）IndexController IndexService 使用service注入到controller完成测试

       所有代码所在的包名，必须是cn.tedu（根包）的下级包

#### 5.对比springboot环境配置和Spring+SpringMVC环境配置

| 对比内容                                                     | springboot                  | spring+springmvc                                             |
| ------------------------------------------------------------ | --------------------------- | ------------------------------------------------------------ |
| web.xml                                                      | 配置文件消失了              | 第三方tomcat运行时加载web.xml通过web.xml加载框架配置xml      |
| applicationContext.xml                                       | 配置文件消失了              | 1.context:component-scan:包扫描，扫描包路径下所有类上的注解@Controller,@Service,@Repository,@Autowired,@Resource,@Value2.其他bean标签配置 |
| spring-mvc.xml                                               | 配置文件消失了              | 开启springmvc注解驱动使得@RequestMapping,@ResponseBody..     |
| 一些properties文件mysql.propertiesdatasource.propertiesredis.properties | 整合了applicatin.properties | 用来配置需要在配置文件中创建bean对象的属性 ${name}相当于properties有name=*** |

## 三、springboot进阶

#### 1.springboot的配置文件

指的是读取属性使用的properties格式的全局文件。在springboot初始化时，会创建一些赋予属性定义的值的bean对象，有的没有默认值，有的一些属性是具备默认值的。例如：tomcat端口8080，datasource操作持久层数据源user password url driver,需要在全局配置文件中指定属性。除此以外还可以覆盖默认值。

##### 1.1 application.properties

是springboot唯一的一个全局配置文件，可以读取属性，可以配置需要的自定义数据例如，可以修改web容器启动端口，修改web容器的访问根目录

默认端口是8080

默认访问项目根目录是/

可以在maven工程的src/main/resources文件夹

![](https://note.youdao.com/yws/api/personal/file/15235F21417B4E169A6BD2C58DB73C7C?method=download&shareKey=9cecc44ef8987ae4061230abd7fbe5fb)

==注意==：当前项目中的module资源配置需要调整 右键选择open module settings找到对应配置

![](https://note.youdao.com/yws/api/personal/file/CF230E9ED27742A19C054FA0AE404A40?method=download&shareKey=cec51cd005e54bb6de411e51ad35852e)

添加自定义属性

```
#修改端口
server.port=8090
#修改默认项目根路径 localhost:8080/hello
#localhost:8080/big1910/hello
server.context-path=/big1910
```

##### 1.2 application.yml

properties学习过程中常用的属性配置格式 key-value，经常会出现重复的配置

例如：

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql:///easydb
spring.datasource.username=root
spring.datasource.password=root
```

spring.datasource重复了4次

yml格式是企业中常用的一种配置格式，面向数据结构的，可以轻松的体现层级关系的配置逻辑（底层翻译的时候依然是编程key-value读取）

properties格式

```
server.port=8090
server.context-path=/big1910
```

yml格式（[空格]不是真正输入的字符串，提示这里使用空格）

```
server:
[空格]port:[空格]8090
[空格]context-path:[空格]/big1910
```

简单练习

properties手动转化yml

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql:///easydb
spring.datasource.username=root
spring.datasource.password=root
```

```
spring:
[]datasource:
[][]driver-class-name:[]com.mysql.jdbc.Driver
[][]url:[]值
[][]username:[]值
[][]password:[]值
```

实现properties与yml格式相互转化

https://www.toyaml.com/index.html

#### 2.springboot整合mybatis

对应到之前使用ssm框架开发的项目（最终会把这个项目重新整合到springboot）

##### 实现步骤

springboot提倡0配置，扩展了大量的应对开发不同场景的配置类代码也准备持久层的相关配置。我们只要完成依赖和属性配置就可以直接使用这个持久层框架。

1. 项目中引入持久层框架需要的依赖

   - springboot-jdbc自动配置datasource所需要的依赖

   - MySQL提供驱动类依赖com.jdbc.mysql.Driver

   - mybatis-springboot

   - 连接池（c3p0）

     ```
     <!--持久层相关依赖内容-->
     <!--spring-boot-jdbc-->
     <dependency>
     	<groupId>org.springframework.boot</groupId>
     	<artifactId>spring-boot-starter-jdbc</artifactId>
     </dependency>
     <!--mysql-->
     <dependency>
     	<groupId>mysql</groupId>
     	<artifactId>mysql-connector-java</artifactId>
     </dependency>
     <!--mybatis整合springboot依赖-->
     <dependency>
     	<groupId>org.mybatis.spring.boot</groupId>
     	<artifactId>mybatis-spring-boot-starter</artifactId>
     	<version>1.3.0</version>
     </dependency>
     ```

   - 常见问题：不了解自定配置原理的话，容易在课下自习练习时，因为引入了spring-boot-starter-jdbc导致问题出现。

     原因：根据你这个依赖创建datasource。但是项目中没有提供driver username password url这些值。

     Description:

     Cannot determine embedded database driver class for database type NONE

2. 给自动配置的datasource赋值4个属性

   driverClass

   username

   password

   url

   ```
   spring.datasource.username=root
   spring.datasource.password=root
   spring.datasource.url=jdbc:mysql:///easydb
   spring.datasource.driver-class-name=com.mysql.jdbc.Driver
   ```

3. 开始准备mybatis持久层的框架相关配置

   之前配置存在一个bean对象SqlSession（实现接口类抽象方法动态代理，完成绑定sql语句，mybatis才能发送sql到数据库执行逻辑）

   \#扫描映射文件mapper.xml路径，只要将mapper映射文件放到resources下

    ==mybatis.mapper-locations=classpath:/mapper/*.xml==

   \#别名包，使用映射文件ResultType的时候需要指定全路径名称的，如果所有的domain实体类都在一个相同包下

    ==mybatis.type-aliases-package=cn.tedu.domain==

   \#开启驼峰命名 一旦开启驼峰命令，就可以让mybatis封装对象时，自动对应类属性和字段名称，满足驼峰命名的对应。

   例如，表格字段 user_name user_password user_email，

   类属性username,pwd useremail，处理解决方式可以使用sql语句中的字段别名。

   mybatis提供的驼峰命名，遵循了表格字段这种A_B_C的结构实现的简单配置。只要类中的属性满足驼峰命名的就可以自动封装。

   类属性：userName(user_name) userPassword,userEmail

   直接使用select * from table;

    ==mybatis.configuration.map-underscore-to-camel-case=true==

   \#关闭持久层框架缓存,持久层框架默认是有缓存逻辑，基本思路使用sql语句作为key值，使用返回的resultSet数据作为value，一旦发现多次执行了同一个sql（select * from table）.性能不高。redis做缓存

    ==mybatis.configuration.cache-enabled=false==

4. 配置好对应其他内容

   - 准备映射文件（拷贝一个mapper.xml）

   - 编写mapper接口类，在映射文件中namespace指向接口

   - SSM框架中配置bean对象     MapperScan 由注解代替

     ```
     @MapperScan需要和启动类在同一个位置
     @MapperScan("cn.tedu.mapper")//指定扫描mapper接口的包名称
     ```

5. 按照之前开发逻辑开发持久层相关功能

   - 准备一个表格，填写一些数据

   - 代码中准备一个对应表格User类

     - 驼峰命令，user_name对应userName
     - 类全路径名称满足别名包使用     cn.tedu.domain.User，别名包cn.tedu.domain

   - 测试持久层框架使用

     需求：做表格行数据的查询功能，发起请求localhost:8080/getAllUser:返回表格中所有的user数据List<User>

     localhost:8080/getUser?userId=1:返回表格中一个user

     ![](https://note.youdao.com/yws/api/personal/file/366DFEA3242144599C521A8B3121A33B?method=download&shareKey=355e2306252a7fe0eeb170c85fa8c7e4)

     测试运行出现常见的异常问题：

      Invalid bound statement (not found): cn.tedu.mapper.DemoMapper.selectUserByUserId

     无效的绑定，接口抽象方法，和sql语句没有绑定正确

     原因：namespace没有对应正确的接口类

      抽象方法名称没有对应标签的id
   
6. 总体代码编写

   - application.properties配置文件

     ```
     #修改端口
     server.port=8090
     #修改默认项目根路径 localhost:8080/hello
     #localhost:8080/big1910/hello
     server.context-path=/big1910
     #datasource配置
     spring.datasource.username=root
     spring.datasource.password=root
     spring.datasource.url=jdbc:mysql:///easydb
     spring.datasource.driver-class-name=com.mysql.jdbc.Driver
     #配置底层连接池，默认tomcat jdbc
     #spring.datasource.type=
     #SqlSession+mybatis
     #扫描映射文件mapper.xml路径
     mybatis.mapper-locations=classpath:/mapper/*.xml
     #别名包
     mybatis.type-aliases-package=cn.tedu.domain
     #开启驼峰命名
     mybatis.configuration.map-underscore-to-camel-case=true
     #关闭持久层框架缓存
     mybatis.configuration.cache-enabled=false
     ```

   - User

     ```
     package cn.tedu.domain;
     public class User {
         //满足驼峰命名
         //user_id,user_name,user_age
         //一旦使用实体类封装有现实意义的数据必须使用封装类型
         //LONG INTEGER DOUBLE FLOAT作为属性类型使用。
         private Long userId;
         private String userName;
         private Integer userAge;
         public Long getUserId() {
             return userId;
         }
         public void setUserId(Long userId) {
             this.userId = userId;
         }
         public String getUserName() {
             return userName;
         }
         public void setUserName(String userName) {
             this.userName = userName;
         }
         public Integer getUserAge() {
             return userAge;
         }
         public void setUserAge(Integer userAge) {
             this.userAge = userAge;
         }
     }
     ```

   - Controller

     ```
     package cn.tedu.controller;
     import cn.tedu.domain.User;
     import cn.tedu.service.DemoService;
     import org.springframework.beans.factory.annotation.Autowired;
     import org.springframework.web.bind.annotation.RequestMapping;
     import org.springframework.web.bind.annotation.RestController;
     @RestController
     public class DemoController {
         @Autowired
         private DemoService ds;
         //localhost:8080/getUser?userId=1
         @RequestMapping("getUser")
         public User getUser(Long userId){
             return ds.getUser(userId);
         }
     }
     ```

   - Service

     ```
     package cn.tedu.service;
     import cn.tedu.domain.User;
     import cn.tedu.mapper.DemoMapper;
     import org.springframework.beans.factory.annotation.Autowired;
     import org.springframework.stereotype.Service;
     @Service
     public class DemoService {
         //注入持久层mapper实现读取数据库
         @Autowired
         private DemoMapper dm;
         public User getUser(Long userId){
             //给控制层和业务层命名时，使用业务意义命名
             //持久层 增删查改的详细名称
             return dm.selectUserByUserId(userId);
         }
     }
     ```

   - Mapper

     - 接口

       ```
       package cn.tedu.mapper;
       import cn.tedu.domain.User;
       public interface DemoMapper {
           public User selectUserByUserId(Long userId);
       }
       ```

     - mapper映射

       ```xml
       <?xml version="1.0" encoding="UTF-8" ?>
       <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
       <mapper namespace="cn.tedu.mapper.DemoMapper">
           <select id="selectUserByUserId" parameterType="Long" resultType="User">
               select * from demo_user where user_id=#{userId}
           </select>
       </mapper> 
       ```

   - 开启

     ```Java
     package cn.tedu;
     import org.mybatis.spring.annotation.MapperScan;
     import org.springframework.boot.SpringApplication;
     import org.springframework.boot.autoconfigure.SpringBootApplication;
     /**
      * 使当前class类成为测试案例中唯一的
      * springboot启动类，需要使用main调用run方法
      * 需要使用springboot的核心注解
      */
     @SpringBootApplication
     @MapperScan("cn.tedu.mapper")//指定扫描mapper接口的包名称
     public class StarterDemo01 {
         public static void main(String[] args) {
             //调用springboot的静态run方法传递当前启动类
             //反射对象
             SpringApplication.run(StarterDemo01.class,args);
         }
     }
     ```

#### 3.springboot自动配置的原理

通过原理，使用配置代码实现后续技术的引入 redis ，rabbitmq, elasticsearch

##### 3.1 spring背景

最新版本5.x，从旧版本到新版本更新迭代大趋势：趋于简化

1. 1.x时代：spring使用的特点，配置大量的xml，并且xml中需要配置非常多的bean标签，使用容器创建bean对象实现各种功能。

2. 2.x时代：Java5出现（jdk1.5）提供了新的特性 注解。spring可以利用注解功能将xml中的配置逻辑转换为注解使用@Controller @Service @Autowired @Configuration等等。

   争论点：到底使用注解完成配置还是使用xml

   结论：spring框架IOC DI使用注解方便（控制层，业务层，持久层），如果涉及到其他插件，软件整合还是使用xml配置

3. 3.X/4.X/5X：可以实现利用注解，将配置全部转化为代码实现。所以0配置不是springboot实现的，spring早就可以实现，但是需要编写大量配置代码

##### 3.2 spring自动配置原理的几个核心注解

###### 3.2.1 @Configuration: 类注解

可以将一个代表一个完整的xml配置文件写成代码的格式

```Java
/**
	* 可以将MyConfig看成是一个xml配置文件只要spring容器
	* 启动时加载这个配置类，就能实现相同的功能
*/
@Configuration
public class MyConfig {
//如果类的代码里是空的，相当于xml配置文件什么都没有
}
```

###### 3.2.2 @Bean：方法注解

相当于在配置类中bean标签，可以将类中的方法返回值做成bean对象，交给容器管理

1. 创建一个测试的Bean1

2. 在配置类中编写代码

   ```Java
   //利用@Bean注解实现配置Bean1的容器管理对象
   @Bean
   public Bean1 initBean1(){
       //将其生成new
   	Bean1 b1=new Bean1();
   	return b1;
   }
   ```
   
3. 可以像配置文件指定bean标签的property属性一样，给bean对象初始化属性

   ```
   //如果类的代码里是空的，相当于xml配置文件什么都没有
   //利用@Bean注解实现配置Bean1的容器管理对象
   @Bean
   public Bean1 initBean1(){
   	//将其生成new
   	Bean1 b1=new Bean1();
   	b1.setAge(18);
   	b1.setName("王翠花");
   	return b1;
   }
   ```

   等价于一个xml配置文件中的

   ```
   <bean class="cn.tedu.bean.Bean1">
   	<property name="name" value="haha"/>
   	<property name="age" value="18"/>
   </bean>
   ```

4. 运行测试

   和之前运行测试spring区别在于不是加载xml配置文件，而是加载的配置类

###### 3.2.3 @ComponentScan

xml配置中，除了bean标签，还有<context:component-scan base-package="cn.tedu">

扫描包路径，只要满足扫描范围的所有类上都可以直接使用@Controller，@Service等注解。

@ComponentScan:代替了上述这个标签，往往都需要配合一个@Configuration的注解实现包扫描

```
在代码中准备一个具有注解的类@Service
@Service
public class Service1 {
	//注入使用
	@Autowired
	private Bean1 b1;
	public String getB1Name(){
	return b1.getName();
}
```

直接启动，通过配置类的加载，无法扫描到当前@Service所在的类,配合@Configuration添加一个ComponentScan指定扫描范围，扫描注解使个类注解，可以一并和配置注解放到一起

###### 3.2.4 阶段总结

上述所有注解@Configuration @Bean @ComponentScan可以实现之前使用xml配置的所有基础逻辑的，将xml完全转向代码来处理spring的配置内容。

##### 3.3 springboot核心注解

###### 3.3.1 @SpringBootApplication

本质上是一个组合注解

@SpringBootConfiguration：相当于是Springboot的@Configuration注解,就是一个配置标识。启动类本身就是一个配置类（相当于需要加载的xml一个配置文件）

@EnableAutoConfiguration：开启自动配置的注解。有了它的存在，springboot当前工程环境的所有配置类扩展代码，就都会被加载根据使用依赖条件判断加载哪些内容。

@ComponentScan：==默认情况会使用当前启动类的所在包（cn.tedu）==作为base-package进行扫描（Controller,Service的注解）。

###### 3.3.2 总结自动配置原理

掌握上述配置中使用的注解的方式

#### 4.springboot的打包运行

如果开发的一个web应用，之前的习惯打成war包，放到tomcat运行。在一个远程服务器中使用linux系统安装tomcat，运行项目war包。

##### 4.1 main入口插件

将当前的quickstart骨架打成的项目jar进行运行，需要在打包同时，填写main方法入口类位置。否则运行时无法找到入口类。需要引入springboot的maven插件

```
<!--maven的springboot打包插件，作用是找到配置main方法-->
<build>
	<plugins>
	<!--springboot插件-->
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

##### 4.2 将项目打包获得jar包

mvn package/install，都会在target 目录中出现jar包

Springboot-demo01-1.0-SNAPSHOT.jar

##### 4.3 在服务器中运行

不需要安装tomcat，所有资源所有逻辑都在jar包中，只需要支持jdk环境即可

调用java的命令 java -jar jar包全路径名称就可以运行起来

C:\Users\Think>java -jar C:\Users\Think\Desktop\springboot-demo01-1.0-SNAPSHOT.jar

## 三、实现order-user项目转化

#### 1.思路

##### 1.1 原工程需要变动地方

1. spring+springmvc+mybatis的xml配置文件消失了

2. 代码controller-service-mapper-domain

   不需要发生变动，搭建好springboot环境后直接粘贴使用

3. 静态文件

   静态文件放到项目中访问，可以使用nginx来管理这些静态文件内容

##### 1.2 实现转化操作步骤

1. 准备好一个springboot工程

2. 具备的环境（依赖资源都有哪些）

   2. spring+springmvc+mybatis
   2. spring+springmvc---spring-boot-starter-web
   3. 持久层：依赖，datasource配置，mybatis配置

4. 粘贴代码

   1. 做好启动类

   2. 将原有代码按照原有结构粘贴到当前工程

      cn.tedu.controller/mapper/domain/service

   3. 粘贴mapper映射文件

4. 静态文件

   2. nginx配置使用静态文件访问
   2. 最终www.pq.com访问到一个springboot启动工程


#### 2.springboot整合order-user

##### 2.1 创建springboot配置环境（以后创建springboot的顺序）

1. 使用quickstart创建工程

2. pom文件修改：

   2. 继承：自动实现的继承自定义的springboot-parent

   2. 依赖：

      2. spring-boot-starter-web
      2. jdbc
      3. mysql
      4. mybatis

      ```
      <!--引入springboot简化依赖 web-->
      <dependency>
      	<groupId>org.springframework.boot</groupId>
      	<artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <!--持久层相关依赖内容-->
      <!--spring-boot-jdbc-->
      <dependency>
      	<groupId>org.springframework.boot</groupId>
      	<artifactId>spring-boot-starter-jdbc</artifactId>
      </dependency>
      <!--mysql-->
      <dependency>
      	<groupId>mysql</groupId>
      	<artifactId>mysql-connector-java</artifactId>
      </dependency>
      <!--mybatis整合springboot依赖-->
      <dependency>
      	<groupId>org.mybatis.spring.boot</groupId>
      	<artifactId>mybatis-spring-boot-starter</artifactId>
      	<version>1.3.0</version>
      </dependency>
      ```

3. application.properties文件

   1. 项目端口8090

   2. 项目根路径/

   3. datasource属性
      1. username
      2. password
      3. url：指向本地数据库microtest
      4. driver
      
   4. mybatis
      1. 扫描映射文件：文件夹名称要注意mapper/mappers
      2. 别名包
      3. 驼峰命名
      4. 关闭缓存
      
      ```
      server.port=8090
      server.context-path=/
      #datasource配置
      spring.datasource.username=root
      spring.datasource.password=root
      spring.datasource.url=jdbc:mysql:///microtest
      spring.datasource.driver-class-name=com.mysql.jdbc.Driver
      
      mybatis.mapper-locations=classpath:/mappers/*.xml
      #别名包
      mybatis.type-aliases-package=cn.tedu.domain
      #开启驼峰命名
      #mybatis.configuration.map-underscore-to-camel-case=true
      #关闭持久层框架缓存
      mybatis.configuration.cache-enabled=false
      ```

4. 编写启动类

   2.  核心注解@SpringBootApplication
   2. 持久层包扫描注解@MapperScan(cn.tedu.mapper)

   ```Java
   package cn.tedu;
   import org.mybatis.spring.annotation.MapperScan;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   @SpringBootApplication
   @MapperScan("cn.tedu.mapper")
   public class StarterOrderUser {
       public static void main(String[] args) {
           SpringApplication.run(StarterOrderUser.class,args);
       }
   }
   ```

##### 2.2 粘贴源码

1. 代码粘贴

   controller service mapper domain

2. 粘贴映射文件

3. 数据库准备（sql文件导入）

   2. 使用sqlYog恢复sql文件

      ![](https://note.youdao.com/yws/api/personal/file/07D8CE33A10D412E93CF8ABA7703A7AF?method=download&shareKey=c93c72dacc05bea4705b20eb75f64cfd)

   2. 选择课前资料的sql文件

      ![](https://note.youdao.com/yws/api/personal/file/0C72E9CD19E04C5CB6A40B8B02BAF7C4?method=download&shareKey=08bbe1fcf73026b0b10bbe576e96e25f)

   3. 执行导入 选择根目录，不要选择到一个已存在数据库进行数据恢复

      ![](https://note.youdao.com/yws/api/personal/file/B52C1BED101F4FF592CD260FB1370728?method=download&shareKey=fd020123d77b4fad8d1532ffd7d8323e)

      ![](https://note.youdao.com/yws/api/personal/file/4E9E44E566AD4538AD130B0EF67D85C0?method=download&shareKey=6c9e4da0122415da2d1c495e2b53b716)

   4. 调整表格与类字段属性对应表格orderMoney-->order_money


##### 2.3 手动测试所有接口

1. 启动系统

2. 执行测试功能订单支付
   1. 代码catch的异常不能做屏蔽，至少打印
   2. 思路分析
      2. 提示空指针异常
      2. 一定代码运行过程有值为null的对象
      3. 就是用debug运行程序
      4. 使用断点检查一下程序中调用方法的对象是否有null
      5. 从数据库利用order_id查询order对象 查不到
      6. 第一次解决没有成功
      7. 问题定位与驼峰命名有关，在类型属性中，不支持_属性名称对应同名的字段
      8. 第二次解决当前mybatis驼峰命名关闭

4. 执行测试功能积分查询

##### 2.4 实现动静分离

最终一定是通过访问页面静态资源，点击页面按钮等等操作，实现js的ajax发送请求，调用到了系统的动态资源。使用nginx完成动静分离，最终www.pq.com。

1. 将页面静态资源保存在nginx管理中,并且可以使用www.pq.com访问到这些静态资源

   2. 将静态资源拷贝到nginx的一个文件夹

      ![](https://note.youdao.com/yws/api/personal/file/E40CA9B37A084030ABB353D0CCA9DF2A?method=download&shareKey=790bcb3b95c3300429c261f88275d52c)

   2. 使用www.pq.com访问到这个页面index.html

      1. www.pq.com](http://www.pq.com) 的hosts文件映射

         127.0.0.1    [www.pq.com](http://www.pq.com)

      2. nginx.conf文件中配置一个server

         ```
         server{
         	listen 80;
         	server_name www.pq.com;
         	location /{
         		root webapp;
         		index index.html
         	}
         }
         ```

   3. 动态资源的访问

      通过页面中的点击发起了ajax异步请求，以积分查询为例

      http://www.pq.com/orderusers/user/point.action

         |hosts文件的ip地址映射127.0.0.1:80

         |nginx监听接收  满足www.pq.com：80

         |location /orderusers

      添加动态转向访问

      ```
      #动态资源访问
      location /orderusers{
      	#请求转向localhost:8090服务器
      	proxy_pass http://localhost:8090/;
      }
      ```

      | 经过location匹配 剩余url字符串 

      |proxy_pass http://localhost:8090/;

      http://localhost:8090/user/point.action?userId=1

      

      订单支付

      http://www.pq.com/orderusers/order/pay.action

      |ip映射访问nginx

      |nginx 监听80端口域名 [www.pq.com](http://www.pq.com)

      |匹配location /orderusers 剩余/order/pay.action

      |拼接proxy_pass http://localhost:8090/;

      http://localhost:8090/order/pay.action

   4. 总结：nginx保存对应的静态数据，配置nginx.conf实现新的server编辑，多个location通过优先级匹配逻辑，将静态资源交给location /将动态资源交给 location /orderusers

      ```
      server {
      	listen 80;
      	server_name www.pq.com;
      	#静态文件访问
      	location /{
      		root webapp;
      		index index.html;
      	}
      	#动态资源访问
      	location /orderusers{
      		#请求转向localhost:8090服务器
      		proxy_pass http://localhost:8090/;
      	}
      }
      ```


