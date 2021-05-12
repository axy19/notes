[TOC]

# SpringCould

## 一、SpringCould概述

#### 1.概括

##### 1.1 什么是微服务

应用增长迅速，功能繁多，用户终端种类庞杂。业务功能编写的特别细致。

微服务概念出现了。

微：相对而言较小，大型业务场景拆分后的独立运行系统，对比之前的功能变小了。

服务：本质上就是拆分出来的独立的应用，每一个应用都可以称为服务。由大量的应用组成的集群，每个应用都要被别人调用---体现被调用的概念。

##### 1.2 微服务特点

1. 独立。为了解决庞大应用中存在的并发问题，耦合问题，一定产生相对独立的系统。
2. 松耦合。
3. 例如：order-user系统，虽然进行纵向拆分，但是并不是微服务结构，可以把order/user称为微服务。存在强耦合---nginx配置文件

##### 1.3 order-user系统

经过拆分后，解决了功能强耦合，并发集中，但是一旦运行，使用这种结构不合理，不规范，没有一个管理的概念。需要引入微服务框架解决这个问题。

#### 2.springcloud微服务框架

##### 2.1 spring的家族

spring,springmvc,springboot,springcloud

spring session,spring-data,spring-security

##### 2.2 spring cloud

不是一向独立的技术（并不是spring团队开发的），只是将一些成熟的继承经过整合，调试，组成了一套可以用来维护监听管理搭建微服务集群的组件。

##### 2.3 spring cloud学习的组件

eureka

ribbon

zuul

config

feign

hystrix

随着学习的进度，慢慢掌握最基本的springcloud微服务框架结构。

深入学习扩展的，完善的结构。单独学习某一个组件时，完全不理解到底在干什么。

## 二、springcould -eureka服务治理组件

#### 1.概括

eureka（我发现了，我找到了）。eureka是springcloud支持的微服务框架中实现服务治理的核心组件。服务治理就是管理微服务集群中的所有微服务（例如，order-user系统拆分之后的order和user就可以在这个组件中实现管理结构）。

#### 2.eureka的实现逻辑

eureka实现了2个核心的功能，一个是**==注册==**，一个是**==发现==**。

eureka的角色：

eureka服务端：提供管理服务的功能，所有eureka客户端都是服务提供者（拆分之后被调用的服务实例）

eureka客户端：就是将拆分之后的每一个服务提供者，注册在eureka服务端，并且可以根据需求，发现所有注册信息

![](https://note.youdao.com/yws/api/personal/file/44C81880812F451895628362D5F3F5CB?method=download&shareKey=670c4ea7a67e9dfd627c64b27e97ce63)

#### 3.测试案例

1. 搭建注册中心（配置基本springcloud搭建项目的环境）

   1. quickstart（eureka-server01）

   2. pom:通过继承springboot-parent标准的springboot环境（==springcloud必须依赖springboot搭建==）

      1. 依赖管理引入springcloud

         ```xml
         <dependencyManagement>
         	<!--实现使用spring cloud所有依赖声明式导入-->
         	<dependencies>
         		<dependency>		                											
         			<groupId>org.springframework.cloud</groupId>
         			<artifactId>spring-cloud-dependencies</artifactId>
         	<!--springboot 1.5.9对应springcloud Edgware Dalston-->
         			<version>Edgware.RELEASE</version>
         			<scope>import</scope>
         			<type>pom</type>
         		</dependency>
         	</dependencies>
         </dependencyManagement>
         ```

      2. 依赖eureka服务端资源

         ```xml
         <!--添加eureka服务端依赖-->
         <dependency>
         	<groupId>org.springframework.cloud</groupId>
         	<artifactId>spring-cloud-starter-eureka-server</artifactId>
         </dependency>
         ```

   3. 准备一个配置文件application.properties

      1. 端口：8761

      2. 配置eureka注册特性

         1. 关闭注册
         2. 关闭发现

      3. 提供一个注册中心的连接地址

      4. 让当前工程在eureka治理组件中底层通信使用ip记录

         ```properties
         #端口 8761
         server.port=8761
         #eureka 客户端配置 默认情况下，服务端进程包含一个客户端
         #eureka.client.fetch-registry表示是否从Eureka Server获取注册信息，默认为true。 如果这是一个单点的 Eureka Server，不需要同步其他节点的数据，可以设为false。 
         eureka.client.fetch-registry=false
         #register-with-eureka属性表示是否将自己注册到Eureka Server，默认为true。由于当前应用就是Eureka Server，因此设为false；
         eureka.client.register-with-eureka=false
         #底层优先使用ip通信
         eureka.instance.prefer-ip-address=true
         #提供注册中心访问的具体地址
         eureka.client.service-url.defaultZone=http://127.0.0.1:8761/eureka
         ```

   4. 启动类

      ```Java
      @SpringBootApplication
      //eureka server开启注解 
      //可以在启动当前工程时利用配置好的资源，启动内部的eureka-server的进程
      @EnableEurekaServer
      public class StarterES01 {
      	public static void main(String[] args) {
      		SpringApplication.run(StarterES01.class,args);
      	}
      }
      ```

2. 访问以下注册中心的web控制台页面http://localhost:8761 直接访问控制台页面，观察当前注册中心的状态看到如下内容 当前注册在eureka中的实例信息（某个服务集群的一员）

   ![](https://note.youdao.com/yws/api/personal/file/3EBB67AC625545B4A20356D755196C6B?method=download&shareKey=bd845d599023f2d7c1b84c3c3c99f7e6)

3. 客户端搭建

   使用客户端资源eureka client搭建一个工程，工程中包含一些功能（简单的请求访问返回字符串），以service-hi服务名称注册在eureka-server

   1. quickstart

   2. pom文件

      1. 导入springcloud依赖

      2. 依赖eureka客户端资源

         ```
         <!--eureka 客户端依赖-->
         <dependency>
         	<groupId>org.springframework.cloud</groupId>
         	<artifactId>spring-cloud-starter-eureka</artifactId>
         </dependency>
         ```

   3. application.properties

      ```properties
      #端口9001
      server.port=9001
      #提供一个该工程服务名称，使用注册在注册中心
      serviceIdspring.application.name=service-hi
      #开启注册，开启抓取的功能：默认就是开启，可以不设置
      #开启ip地址使用优先
      eureka.instance.prefer-ip-address=true
      #指向注册中心的地址实现注册发现
      eureka.client.service-url.defaultZone=http://127.0.0.1:8761/eureka
      ```
   ```
   
   ```
   
4. 启动类
   
      ```java 
      @SpringBootApplication
      //开启是eureka的客户端进程注解
      @EnableEurekaClient
      public class StarterEC01 {
      	public static void main(String[] args) {
      		SpringApplication.run(StarterEC01.class,args);
      	}
      }
   ```
   
5. 在该客户端进程中提供一些可以访问的功能：访问客户端，返回hello字符串
   
      ```Java
      @RestController
      public class HelloController {
      	//模仿该客户端工程的具体功能
      	@RequestMapping("/client/hello")
      	public String sayHello(String name){
      		//通过端口号的标识，区分一下服务被调用时的响应来源
      		return "hello "+name+", i am from 9001";
      	}
      }
   ```
   
6. 使用开发号的client01 创建一个工程client02，启动client02在注册中心，同一个服务名称service-hi下出现2个服务提供者可以通过复制client01实现操作修改部分内容
   
   1. pom文件修改
   
         ```
         <artifactId>spring-cloud-eureka-client02</artifactId>
         <name>spring-cloud-eureka-client02</name>
      ```
   
   2. springboot-parent(自定义聚合工程的moudules标签)
   
      添加一个指向复制的新工程的module
   
   3. 修改client02中代码
   
         2. 启动类名称StarterEC02
         2. controller返回值端口标识9002
      3. application.properties 修改server.port为9002
   
7. 注册中心eureka -server 管理的注册信息非常类似于（upstream）
   
      2. 区别 upstream是静态文件
      2. 注册信息是动态数据


#### 4.eureka治理组件的管理逻辑

eureka治理组件，不同角色的详细功能

##### 4.1 服务端

1. 在客户端访问http://localhost:8761/eureka时，服务器接收请求和客户端参数，实现管理客户端信息的功能，服务端会在底层创建当前注册中心所有服务注册信息的数据结构----双层map。

   ![](https://note.youdao.com/yws/api/personal/file/0F80CC167FB540B6A67527122C67E5F2?method=download&shareKey=beeb660bfa41ba4d11e2b96ba876fa83)

   每个服务的提供者，作为eureka治理组件的客户端进程，携带自身的详细信息，访问

   注册中心的接口地址/eureka，注册中心接收到请求，开始在内存的map对象存储双层map的数据

   第一层map的key值：服务名称

   第一层map的value：就是第二层map的对象

   第二层map的key：就是服务提供者实例名称，默认结构（系统名称：服务名称：端口号）

   第二层map的value：该实例的详细信息 （ip,port,时间戳数据等等。）

   双层map

   Map<String,String> map2=new HashMap<String,String>();

   Map<String,Map<String,String>> map1=new HashMap<String,Map<String,String>>();

   map2.put("实例1","10.9.9.9…");表示map2里保存了一个实例对象，有一个eureka client启动注册

   map1.put("service-hi",map2);

2. 定时判断失效的客户端

   服务端根据客户端发送的心跳检测最后时间戳，==每隔60秒中执行一个定时任务==，判断双层map中第二层map里携带时间戳的值，一旦超过90秒，就会认为client客户端节点失效了，从双层map记录的数据中剔除这个节点。

3. 服务保护模式

   正常情况下，服务提供者（服务实例）是非常庞大的集群，按照故障概率，应该经常会有一部分出现故障，无法满足心跳检测的要求，将会被剔除。但是异常情况下，例如网络波动，导致心跳检测超时的服务实例大量达到超时时间条件。不能再这种情况下将大量实例剔除，eureka-server默认开启一个应对这种场景的非正常剔除的保护模式，防止错误的剔除导致微服务集群的瘫痪。只要集群节点数量超时达到15%以上（在测试环境非常容易达到），保护模式一旦开启，所有服务将不会被剔除，等待网络修复。

   EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.

   可以通过修改eureka服务端的保护模式--关闭。就可以观察超时剔除的现象

   ```
#关闭保护模式
   eureka.server.enable-self-preservation=false
   ```

##### 4.2 客户端功能

1. 注册：客户端在启动时，根据配置的地址service-url.defaultZone发起http请求，访问eureka注册中心携带详细到当前客户端信息。

2. 心跳：启动完成，注册也就完成，后续为了让eureka-server不做剔除处理，==每隔30秒钟，发送心跳续约请求==，携带新的更新数据到eureka-server服务端，服务端接收心跳，修改更新注册信息（时间戳）

3. 发现-同步：客户端一旦开启fetch-registry的功能，每隔30秒中，到注册中心抓取注册信息

   本质上通过网络访问获取的就是双层map的数据，如果客户端在抓取数据后，发现本地已经抓取过数据了，就不会做过多的操作，一旦发现更新内容，将会同步到本地（新服务，老服务剔除）。

#### 5.eureka注册中心的高可用

1. eureka治理组件结构中目前的问题。

   ![](https://note.youdao.com/yws/api/personal/file/35A3914032F24793BCE0729ABA6E44C8?method=download&shareKey=353002eb39b3f804a3ed9ebbfac74838)

   服务端一旦出现故障整个结构不可以使用，瘫痪

   解决思路--一旦是高可用的问题，添加节点，添加角色数量

   ![](https://note.youdao.com/yws/api/personal/file/E9144B962E7645BC8088FCD9461E9927?method=download&shareKey=fa713ecab002e67c1e2dd683f7b63f16)

   通过搭建eureka的多个服务端，实现高可用结构。

2. 本质思路：多个服务端之间相互作为客户端使用的。

   ![](https://note.youdao.com/yws/api/personal/file/8DE0279D7D30470FA0A08BE5C4A65EE1?method=download&shareKey=c1acf3e432d86eb07c0c793a2f68ce39)

   多个注册中心，互相作为服务端，互相作为客户端同步注册和发现，这样所有的当前微服务集群中的服务在2个注册中心中都会保持相同，任何一个出现宕机故障，都可以留下另外一个提供者正常服务。

3. 实现注册中心高可用结构

   配置多个注册中心工程

   1. server01复制

   2. 修改配置参数

      1. pom（拷贝的新工程）文件修改与server01名称相关，编程server02

         ```
         <artifactId>spring-cloud-eureka-server02</artifactId>
         <name>spring-cloud-eureka-server02</name>
         ```

      2. springboot-parent的pom聚合标签

         ![](https://note.youdao.com/yws/api/personal/file/15F243A5C2014B5B832444476DBEC466?method=download&shareKey=3d5c5ba4ea47ac3141ebdd16a8fd7b6b)

      3. 2个注册中心相互注册使用的配置逻辑

         1. 双方添加一个相同的服务名称，在没有提供注册时的服务名称时，统一使用UNKNOWN

         2. server01注册到server02 抓取服务

            ```
            eureka.client.fetch-registry=true
            eureka.client.register-with-eureka=true
            ```

         3. server02注册到server01抓取服务

            ```
            eureka.client.fetch-registry=true
            eureka.client.register-with-eureka=true
            ```

         4. 相互的service-url.defaultZone指向对方

            8761-->8762

            8762-->8761

         5. 测试环境，==这种结构保证关闭保护模式==

      4. 注意启动的异常是正常的（启动某一个注册中心，找不到对方进行注册和发现）

         ```
         Connection refused: connect
         Cannot execute request on any known server
         ```

#### 6.order-user系统中对应eureka组件使用

- eureka注册中心

- eureka客户端：所有服务提供者，服务的进程都是eureka客户端
  - order系统：配置成eureka客户端，注册在注册中心（test-order）
  - user系统：配置成eureka客户端，注册在注册中心（test-user）


#### 7.eureka治理组件总结

微服务框架中，如果仅存eureka治理组件，仅仅多了一个server进程，多了一批注册信息的内容数据结构（双层map），不能够完成调用服务的功能。

## 三、springcloud-ribbon组件

#### 1.ribbon介绍

ribbon是springcloud中进行负载均衡调用服务提供者集群的一个客户端组件，需要依赖eureka-client的发现机制，先从注册中心抓取注册信息，才能根据注册信息实现负载均衡调用（拦截逻辑）

#### 2.搭建一个ribbon测试案例

创建一个项目ribbon，在ribbon中编写一些功能，供测试访问

localhost:9004/ribbon/hello?name=wang

返回一句话，但是这句话实际上是ribbon工程调用了9001/9002（service-hi）

其中一个人帮他完成的

##### 2.1 创建工程配置环境

1. quickstart
2. pom文件
   
   1. 继承springboot
   
   2. 导入springcloud的dependency资源
   
   3. 依赖添加：
   
      1. eureka-client依赖
   
      2. ribbon依赖
   
         ```
         <dependency>			          
         	<groupId>org.springframework.cloud</groupId>
         	<artifactId>spring-cloud-starter-ribbon</artifactId>
         </dependency>
         ```
      ```
      
      ```
      
   3. application.properties
      
      1. 端口：9004
      
      2. 服务名称：service-ribbon
      
      3. 注册和抓取：
      
         抓取必定要开启（抓取的开启是为了调用别人）
      
         注册开启（开启注册是为了让当前客户端功能被别人调用）
      
      4. 注册中心地址：8761
      
   4. 启动类
      
      1. 启动eureka客户端进程
      
      2. 创建RestTemplate对象，并且使用注解配置ribbon逻辑
      
            ```Java
            import org.springframework.web.client.RestTemplate;
            @Bean
            @LoadBalanced 
            //本质是对该对象添加过滤的逻辑，所有这个对象发送的http请求都会被ribbon过滤
            public RestTemplate initTemplate(){
            	return new RestTemplate()；
            }
            ```

##### 2.2 测试案例

要实现在ribbon工程中，使用RestTemplate对象调用service-hi的功能

- 准备一个测试案例结构

  ribbon做一个controller+service的代码

  controller
  
  ```Java
  package cn.tedu.controller;
  
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  import cn.tedu.service.RibbonService;
  
  @RestController
  public class RibbonController {
      @Autowired
      private RibbonService rs;
      //localhost:9004/ribbon/hello?name=wang
      //ribbon也想给它返回一个英文，
      //假设当前ribbon工程不具备说英文的能力，这次请求虽然
      //到达了该ribbon的web应用，无法单独处理功能
      //想方法实现调用service-hi的功能
      @RequestMapping("ribbon/hello")
      public String sayHello(String name){
          //使用业务层返回调用service-hi的结果
          return "RIBBON:"+rs.sayHello(name);
      }
  }
  ```
  
  service
  
  ```Java
  package cn.tedu.service;
  
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Service;
  import org.springframework.web.client.RestTemplate;
  
  @Service
  public class RibbonService {
      @Autowired
      private RestTemplate template;
      public String sayHello(String name){
          //需要在业务层想办法调用service-hi的功能
          //通过治理组件注册发现，可以直接调用service-hi
         String result= template.getForObject
                  ("http://service-hi/client/hello?name="+name,
                          String.class);
         //result hello i am from 9001/9002
          return result;
      }
  }
  ```
  
  启动类
  
  ```Java
  package cn.tedu;
  
  import com.netflix.loadbalancer.IRule;
  import com.netflix.loadbalancer.RandomRule;
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.boot.web.client.RestTemplateBuilder;
  import org.springframework.cloud.client.loadbalancer.LoadBalanced;
  import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
  import org.springframework.context.annotation.Bean;
  import org.springframework.web.client.RestTemplate;
  
  @SpringBootApplication
  @EnableEurekaClient
  public class StarterRibbon {
      public static void main(String[] args) {
          SpringApplication.run(StarterRibbon.class,args);
      }
      //构造一个RestTemplate对象，注入到业务层使用
      //启动类本身是配置类前提，准备一个bean对象，注入使用
  
      @Bean
      @LoadBalanced //本质是对该对象添加过滤的逻辑，所有这个对象发送
      //的http请求都会被ribbon过滤
      public RestTemplate initTemplate(){
          return new RestTemplate();
      }
      //实现自定义的负载均衡计算执行对象
      @Bean
      public IRule initMyRule(){
          return new RandomRule();
      }
  }
  ```

#### 3.当前的项目结构和逻辑

##### 3.1 结构

![](https://note.youdao.com/yws/api/personal/file/AF90C5F73A064E018CABB6979FE2F3F5?method=download&shareKey=89bc0eafd916f97e4fac230f6338942d)

##### 3.2 ribbon负载均衡逻辑和原理

ribbon配合了eureka-client组件，实现了服务的抓取，抓取到注册的所有信息，包含service(9001,9002),然后在ribbon中编写RestTemplate的访问代码实现调用“service-hi”服务，完成负载均衡。底层通过ribbon过滤逻辑。

![](https://note.youdao.com/yws/api/personal/file/2FD050E861C04C1C99B5927EBC3C724E?method=download&shareKey=eb68f8477a95f4a8472c1e97ca803f38)

- ribbon中已经抓取到了服务信息

- RestTemplate调用api时候，就进入到了拦截逻辑

  - 先去对请求地址http://service-hi/client/hello?name=wanglaoshi解析计算
  - 获取了服务名称service-hi
  - 从双层map通过key的服务名称找到了9001/9002详细信息
  - 经过负载均衡计算（IRule的接口实现类做的计算，实现类不同负载均衡逻辑不同）拿到详细信息中某个节点的具体信息ip+port
  - 请求地址中service-hi服务名称替换成了ip:port真实节点信息
  - 发起了从ribbon工程出去的最终访问http://127.0.0.1:9001/client/hello?name=wanglaoshi>

- 扩展问题：

  - restTemplate对象，不能使用域名访问服务http://localhost:9001/client/hello>?

- 当前这种调用负载均衡的方式和nginx比较，有什么优势？

  - eureka治理组件==动态数据维护==，所以在扩展实例提供者时，已有的结构不需要任何变化

    ![](https://note.youdao.com/yws/api/personal/file/426B2BD6DF2244CAA8A07FB2D58CABE4?method=download&shareKey=fd38b0081729d95aa6408340eaf44396)

  - 默认负载均衡是轮询RoundRobinRule，如何实现其他负载均衡逻辑ribbon？

    使用restTemplate调用api后，进行拦截逻辑，实现负载均衡计算，这种负载均衡计算使用的是IRule接口的实现类，实现类不同，负载均衡计算逻辑不同。
    
    | RoundRobinRule           | 默认负载均衡逻辑，轮询                                       |
    | ------------------------ | ------------------------------------------------------------ |
  | RandomRule               | 随机访问                                                     |
    | WeightedResponseTimeRule | 一种权重的计算，根据不同环境下，后端服务实例响应速度动态分片权重比例，后端响应速度越快，权重比例越高，访问负载均衡次数越多，反之相同逻辑 |

    实现配置负载均衡逻辑的自定义

    就是在当前系统中，创建一个内存管理的容器bean对象IRule实现类

    @Bean在配置类中，new一个实现类对象
    
    ```
    //实现自定义的负载均衡计算执行对象
    @Bean
    public IRule initMyRule(){
    	return new RandomRule();
    }
    ```


#### 4.ribbon的作用

以order-user系统结构，讨论可以使用eureka和ribbon实现的方式。

- order-user系统拆分2个工程 order      user

  - order: eureka       client ribbon+restTemplate

    - 支付功能，被别人调用（最前端的js，目前没法实现js调用服务service**）,实现注册（eureka        client）
    - restTemplate，发起请求访问user实现用户积分的新增，相当于在调用别人的服务，实现发现并且利用ribbon做负载均衡

  - user：eureka       client

    - 积分新增：纯粹被调用，实现注册

    - 积分的查询：纯粹被调用（js调用），实现注册

      ![](https://note.youdao.com/yws/api/personal/file/3AC98B18AC0C4BBE8469DA8B9F636109?method=download&shareKey=de2af527fa1662ed0fedba292bd58a03)

## 四、整合order-user

##### 整合组件到项目中

order:eureka ribbon

user:eureka

1. user

   具备了工程，需要在工程中添加组件eureka配置文件，启动类

   - pom文件：
  - 添加springcloud的依赖导入dependencyManagement
     - 依赖eureka（传递了springboot       starter web）
   
- application.properties
     - 端口不变
  - 添加服务名称：test-user
     - 开启注册功能，不开启抓取服务
  - ip优先
     - 指向eureka8761注册中心
  - 持久层不动
       - datasource
    - mybatis
  
- 启动类
     - 添加一个enableEurekaClient注解

2. order：多一个ribbon配置

   - pom文件：

     - springcloud依赖导入
     - 依赖：eureka       ribbon

   - application.properties

     - 端口不变
     - 服务名称：test-order
     - ip优先
     - 指向8761注册中心
     - 持久层不变

   - 启动类

     - 添加eureka客户端注解
     - 调用user服务代码RestTemplate满足ribbon拦截逻辑

   - service代码逻辑修改

     - 不能再使用new的对象，

     - 注入启动类配置的RestTempalte对象

     - 修改发送请求的url地址

       String url="http://test-user/user/update.action?money="+money;

3. 遗留问题

   - 页面访问怎么才能调用服务？？？

     - 能否实现通过js访问http://www.pq.com/orderusers/order/pay.action访问test-order服务
     - 只要实现nginx配置 order系统负载均衡访问
     - 不能通过之前nginx再去访问目前springcloud组件的框架集群

   - 解决思路1

     - 自定义编写一个对外提供访问的工程

       ![](https://note.youdao.com/yws/api/personal/file/FE12D50EA37C4DDB87B5AC3CFCD4C063?method=download&shareKey=a47a1687816fe74caeb951ad325855de)

     - 思路1中提到的网关就是新的组件：zuul


## 五、zuul网关组件

使用eureka和ribbon可以实现一个复杂的，多种多样的微服务集群的搭建，并且可以实现一个功能调用过程，复杂的调用链，但是并不能解决外界调用（因为外界的客户端没有springcloud技术）。所以引入网关组件zuul。

zuul是微服务集群==唯一对外访问的入口工程==（公司很多员工，老师，助教，经理，CEO，人力等等，但是访问老师等内部资源需要有前端咨询老师）。

#### 1.zuul网关实现的功能

1. 路由：zuul接收到外界请求后，根据请求地址(url)判断该请求访问后端哪个微服务。
2. 过滤：不是所有的请求都是合法请求，在过滤过程中，可以根据校验逻辑，判断哪些请求来到网关可以向后调用微服务，不合法的就拒绝了访问。

#### 2.搭建配置zuul网关测试案例

##### 2.1 路由功能

- 搭建zuul网关系统。

  - quickstart

  - pom：

    - 导入springcloud依赖

    - 引入zuul组件

      ```
      <!--zuul 组件-->
      <dependency>				        
      	<groupId>org.springframework.cloud</groupId>
      	<artifactId>spring-cloud-starter-zuul</artifactId>
      </dependency>
    ```
  
  - eureka client
  
- applcation.properties
  
  - 配置路由规则/微服务配置
  
      ```
      zuul.routes.api-a.path=/zuul-a/**
      zuul.routes.api-a.service-id=service-hi
    ```
  
    zuul.routes:路由属性前缀固定
  
    api-a:该路由的一个路由名称（代号），一般都会使用业务功能起名，例如order,user,cart,product等等
  
    path:表示到达zuul网关的访问地址匹配逻辑
  
    service-id:表示同一个路由名称，只要满足了path匹配，就会调用后端服务
  
    上述这一对路由匹配：在zuul当中，自定义了一个路由规则，叫做api-a，凡是到达zuul网关的请求url满足path路径匹配，就会被zuul调用service-hi来处理。
  
    path匹配规则满足ANT规范的。
  
      | ？   | 表示一个字符，/zuul-a/?可以匹配到的路径<br/>/zuul-a/a,/zuul-a/b,匹配不到/zuul-a/abc |
      | ---- | ------------------------------------------------------------ |
      | *    | 表示一个字符串，/zuul-a/*可以匹配到路径/zuul-a/a,/zuul-a/abc,匹配不到多级/zuul-a/a/b/c |
    | **   | 表示任意多级的，任意字符串长度，/zuul-a/**可以匹配到所有     |
  
  - 端口8103：后续easymall的nginx就是8103
  
- 启动类
  
    - @SpringBootApplication
    - @EnableEurekaClient
  - @EnableZuulProxy
  
- 测试访问
  
  - 通过访问zuul理解路由匹配规则，理解流转逻辑
  
    起始访问zuul网关http://localhost:8103/zuul-a/client/hello>
  
    |到达网关，接收到url域名端口剩余/zuul-a/client/hello
  
    |做路由的path匹配，/zuul-a/** 去掉匹配规则字符串/zuul-a/
  
    |剩余/client/hello，拼接到service-hi服务名称下
  
    |最终访问到http://service-hi/client/hello的请求，之后就是ribbon的逻辑>
  
    zuul网关中==转发调用后端服务使用的是ribbon组件==
  
    |拦截请求，解析服务名称service-hi
  
    |根据抓取的服务信息service-hi拿到服务列表9001/9002做负载均衡http://127.0.0.1:9001/client/hello地址>
  
  - 让zuul网关实现一个第二个路由规则，path满足/zuul-b/**      路由名称api-b
  
    访问到service-ribbon /ribbon/hello
  
    zuul.routes.api-b.path=/zuul-b/**
  
    zuul.routes.api-b.service-id=service-ribbon
  
    http://localhost:8103/zuul-b/ribbon/hello?name=王>
  
    http://localhost:9004/ribbon/hello?name=王>
  
- 路由匹配相关的问题
  
    - 如果路由匹配的path规则具备包含关系
      - a.path=/zuul-a/*
    - b.path=/zuul-a/**
  
- http://localhost:8103/zuul-a/haha>， 到底交给a路由处理，还是交给b的路由处理
  
    - 在程序加载路由规则时，根据加载的顺序，判断优先级高低，
    - 先加载的配置在内存中key-value数据位置靠上，优先级就高。
    - properties:加载配置到内存，没有根据先后上下顺序去加载，底层是无序map HashMap
    - yml：格式配置在上的属性，可以先加载到内存，底层是有序map（LinkedHashMap）

##### 2.2 将order-user系统整合加上zuul网关

###### 2.2.1 完整结构

![](https://note.youdao.com/yws/api/personal/file/7FBDED269A684A4A83B7961B71111661?method=download&shareKey=7884f8ca344423983fac4c6522da4fc4)

在网关中配置不同的请求地址访问不同服务的路由规则

order规则

user规则

nginx访问发送出来的请求地址满足zuul网关定义的path路径

###### 2.2.2 配置order-user的网关规则

- order路由规则

  ```
  zuul.routes.t-order.path=/zuul-o/**
  zuul.routes.t-order.service-id=test-order
  ```

- user路由规则

  ```
  zuul.routes.t-user.path=/zuul-u/**
  zuul.routes.t-user.service-id=test-user
  ```

###### 2.2.3 nginx配置文件

流转逻辑：

js发起请求地址

<http://www.pq.com/orderusers/order/pay.action>

|ip映射找到了nginx，进入nginx

|nginx匹配端口域名，剩余/orderusers/order/pay.action

|location ^~/orderusers/order 剩余/pay.action

|proxy_pass <http://localhost:8103/zuul-o/order/>

zuul网关接收地址

<http://localhost:8103/zuul-o/order/pay.action>

|匹配path /zuul-a/** 剩余/order/pay.action

|找到路由规则 t-order 拼接test-order服务

|http://test-order/order/pay.action

|拼接服务信息详细信息 <http://127.0.0.1:8091/order/pay.action>

所以说，后端某一个微服务的具体访问路径，经过nginx匹配过滤，经过zuul匹配过了，可以做到完全对外界是屏蔽的。想要调用服务功能必须通过nginx，必须通过网关。

NGINX配置修改

```
server {
	listen 80;
	server_name [www.pq.com](http://www.pq.com);
	#静态文件访问
	location /{
		root webapp;
		index index.html;
	}
	location ^~/orderusers/order{
		proxy_pass <http://localhost:8103/zuul-o/order/>;
	}
	localhost ^~/orderusers/user{
		proxy_pass <http://localhost:8103/zuul-u/user/>;
	}
}
```

#### 3.检查微服务功能问题

无论测试任何功能出现问题，解决定位问题的思路：

![](https://note.youdao.com/yws/api/personal/file/9EED4D7A07DF4927977F691C2AE83BAF?method=download&shareKey=65cbba568a726555caa5a834bcde69d8)

第一步：先检测最后端的服务提供者是否能正常访问

<http://localhost:8091/order/pay.action> 看看是否响应正常

第二步：保证第一步正常的情况下，测试访问网关

<http://localhost:8103/zuul-o/order/pay.action> 看看响应是否正常

第三步：保证第二步测试时正常的，测试nginx访问

<http://www.pq.com/orderusers/order/pay.action> 看看响应是否正常

zuul网关常见的问题：

Forwarding error

load balancer dose not have avalibal service：服务名称

原因1：zuul启动时先要去抓取注册中心的所有微服务，根据路由调用某个微服务。一旦zuul启动顺序在某个微服务之后去启动，启动时无法抓取到该服务信息，前面调用到达网关之后，找不到该微服务。可以等一段时间。

原因2：路由配置内容错误的。例如：服务名称service-ribbon, service-ribon

## 六、spring cloud的其他组件

#### 1.回顾已学过的组件

##### 1.1 eureka组件

![](https://note.youdao.com/yws/api/personal/file/5D45DF5579724163845BCC839A228924?method=download&shareKey=493ff229f539f1d1efa64466b9e63495)

##### 1.2 ribbon组件

![](https://note.youdao.com/yws/api/personal/file/9BD2EF3FF6A9431CB4617D1AA21A481B?method=download&shareKey=e517e3feac29a2aec63d5fa027f44553)

##### 1.3 zuul网关组件

![](https://note.youdao.com/yws/api/personal/file/CC2459C43EA5402B850632D0BD0A1444?method=download&shareKey=20f261e33335151be48c27a0c4c15182)

##### 1.4 存在的不足

###### 1.4.1 配置文件重复内容太多

application.properties大部分系统都使用了重复的配置文件。

###### 1.4.2 没有能力解决访问微服务提供者的故障问题

故障出现，会有请求积压，导致一个请求链的瘫痪。

#### 2.spring cloud config组件

##### 2.1 概述

微服务框架中,微服务提供者搭建环境时,总是会出现大量properties重复配置,springcloud提供了一个高可用访问的配置中心config,实现对配置文件内容的类型,标签分类管理。

![](https://note.youdao.com/yws/api/personal/file/DA59A8433383431BBFB7FEAE77614281?method=download&shareKey=e260307fbfe961886a7690210d037e8a)

根据技术的配置内容,划分功能/技术,独立配置文件,共享使用。

##### 2.2 config组件的角色

2个角色(eureka类似)

1. server端:从共享存储的资源服务器(github/gitee),git资源版本控制服务器获取资源(各种不同的公用配置文件)
2. client端口:需要获取公用配置的服务提供者系统,从server按照接口要求,访问获取所有的yml/properties数据;

如果想实现config配置中心高可用,需要配置中心注册在eureka,client通过服务名称访问server

##### 2.3 案例

###### 2.3.1 配置中心工程config server(服务端)

配置中心读取网络共享文件(位置github/gitee)

github/gitee:商用的git服务器(资源版本控制服务器)

![](https://note.youdao.com/yws/api/personal/file/4C375C03498A4EE7AE7C6E3EB2E0671A?method=download&shareKey=91a908a1a4d0a8a021bcd3a9c6ce1df0)

实现步骤

1. 搭建配置中心读取gitee数据 

   - quickstart

   - pom

     - 继承parent  

     - 依赖config-server  

       ```
       <dependency>               
       	<groupId>org.springframework.cloud</groupId>
       	<artifactId>spring-cloud-config-server</artifactId>
       </dependency>
       ```

     - starter-web(补充jackson)   
     - 导入spring cloud 依赖资源 

   - application.properties

     ```properties
     //连接git仓库的url地址
     spring.cloud.config.server.git.uri=https://gitee.com/xiaoxuwei1986/1905emconfig
     //仓库中可以直接访问配置文件properties/yml
     //一般使用文件夹维护不同工程不同功能的文件夹名称
     spring.cloud.config.server.git.searchPaths=easymall
     //如果仓库是公用public资源,不需要提供用户名密码,如果private需要用户名密码
     #如果读取的是私有文件，需要提供登录名密码
     spring.cloud.config.server.git.username=
     spring.cloud.config.server.git.password=
     ```

   - 启动类

     ```Java
     package cn.tedu;
     import org.springframework.boot.SpringApplication;
     import org.springframework.boot.autoconfigure.SpringBootApplication;
     import org.springframework.cloud.config.server.EnableConfigServer;
     import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
     @SpringBootApplication
     @EnableConfigServer
     @EnableEurekaClient
     public class StarterConfigServer {
         public static void main(String[] args) {
             SpringApplication.run(StarterConfigServer.class,args);
         }
     }
     ```

2. 测试访问远程共享中心配置文件的几种接口方式（请求url格式）： 

   通过访问配置中心application-profile的文件名称结构

   根据接口格式可以获取资源数据

   application: 配置文件按照功能,种类切分后的名称,例如

   redis,datasource,rabbitmq

   profile:每一个不同功能和种类的配置文件对应的标签,

   可以表示不同的使用环境,例如:redis-ceshi01.properties,redis-ceshi02.properties,redis-shengchan01.properties

   lable:使用git上分支名称(这里都是master)

3. 基本访问结构（可以直接访问server的页面后边拼接如下内容，即可访问source资源）：

   redis.properties(application名称 profile=default)

   redis-test1.properties(application-profile)

   - /{application}/{profile}/[{label}]   
   - /[{label}]/{application}-{profile}.yml/properties  

   redis-test1.properties

   <http://localhost:8098/redis/test1/master>

   <http://localhost:8098/master/demo01-test.properties>

   发现效果，可以根据接口中按照config-server定义的结构赋值application名称，profile名称访问文件内容。redis.properties也返回了。

   只要请求中满足上述接口，config-server会先去比对application-profile再去比对application,将满足redis但是没有profile名称的properties一并返回。对于一个只使用application的名称的配置文件，可以定义一些当前分类中的公用配置。

   例如： redis.properties:定义最大连接，最小连接，最大空闲

   redis-test1.properties:定义test1环境的nodes连接

   redis-test2.properties:定义test2环境的nodes连接

   demo01-test01-version01.properties（三级连接）：

   1. 后边两个看成一个整体 <http://localh ost:8098/demo01/test01-version01>
   2. 前边两个看成一个整体：http://localhost:8098/demo01-test01/version01> 

4. 公用配置文件

   在连接到git中心的位置如果存在一个application.properties的文件,直接读取(公用文件一种方式),无论客户端想要配置访问的资源文件是哪个,都会自动读取这个文件中的内容,强制将数据传递给客户端访问。

###### 2.3.2 配置中心客户端

客户端系统从config配置中心读取gitee的配置文件(按需求读取)。

可以通过配置内容指定读取datasource,demo01-test.properties。

配置中心客户端使用的读取的配置文件不再是application.properties而是bootstrap.properties(加载顺序在application.properties之前)。

步骤：

1. quickstart

2. pom

   - 继承parent 

   - 依赖config

     ```
     <dependency>          
     	<groupId>org.springframework.cloud</groupId>
     	<artifactId>spring-cloud-starter-config</artifactId>
     </dependency>
     ```

   - bootstrap.properties(bootstrap.properties是先于application.properties加载的启动进程使用的配置文件,application.properties启动web进程) 用于配置客户端信息，与application.properties不冲突，一样的会被application.properties会把相同的属性覆盖掉。

     ```
     server.port=9998
     spring.application.name=config-client
     eureka.client.service-url.defaultZone=http://localhost:8761/eureka
     #当前工程作为客户端读取公用配置文件
     #按照需要指定读取application名字
     spring.cloud.config.name=redis,datasource,mybatis,elastcisearc,rabbitmq
     #profile
     spring.cloud.config.profile=test1
     spring.cloud.config.label=master
     #指定配置中心config-server连接地址
     #spring.cloud.config.uri=http://localhost:9999/
     #config客户端要以服务抓取的方式，获取config-server服务
     #负载均衡访问
     spring.cloud.config.discovery.enabled=true
     spring.cloud.config.discovery.service-id=config-server
     #发起请求http://localhost:9999/redis/test1/master
     date=2020
     ```

     ```
     spspring.application.name=servicename//默认情况下服务名称就是要读取配置文件的application名称
     spring.cloud.config.name=redis,datasource,mybatis
     spring.cloud.config.profile=test //profile的文件名称,指定多个,不配置,default
     spring.cloud.config.label=master //多个分支的名称,默认就是master
     spring.cloud.config.uri=http://localhost:8098 //配置中心
     server.port=8099
     //类似于发送请求：http://localhost:8098/redis/test/master
     ```

   - 启动类

     @SpringBootApplication

   - 测试案例---证明读取了想要的数据

     ```Java
     package cn.tedu;
     import org.springframework.beans.factory.annotation.Value;
     import org.springframework.boot.SpringApplication;
     import org.springframework.boot.autoconfigure.SpringBootApplication;
     import org.springframework.web.bind.annotation.RequestMapping;
     import org.springframework.web.bind.annotation.RestController;
     @SpringBootApplication
     @RestController
     public class StarterConfigClient {
         //验证读取到的properties属性在本地工程能否
         //正常使用
         @Value("${date}")
         private String date;
         @Value("${profile}")
         private String rand;
         public static void main(String[] args) {
             SpringApplication.run(StarterConfigClient.class,args);
         }
         @RequestMapping("profile")
         public String profile(){
             return rand;
         }
         @Value("${name}") 
         private String name;
         @RequestMapping("name")
     		public String getName(){
     			return name;
     		}
     }
     ```

3. 客户端只能通过访问与服务名称相同的application 学习配置客户端的properties属性解决。

   默认情况下,使用服务名称赋值application;

   spring.cloud.config.name=a,b,c,d 多个赋值,表示当前客户端读取配置中心中扫描范围内容所有以 a-*.properties,b-*.properties,c-*.properties的文件

4. 配置中心没有实现高可用(依赖eureka治理组件完成)
   

![](https://note.youdao.com/yws/api/personal/file/18E134BCA99C401189AC43EDEAF7222A?method=download&shareKey=4198cdfc1b4100173c230a9d6e93fc3a)

将eureka客户端组件添加到配置中心步骤：

1. 添加一个依赖 
   
   ```
   <dependency>                
      	<groupId>org.springframework.cloud</groupId>
      	<artifactId>spring-cloud-starter-eureka</artifactId>
   </dependency>
   ```
   
2. application.properties中添加注册地址,添加服务名称 
   
3. 启动类@EnableEurekaClient 
   
   在客户端不能以具体的url地址localhost:8098访问配置中心,以服务名称访问。
   
4. eureka客户端配置 
   
   依赖
   
   启动注解
   
5. 客户端bootstrap.properties修改访问config-server的方式 
   
   ```
      # eureka server
      eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
      # enbale server name ask configserver
      spring.cloud.config.discovery.enabled=true
      spring.cloud.config.discovery.serviceId=config-server
   ```

#### 3.feign组件

和ribbon的关系:feign是负载均衡访问/调用微服务客户端组件.

底层封装了ribbon+restTemplate实现调用,并且利用声明式的==注解==来调用,一般feign的作用是--做公用服务调用统一的入口.能使用ribbon的地方,都能替换成feign,反之亦然。

##### 3.1 开发一个feign客户端工程

1. quickstart

2. pom

   - 继承
   - 依赖feign组件
   - 依赖starter-web
   - 依赖eureka-client

   ```
   <dependencies>
   	<!--feign组件-->
   	<dependency>
   		<groupId>org.springframework.cloud</groupId>
   		<artifactId>spring-cloud-starter-feign</artifactId>
   	</dependency>
   	<dependency>
   		<groupId>org.springframework.cloud</groupId>
   		<artifactId>spring-cloud-starter-eureka</artifactId>
   	</dependency>
   </dependencies>
   <dependencyManagement>
   	<dependencies>
   		<dependency>
   			<groupId>org.springframework.cloud</groupId>
   			<artifactId>spring-cloud-dependencies</artifactId>
   			<version>Edgware.RELEASE</version>
   			<scope>import</scope>
   			<type>pom</type>
   		</dependency>
   	</dependencies>
   </dependencyManagement>
   ```

3. application.properties

   - ribbon一样  
   - 注册中心地址  
   - 端口  
   - 服务名称

   ```properties
   #端口9001
   server.port=9005
   server.context-path=/feign
   #提供一个该工程服务名称，使用注册在注册中心 serviceId
   spring.application.name=service-feign
   #开启注册，开启抓取的功能
   #开启ip地址使用优先
   eureka.instance.prefer-ip-address=true
   #指向注册中心的地址实现注册发现
   eureka.client.cn.tedu.service-url.defaultZone=http://127.0.0.1:8761/eureka
   ```

4. 启动类

   注解spring boot

   eurekaclient

   @EnableFeignClients
   
   ```Java
   package cn.tedu;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
   import org.springframework.cloud.netflix.feign.EnableFeignClients;
   @SpringBootApplication
   @EnableEurekaClient
   @EnableFeignClients//进程将会以feign客户端启动
   public class StarterFeign {
       public static void main(String[] args) {
           SpringApplication.run(StarterFeign.class,args);
       }
   }
   ```
   
5. controller

   ```java
   package cn.tedu.controller;
   import cn.tedu.service.HelloService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   @RestController
   public class HelloController {
       @Autowired
       private HelloService helloService;
       @RequestMapping("hello")//9005/hello
       public String sayHi(String name){
           return "FEIGN:"+helloService.sayHi(name);
       }
   }
   ```

6. service

   ```Java
   package cn.tedu.service;
   
   import com.sun.org.glassfish.gmbal.DescriptorFields;
   import org.springframework.cloud.netflix.feign.FeignClient;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   
   /**
    * feign作为负载均衡调用服务客户端
    * 可以通过注解形式实现底层代码封装
    * 不需要手动编写http请求代码不用写restTemplate
    * 配合Springmvc的注解实现
    */
   @FeignClient("service-hi")//表示该接口交给feign组件
   //实现底层逻辑，并且该接口的实现类中，所有方法都需要
   //访问service-hi这个服务
   public interface HelloService {
       /*
       即使使用注解实现访问服务指定，service-hi，对着服务后端
       提供者中这可能有好几个访问的接口 service-hi/hello
       service-hi/haha service-hi/kaka springmvc注解实现更详细的请求
       方法定义
       @RequestMapping 定义uri method
       实现方法：restTemplate.getForObject("http://service-hi/client/hello")
       @RequestParam 定义请求参数
       实现方法：restTemplate.getForObject("http://service-hi/client/hello?name="+name,String.class)
        */
       @RequestMapping(value="/client/hello",method = RequestMethod.GET)
       String sayHi(@RequestParam("name")String name);
       //这个接口中的所有方法，都和被访问的工程中的controller方法
       //完全一致
       /*
       @RequestMapping(value="/client/hello",method = RequestMethod.GET)
       public String sayHello(@RequestParam("name") String name){
        */
   }
   ```

##### 3.2 注解使用

在feign工程也去实现服务调用服务的功能,通过访问feign工程实现后端9001/9002的调用

类注解:FeignClient(当前类访问拼接的服务名称)

方法注解:

RequestMapping:定义访问后端服务的url接口和访问的方式

RequestParam/RequestBody:定义参数携带的名称和方式

##### 3.3 feign和ribbon的对比

1. ribbon:可以在业务层中对于调用的前后逻辑做封装。

2. feign:服务调用过程只能通过注解实现,调用单一。

3. 应用场景:

   1. ribbon哪都能用(代码复杂)

   2. feign==一般使用在公用服务的调用入口编程==,feign的业务层实现代码中使用的注解,几乎和后端调用服务的注解相同(service-->后端controller)

      ![](https://note.youdao.com/yws/api/personal/file/7BD316C23D2E4B2AB8D34D4604165C12?method=download&shareKey=68d1f693bbc4bc777c6c1ef4998b0903)

4. feign客户端中自动支持,熔断机制的断路器和服务降级

#### 4.hystrix组件

##### 4.1 熔断机制

在微服务集群中存在大量的功能时，调用链，调用逻辑可能会出现非常复杂的情况，同时也经常会出现同一个服务被不同的服务调用。被调用服务的实例故障等问题会出现微服务集群的崩溃。为了解决一个服务中某个服务实例故障或不可达导致的请求压力积压问题,引起的一个服务调用链瘫痪,需要引入熔断机制。

![](https://note.youdao.com/yws/api/personal/file/002A24B31803444A9AB74131B9B01C5E?method=download&shareKey=4d243283e2fda8e78ecce96559db9095)

![](https://note.youdao.com/yws/api/personal/file/47F66956AA1A4CB7A514D6D9C4560E72?method=download&shareKey=410242d1f91653024e1e059beb1cf49b)

##### 4.2 熔断机制的内容

工作原理:牺牲局部保存全局,退而求其次的获取响应结果

1. 断路器

   直接牺牲（屏蔽）后端不用的服务提供者,不在访问该服务实例。

   断路器的三种状态：

   - 断路器打开:断路器一旦打开,后端服务提供者就不会被访问
   - 断路器半开:断路器尝试将一部分请求发送给故障后的服务实例(做判断)
   - 断路器关闭:直接连接后端服务实例访问

   ![](https://note.youdao.com/yws/api/personal/file/18E0B7FDFD43422ABBB02B5248F6B10B?method=download&shareKey=b08d1f6375a9afbbd2c8888654847660)

2. 服务降级

   断路器在牺牲局部的原则下使得大量的访问不会通过故障的服务实例返回请求,总会出现部分请求访问超时,断开,不正常--服务降级,当该服务故障后,依然通过的各种访问请求,一个退而求其次的响应
   
   ![](https://note.youdao.com/yws/api/personal/file/73D044949F3A41E1B34431E38683C5CC?method=download&shareKey=438c92c8a432df1ba24e331fb6a0ceaa)

##### 4.3 基于ribbon功能添加熔断器hystrix实现断路器和服务降级

1. pom添加hystrix的依赖

   ```
   <!--hystrix 熔断-->
   <dependency>
   	<groupId>org.springframework.cloud</groupId>
   	<artifactId>spring-cloud-starter-hystrix</artifactId>
   </dependency>
   ```

2. 启动类中添加断路器注解开启熔断机制

   @EnableCircuitBreaker

   ```Java
   package cn.tedu;
   
   import com.netflix.loadbalancer.IRule;
   import com.netflix.loadbalancer.RandomRule;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.boot.web.client.RestTemplateBuilder;
   import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
   import org.springframework.cloud.client.loadbalancer.LoadBalanced;
   import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
   import org.springframework.context.annotation.Bean;
   import org.springframework.web.client.RestTemplate;
   
   @SpringBootApplication
   @EnableEurekaClient
   @EnableCircuitBreaker//断路器注解
   public class StarterRibbon {
       public static void main(String[] args) {
           SpringApplication.run(StarterRibbon.class,args);
       }
       //构造一个RestTemplate对象，注入到业务层使用
       //启动类本身是配置类前提，准备一个bean对象，注入使用
   
       @Bean
       @LoadBalanced //本质是对该对象添加过滤的逻辑，所有这个对象发送
       //的http请求都会被ribbon过滤
       public RestTemplate initTemplate(){
           return new RestTemplate();
       }
       /*//实现自定义的负载均衡计算执行对象
       @Bean
       public IRule initMyRule(){
           return new RandomRule();
       }*/
   }
   ```

3. controller

   ```Java
   package cn.tedu.controller;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import cn.tedu.service.RibbonService;
   @RestController
   public class RibbonController {
       @Autowired
       private RibbonService rs;
       //localhost:9004/ribbon/hello?name=wang
       //ribbon也想给它返回一个英文，
       //假设当前ribbon工程不具备说英文的能力，这次请求虽然
       //到达了该ribbon的web应用，无法单独处理功能
       //想方法实现调用service-hi的功能
       @RequestMapping("ribbon/hello")
       public String sayHello(String name){
           //使用业务层返回调用service-hi的结果
           return "RIBBON:"+rs.sayHello(name);
       }
   }
   ```

4. service的方法上添加服务降级的注解

   ```java
   package cn.tedu.service;
   
   import com.netflix.hystrix.contrib.javanica.annotation.HystrixCollapser;
   import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   import org.springframework.web.client.RestTemplate;
   
   @Service
   public class RibbonService {
       @Autowired
       private RestTemplate template;
       /*
           引入服务降级的处理逻辑
        */
       @HystrixCommand(fallbackMethod = "error")//断路或者故障后
       //fallback回调方法 方法名称error，需要根据配置名称
       //在本类中实现一个和调用方法结构完全一致的方法
       public String sayHello(String name){
           //需要在业务层想办法调用service-hi的功能
           //通过治理组件注册发现，可以直接调用service-hi
          String result= template.getForObject
                   ("http://service-hi/client/hello?name="+name,
                           String.class);
          //result hello i am from 9001/9002
           return result;
       }
       //服务降级后备用调用方法
       @HystrixCommand(fallbackMethod = "sayHi")
       public String error(String name){
           //可以在这个方法里编写备用逻辑，连接备用
           //数据库，备用redis
           //需要在业务层想办法调用service-hi的功能
           //通过治理组件注册发现，可以直接调用service-hi
           String result= template.getForObject
                   ("http://service-hi/client/hello?name="+name,
                           String.class);
           //result hello i am from 9001/9002
           return result;
       }
   }
   ```

##### 4.4 面试题

微服务集群中如果登陆集群的节点全部宕机,如何处理?(服务降级)

思路:

熔断机制描述

断路器

服务降级

通过服务降级的逻辑当所有节点不可达时,可以通过(HystrixCommand注解的自定义实现),完成多级降级的自定义逻辑

可以实现当一个微服务集群中的服务全部宕机后,启动后备降级服务(解决极端现象的办法)