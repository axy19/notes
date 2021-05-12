[TOC]

# rabbitmq

## 一、秒杀概述

#### 秒杀系统结构

秒杀功能最大特点是瞬间高并发，这些高并发在服务器处理能力之内可以不引入消息队列，直接处理所有请求的入库逻辑，实现成功秒杀用户信息的入库和失败的返回。处理能力之外（并发在同一时刻无法处理如此之高的请求数量）。引入缓存队列机制---消峰的机制

![](https://note.youdao.com/yws/api/personal/file/WEBeac7b4c28339abb19a1ca2e5fc3cac6b?method=download&shareKey=75a45a31e45829dccd9686a7374a62bf)

消息队列引入，可以在系统内部实现排队缓存机制，使得大量并发请求经过第一个系统后不直接处理操作后端数据层逻辑，提升接收请求的处理速度。将系统资源处理请求的时间减少，提升单位时间系统的并发数量。从而解决瞬间高并发。

## 二、消息队列

#### 1.消息队列历史

##### 早期消息队列解决通信解耦

A,B两个系统进行数据通信，A要将数据传递给B事实现计算，B要计算结果返回给A使用

![](https://note.youdao.com/yws/api/personal/file/WEB541b8500206aff663fec351ba4891194?method=download&shareKey=0cde4ec00a1fbb48c5a51bedceedfa1d)

无论A传递数据还是B返回计算结果，有任何一步骤失败，导致整个通信流程重新来。

 ![](https://note.youdao.com/yws/api/personal/file/WEB6f7a343ac9a98a6281f89654366368f3?method=download&shareKey=fa96feadb18fc3e9479c1f04d1ad2238)

#### 2.rabbitmq

##### 2.1 介绍

rabbitmq是企业级别，实现了sun提出的AMQP（advanced message queue protocal）协议的一个高级消息队列。

消息队列产品

rabbitmq

rocketmq

kafka

JMS：性能比较低

##### 2.2 rabbitmq的结构

###### 2.2.1 客户端角色

rabbitmq支持很多语言连接操作rabbitmq成为客户端，角色不同取决于客户端使用rabbitmq实现的功能

1. 生产端：发送消息的客户端product
2. 消费端：连接消息队列，接受消费消息的客户端consume

###### 2.2.2 核心组件

1. 连接组件：长连接，短连接

   ![](https://note.youdao.com/yws/api/personal/file/WEBb9b6fdc8e56302293a0330116418397e?method=download&shareKey=567bf4e1a38cba172f395ac6e4e76607)

   长连接是基于TCP/IP协议创建消耗资源，不能频繁的创建销毁，一般一个进程都会绑定给一个长连接。短连接基于长连接创建，消耗资源有限，适合客户端使用完毕销毁的情况。（长连接就是高速公路，短链接就是车）

2. 交换机：exchange

   RabbitMQ接收到消息之后通过交换机exchange转发到对应的队列上面。exchange组件是使用并发语言erlang实现的。

3. 队列：queue

   是rabbitmq存储消息组件，可以在内存存储，也可以实现持久化。对接生产端时，队列从生产端发送到交换机中获取信息，消费端并发没有生产端集中和复杂，消费绑定监听队列实现消费。

   ![](https://note.youdao.com/yws/api/personal/file/WEB9e3c7719e44e57f82d72c4d9c8dd1923?method=download&shareKey=132f90c32135b90d4a35267b7b2d8b59)

#### 3.rabbitmq的安装和启动使用

##### 3.1 安装rabbitmq（一台）

直接使用rpm的安装命令。安装包/home/resources。

由于rabbitmq安装核心组件exchange是使用erlang语言开发，需要在

安装rabbitmq之前准备对饮高版本的erlang环境

![](https://note.youdao.com/yws/api/personal/file/WEBa2447b149f3aa3c3cc109dcb7b66f73f?method=download&shareKey=f36d63bd62176d4ce5cef80e28f075fe)

云主机上安装了erlang21.0版本。

```
[root@10-9-104-184 resources]# rpm -ivh rabbitmq-server-3.7.7-1.el6.noarch.rpm 
```

##### 3.2 启动rabbitmq

默认的用户guest/guest登录rabbitmq使用rabbitmq资源，但是只允许本地登录使用这个用户。

1. 启动

   rabbitmq

   1. [root@10-9-104-184  resources]# service rabbitmq-server start

      success表示rabbitmq启动成功

   2. 以服务形式启动如果出现任何问题（需要调整用户权限）。可以到  rabbitmq的安装目录找bin文件夹，命令脚本启动

      /usr/lib/rabbitmq/ 是软件的根目录，到bin下启动

      [root@10-9-104-184 bin]#  ./rabbitmq-server start

      [root@10-9-104-184  bin]# ./rabbitmq-server -detached

2. 调整环境配置

   1. rabbitmq提供了一个web控制台，需要在启动rabbitmq之前开启控制台插件  

      [root@10-9-104-184 bin]#  rabbitmq-plugins enable rabbitmq_management

   2. 准备配置文件  

      有一个模板配置文件：/usr/share/doc/rabbitmq-server-3.7.7/rabbitmq.config.example

      将这个模板文件拷贝到/etc/rabbitmq 修改名字rabbitmq.config

      [root@10-9-104-184  rabbitmq-server-3.7.7]# cp  /usr/share/doc/rabbitmq-server-3.7.7/rabbitmq.config.example  /etc/rabbitmq/rabbitmq.config

   3. 修改rabbitmq.config 

      61行，将user限制打开，设置为空

      ![](https://note.youdao.com/yws/api/personal/file/WEBfca5a8ef0f24bfbd31de0ca5fad6a937?method=download&shareKey=9b2137874b6f7f86b9f4533720fa1f53)

3. 重启rabbitmq  

   使用guest/guest就可以登录到rabbitmq，观察控制台中的内容

   登录控制台的地址 服务器ip地址:15672 web服务台访问端口。后续使用代码5672

## 三、rabbitmq五种工作模式

#### 1.概括

消息队列使用过程中，可以根据不同场景选择不同的消息队列模式使用消息队列。

#### 2.五种工作模式和使用场景

##### 2.1 简单模式

强调的不是生产端发送消息的逻辑，强调消费端监听的逻辑。

![](https://note.youdao.com/yws/api/personal/file/WEB76377a5c16c8cd287b95d196c6ba42d0?method=download&shareKey=0f92bc18ac7fac43a222e87b9c462724)

生产端：封装整理消息，连接rabbtimq将消息发送给交换机

交换机：接收所有当前连接的生产端发送的消息，根据消息的参数决定后端发送连接哪个队列去处理该消息。如果找不到任何队列处理消息，消息自动删除。

队列：生成时，就会绑定交换机，负责处理消息存储

消费端：简单模式中，一个队列只被一个消费端监听，当队列有消息时，消费者就会拿走消息进行处理

应用场景：邮件，短信，聊天

##### 2.2 工作模式（争抢模式）

强调的也不是生产端如何发送消息的逻辑，交换机如何判断消息交给那个队列。

强调的是多个消费者同时监听一个队列，形成消息争抢。

![](https://note.youdao.com/yws/api/personal/file/WEBd255e8cf6eb579c68f9931611c7c8ed8?method=download&shareKey=a3c170860345cbb2570e36a4006a37ab)

生产端和交换机：生产端发送消息到交换机，交换机转发消息到队列。

消费端：多个消费端同时连接同一个队列实现消息监听形成争抢的结构。哪个空闲率高、线程资源多就更有可能抢到消息。

应用场景：抢红包，资源分配系统，

![](https://note.youdao.com/yws/api/personal/file/WEB328d39b1cba038d3db35aa136ac5f50e?method=download&shareKey=9dec9bede49ae62c9c837abd790c0505)

##### 2.3 发布订阅模式

同步发送一条消息到达多个队列，强调的是生产端交换机发送消息逻辑。

![](https://note.youdao.com/yws/api/personal/file/WEB93f49838a03f1e8b8faebe87ead65f19?method=download&shareKey=182d29a1fc1e3fd77913322c0552ad80)

生产端：将消息发送给交换机，交换机的类型（fanout）

交换机：会将消息同步复制发送给所有与交换机绑定的队列。

消费端：取决于是简单模式还是争抢模式

应用场景：邮件群发，广告发送。 

##### 2.4 路由模式

路由模式是rabbitmq消息队列最常见的一种方式，可以根据生产端的消息参数决定当前消息该发送给那个队列，不发送给那些队列。

路由：从起始地点到目的地的路线过程，就是路由规则定义的。

![](https://note.youdao.com/yws/api/personal/file/WEB3a668811918020607002e1df4783b7a1?method=download&shareKey=5f51e358d458d35062b359b03f7442c1)

生产端发送消息，携带具体的路由key routingKey交换机类型direct接收到消息后，会匹配判断绑定到该交换机的所有队列的路由key那个相同，决定最终发送到哪，如果没有匹配的队列，消息在交换机删除了。

消费端：简单模式争抢模式决定结构

应用：邮箱（邮箱地址 路由key），error消息路由

##### 2.5 主题模式

将路由key进行多级划分，实现多级传递的一种使用方式。

![](https://note.youdao.com/yws/api/personal/file/WEBb7b4eb99c1f40e622a9b821f4893eb59?method=download&shareKey=b6ac3d56027946046cbf8cedfb962de0)

和路由模式逻辑类似，都是对消息中的路由key进行后端队列绑定路由key匹配，区别在于主题模式的队列使用的路由key可以不是具体的内容，而是一个范围的匹配。

| *    | 匹配一个字符串 |
| ---- | -------------- |
| #    | 匹配多级字符串 |

消息目的地：中国.北京.朝阳

| queue1(中国.#)    | true                        |
| ----------------- | --------------------------- |
| queue2(*.*.朝阳） | true                        |
| queue3(中国.*)    | false，中国.上海，中国.新疆 |

应用：多级消息的的同类分配。

![](https://note.youdao.com/yws/api/personal/file/WEBd6ae5cf3c832673420d252bb70a529cd?method=download&shareKey=cdf3a0dd6270f94ea62b7f6948ff511a)

#### 3.总结

简单模式，工作争抢模式，强调消费端监听队列的结构，简单模式1对1监听，工作模式多对1的监听。

争抢模式中讨论的抓取消息的逻辑和确认逻辑。

1. 每次调用nextDelivery的时候，都会到rabbitmq抓取一个消息   
2. 默认情况下，每次调用都会成功抓取一个新消息   
3. 因为确认机制是使用自动确认，抓完了就确认   
4. 自定义手动回执。在执行完nextDelivery之后，没有返回ack确认消息时，消息只能消费一条。没有确认的消息在rabbitmq中不删除，以unacked状态保存  

 生产端的发送消息逻辑：

1. fanout：多个队列同时==使用一个相同的路由key绑定fanout类型交换机。==只要消息携带相同路由key就可以同步复制发送到多个队列
2. direct：多个队列绑定一个交换机，但是==使用路由key是不同的，可以在消息发送时根据消息携带的路由key判断最终访问的队列是哪个==
3. topic:  和direct执行逻辑是一模一样，区别在队列绑定交换机时使用的路由key不在是确定具体值，而==是范围的匹配==，为了实现接收消费端继续发送消息成为下一级分拣工作。

## 四、java代码实现五种工作模式

#### 1.依赖的使用

使用springboot整合的依赖：spring-boot-starter-amqp(user系统测试)

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

##### 2.编写代码实现五种工作模式测试

##### 2.1 简单模式

可以使用默认的一个路由模式的交换机。（AMQP default）交换机名称是“”。这个交换机的存在，是为了防止任何一个队列在生成时，没有绑定其他交换机使用队列会默认绑定这个AMPQ default并且使用路由key就是队列的名称。

1. 初始化连接对象channel  

   ```
   //实现生产端或者消费端之前，创建连接对象
       private Channel channel;
       //给channel赋值
       @Before
       public void initChannel() throws IOException, TimeoutException {
           //获取连接工厂对象，赋值连接信息 ip port user pw vh
        ConnectionFactory factory=new ConnectionFactory();
           factory.setHost("10.9.104.184");
           factory.setPort(5672);
           //factory.setUsername("guest");
           //factory.getPassword("guest")
           //factory.setVirtualHost("/");
          Connection connection = factory.newConnection();
           channel=connection.createChannel();
       }
   ```

2. 编写一个生产端代码  

   将一个消息文本msg发送到默认交换机AMQP default 携带一个路由key（存在的队列名称）。

   ```Java
   /*
       生产端逻辑，发送消息到amqp default
        */
       @Test
       public void send() throws IOException {
           //准备一个即将发送的消息字符串
           String msg="多日不见，甚是想念，" +
                   "今夜三更，后山见面";
           //发送之前，声明一个队列
           /*
           queue:String 队列名称
           durable: Boolean 表示队列是否支持持久化
           exclusive: Boolean 表示队列是否专属，专属于当前连接对象Connection
           true 表示操作连接队列只有创建connection才能进行
           autoDelete: Boolean 表示是否自动删除。最后一个channel连接使用完队列
           是否删除 true删除 false不删
           args: Map对象，创建队列的各种属性,例如，消息存活最长时间，队列保存对多消息个数
            */
           //队列和交换机属于vh分配资源，一个队列绑定vh
           //在同一批资源中，队列名称不相同，声明时队列存在，声明无效
           //不存在则创建
           channel.queueDeclare("小红",false,false,false,null);
           //channel连接对象的api将消息发送出去
           /*方法参数
           exchange: String 类型代表交换机名称
           routingKey: String 路由key 默认交换机中就是队列名称值
           props: BasicProperties，表示消息的属性
           body: byte[] 消息体的二进制数据
            */
           channel.basicPublish("","小红",null,msg.getBytes());
       }
   ```

3. 编写消费端逻辑  

   监听队列，实现消息发送

   ```java
   /*
       消费端逻辑
        */
       @Test
       public void consumer() throws Exception {
           //创建出来消费端对象
           QueueingConsumer consumer=new QueueingConsumer(channel);
           //consumer绑定channel后就具备了绑定queue的能力
           //绑定消费对象和队列 小红 实现1对1监听
           /*
               autoAck:boolean 自动确认回执
               回执：告诉rabbtimq 是否消费消息成功，返回true
               rabbitmq将会把该消息删除，返回false 该消息继续保存
            */
           channel.basicConsume("小红",true,consumer);
           //监听获取消息信息
           QueueingConsumer.Delivery delivery = consumer.nextDelivery();//从监听的队列中获取一个
   //存在的消息
           //delivery接收的除了消息体body以外还有header properties
           System.out.println(new String(delivery.getBody()));//body是二进制数组
       }
   ```

##### 2.2 争抢模式

现了简单模式一发一收，多个消费代码进程同时启动，同时运行，争抢数据消息。多写一个消费代码方法。

```Java
package cn.tedu.rabbitmq;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.QueueingConsumer;
import org.junit.Before;
import org.junit.Test;
import java.io.IOException;
import java.util.concurrent.TimeoutException;
/**
 资源争抢模式
 多个消费端同时监听一个消息队列
 */
public class WorkMode {
    private Channel channel;
    @Before
    public void initChannel() throws IOException, TimeoutException {
        //获取连接工厂对象，赋值连接信息 ip port user pw vh
        ConnectionFactory factory=new ConnectionFactory();
        factory.setHost("10.9.104.184");
       factory.setPort(5672);
        //factory.setUsername("guest");
        //factory.getPassword("guest")
        //factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        channel=connection.createChannel();
    }
    @Test
    public void send() throws IOException {
       String msg="小红情书：我就喜欢你每次给我送的大白薯";
        channel.queueDeclare("小红",false,false,false,null);
        for(int i=0;i<100;i++) {
            channel.basicPublish("", "小红", null, msg.getBytes());
        }}
    /*
    消费端逻辑
     */
    @Test
    public void consumer01() throws Exception {
       //创建出来消费端对象
        QueueingConsumer consumer=new QueueingConsumer(channel);
        channel.basicConsume("小红",true,consumer);
        while(true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();//从监听的队列中获取一个
//存在的消息
            //delivery接收的除了消息体body以外还有header properties
            System.out.println("阿强拿到消息："+new String(delivery.getBody()));//body是二进制数组
        }
     }
    /*
    消费端逻辑
     */
    @Test
    public void consumer02() throws Exception {
        //创建出来消费端对象
        QueueingConsumer consumer=new QueueingConsumer(channel);
        channel.basicConsume("小红",true,consumer);
        while(true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();//从监听的队列中获取一个
//存在的消息
            //delivery接收的除了消息体body以外还有header properties
          System.out.println("阿明拿到消息："+new String(delivery.getBody()));//body是二进制数组
       } }
}
```

##### 2.3 发布订阅模式

同步发送到多个队列执行群发。使用已有的交换机amq.fanout也可以实现发布订阅。自定义交换机，自定义绑定关系的对队列，实现发送。

```Java
//准备几个静态常量
    private static final String type="topic";//交换机类型
    private static final String exName=type+"Ex";//交换机名称
    private static final String q1="queue01"+type;
    private static final String q2="queue02"+type;
@Test
    public void send() throws IOException {
        //准消息
        String msg = "hello " + type;
        //声明交换机 exName type
        channel.exchangeDeclare(exName, type);
        //声明队列，多个队列，同时绑定一个fanout交换机
       channel.queueDeclare(q1,false,false,false,null);
       channel.queueDeclare(q2,false,false,false,null);
        //绑定
        channel.queueBind(q1,exName,"keke");
        channel.queueBind(q2,exName,"keke");
        //发送消息      channel.basicPublish(exName,"haha",null,msg.getBytes());
 }
```

##### 2.4 路由模式

队列是详细的路由绑定，消息携带路由key匹配队列的路由

```Java
 @Test
    public void send() throws IOException {
    //准消息
        String msg = "hello " + type;
        //声明交换机 exName type
        channel.exchangeDeclare(exName, type);
       //声明队列，多个队列，同时绑定一个fanout交换机
       channel.queueDeclare(q1,false,false,false,null);       channel.queueDeclare(q2,false,false,false,null);
        //绑定
        channel.queueBind(q1,exName,"北京");
        channel.queueBind(q1,exName,"北戴河");
        channel.queueBind(q1,exName,"哈尔滨");
        channel.queueBind(q2,exName,"上海");
        //发送消息
       channel.basicPublish(exName,"哈尔滨",null,msg.getBytes());
    }
```

##### 2.5 主题模式

```Java
package cn.tedu.rabbitmq;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import org.junit.Before;
import org.junit.Test;
import java.io.IOException;
import java.util.concurrent.TimeoutException;
public class TopicMode {
    private Channel channel;
@Before
public void initChannel() throws IOException, TimeoutException {
        //获取连接工厂对象，赋值连接信息 ip port user pw vh
   ConnectionFactory factory=new ConnectionFactory();
       factory.setHost("10.9.104.184");
        factory.setPort(5672);
        //factory.setUsername("guest");
        //factory.getPassword("guest")
        //factory.setVirtualHost("/");
        Connection connection = factory.newConnection();
        channel=connection.createChannel();
    }
    //准备几个静态常量
    private static final String type="topic";//交换机类型
    private static final String exName=type+"Ex";//交换机名称
    private static final String q1="queue01"+type;
    private static final String q2="queue02"+type;
    @Test
    public void send() throws IOException {
        //准消息
        String msg = "hello " + type;
        //声明交换机 exName type
        channel.exchangeDeclare(exName, type);
        //声明队列，多个队列，同时绑定一个fanout交换机
       channel.queueDeclare(q1,false,false,false,null);
       channel.queueDeclare(q2,false,false,false,null);
        //绑定
        channel.queueBind(q1,exName,"中国.#");
        channel.queueBind(q2,exName,"中国.上海.*");
        //发送消息
       channel.basicPublish(exName,"中国.北京.朝阳",null,msg.getBytes());
    } 
}

```

#### 3.整合到spring boot

##### 3.1 springboot特点

自动配置。根据依赖实现大量代码加载（springboot完成的扩展配置类加载）

1. 依赖了starter-amqp，简化依赖，重要作用就是引入这些**AutoConfiguration   
2. 自动配置类中一定存在@Configuration+@Bean的结构。  
3. 可以使用这些bean，并且也可以使用springboot封装的代码逻辑。  

##### 3.2 整合步骤

1. 核心逻辑：  

   配置类实现连接对象，连接工厂的管理（Bean对象创建）

   封装注入使用，获取channel 实现生产端发送消息，消费端监听消息

2. 自定义配置：

   1. @Configuration   
   2. @ConfigurationProperties属性  
   3. @Bean创建对象  conncetionFactory  
   4. 生产端，消费端注入实现发送，监听消费   
   5. 消费端异步监听无法实现（NIO）  

3. 使用springboot自动配置  

   RabbitmqAutoConfiguration

   读取属性，创建连接，RabbitTemplate实现注入生产端

   使用申明式注解实现消费端逻辑

4. 整合详细步骤

   1. 按照配置类读取的属性结构准备属性  

      #rabbitmq

      spring.rabbitmq.host=10.9.104.184

      spring.rabbitmq.virtual-host=/

      spring.rabbitmq.username=guest

      spring.rabbitmq.password=guest

   2. 生产端消息发送处理  

      注入RabbbitTemplate调用api发送消息

      1. convertAndSend：关注的是消息body作为源数据怎么发送，发送到哪

         1. exchange:已存在的交换机名称   
         2. routingkey：消息路由key  
         3. msg：Object  可以任意内存对象数据，一般String  

      2. send：关注的是消息本身携带的各种属性封装

         1. exchange：交换机名称   
         2. routingKey：路由key  
         3. Message：封装了springboot格式的消息对象
            1. 包含body=byte[]   
            2. 包含MessageProperties消息属性  

         ```Java
         package cn.tedu.seckill.controller;
         import org.springframework.amqp.core.Message;
         import org.springframework.amqp.core.MessageProperties;
         import org.springframework.amqp.rabbit.core.RabbitTemplate;
         import org.springframework.beans.factory.annotation.Autowired;
         import org.springframework.web.bind.annotation.RequestMapping;
         import org.springframework.web.bind.annotation.RestController;
         @RestController
         public class SenderController {
             @Autowired
             private RabbitTemplate template;
             //接收请求，发送消息
             @RequestMapping("send")
             public String sendMsg(String msg){
                 //直接将消息msg作为消息体的内容，让template发送
                 /*
                     exchange:交换机名称
                     routingKey:路由key
                     msg：Object类型msg 自动进行byte转化
                  */
                /*template.convertAndSend("directEx",
                         "北京",msg);*/
                 //相当于 channel.basicPublish("ex","routing",msg.geBytes)
                 //关心发送消息时，消息有一些属性想要携带，使用send方法
                 /*MessageProperties properties=new MessageProperties();
                 properties.setPriority(100);
                 //properties.setUserId("110");
                 Message message=new Message(msg.getBytes(),properties);
                 template.send("directEx1","北京",message);
                 return "success";*/       template.convertAndSend("seckillD01","seckill",msg);
                 return "success";
             }
         }
         ```

   3. 消费端逻辑

      1. 准备一个Component对象类   

      2. 实现消费端在类中的消费逻辑方法  

      3. @RabbitListener注解方法上，就能实现监听队列的作用  

         发送消息时，如果使用Object，接收方法参数也是Object

         发送消息时，使用Meassage对象，必须使用Massage接收

         消费端代码逻辑，不一定必须和生产端在同一个进程中，同一个系统中。

         ```
         package cn.tedu.seckill.consumer;
         
         import cn.tedu.seckill.mapper.SecMapper;
         import com.jt.common.pojo.Success;
         import org.springframework.amqp.rabbit.annotation.RabbitListener;
         import org.springframework.beans.factory.annotation.Autowired;
         import org.springframework.stereotype.Component;
         import redis.clients.jedis.JedisCluster;
         
         import java.util.Date;
         
         @Component
         public class SeckillConsumer {
             @Autowired
             private SecMapper secMapper;
             //任意编辑一个方法，实现消费逻辑
             //方法的参数就是发送到rabbitmq中的对象
             //可以String 接收body 也可以是Message接收
             //包含消息属性
             @Autowired
             private JedisCluster jedisCluster;
             @RabbitListener(queues = "seckill01")
             public void consume(String msg){
                 //接收到消息msg="1330119123/1"包含了user信息 商品id
                 //解析出来
                 Long userPhone=Long.parseLong(msg.split("/")[0]);
                 Long seckillId=Long.parseLong(msg.split("/")[1]);
                //先连接redis 从redis中获取一个 seckill_1 rpop元素，成功了
                 //说明元素没有被rpop完，具备秒杀减库存的权利
                 //手动创建这个list 在redis集群，没创建会报错
                 String listKey="seckill_"+seckillId;
                 String rpop = jedisCluster.rpop(listKey);
                 if(rpop==null){
                     //如果rpop结果是null说明元素已经被拿完了，后续减库存
                     //都不做了
                     System.out.println("用户"+userPhone+"秒杀"+seckillId+"由于" +
                             "redis库存见底，秒杀失败");
                     //解决了大量请求到达数据判断number>0时出现
                     //线程安全问题导致的超卖
                     return;
                 }
                 //执行减库存逻辑 seckill表格 number=number-1
                 /*
                 UPDATE seckill SET number=number-1 WHERE seckill_id=2
                 AND number>0 AND NOW()>start_time AND NOW()<end_time
                  */
                 int result=secMapper.decrSeckillNum(seckillId);
                 //判断减库存是否成功，result=1成功 result=0失败
                 if(result==1){
                     //成功 当前用户具备购买商品的资格
                     //将成功的信息封装数据入库记录success表格
                     Success suc=new Success();
                         suc.setCreateTime(new Date());
                         suc.setSeckillId(seckillId);
                         suc.setUserPhone(userPhone);
                         suc.setState(1);
                     secMapper.insertSuccess(suc);
                 }else{
                     //失败，result==0 减库存失败，是因为卖完了
                     System.out.println("用户"+userPhone+"秒杀"+seckillId+"由于" +
                             "库存见底，秒杀失败");
                 }
             }
         }
         ```

   4. 上述生产逻辑，是在已存在组件情况下可以实现发送成功

      1. 想要在执行代码的一瞬间，生成需要声明的交换机，队列   

      2. 通过配置类，加载到容器内存对象Queue表示队列  

      3. 通过配置类，加载到容器内存对象Exchange表示交换机  

      4. 在配置类中实现这些声明的组件的对象封装使用@Bean交给容器管理  

         ```
         package cn.tedu.seckill.config;
         import org.springframework.amqp.core.Binding;
         import org.springframework.amqp.core.BindingBuilder;
         import org.springframework.amqp.core.DirectExchange;
         import org.springframework.amqp.core.Queue;
         import org.springframework.context.annotation.Bean;
         import org.springframework.context.annotation.Configuration;
         /*
         实现一个配置类编写
         通过声明大量的对象，使得连接rabbitmq的底层
         conncetion可以创建很多组件内容
         和调用底层代码queueDeclear exchangeDeclear效果一样
          */
         @Configuration
         public class QueueCompConfig {
             //声明队列对象
             @Bean
             public Queue queue01(){
                 //springboot在底层通过连接
                //调用queueDeclear name false false false null
                 return new Queue("seckill01",false,false,false,null);
             }
             @Bean
             public Queue queue02(){
                 //springboot在底层通过连接
                 //调用queueDeclear name false false false null
                 return new Queue("seckill02",false,false,false,null);
             }
             //配置声明交换机对象
             @Bean
             public DirectExchange exD1(){
                 return new DirectExchange("seckillD01");//默认不自动删除，默认持久化
             }
             //配置声明交换机对象
             @Bean
             public DirectExchange exD2(){
                 return new DirectExchange("seckillD02");//默认不自动删除，默认持久化
             }
             //绑定关系，通过返回代码对象实现 Binding
             @Bean
             public Binding bind01(){
                 return BindingBuilder.bind(queue01()).
                         to(exD1()).with("seckill");
                 //seckill01使用seckill的路由绑定到了seckillD01
             }
             @Bean
             public Binding bind02(){
                 return BindingBuilder.bind(queue02()).
                         to(exD2()).with("haha");
                 //seckill02使用haha的路由绑定到了seckillD02
             }
         }
         ```

         



