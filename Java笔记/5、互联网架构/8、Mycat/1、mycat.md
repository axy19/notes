[TOC]

# Mycat

## 一、数据库的分布式和高可用结构

#### 1.数据库拆分（分布式）

由于微服务结构的项目系统存在，不同的微服务可能访问不同的数据库内容，还可能同一个微服务需要访问不同数据库的数据内容。然而将所有表格放到一个库中的数据结构，不能满足这样的需求（性能）。需要根据场景和条件。进行数据的拆分。

1. 垂直拆分  

   将一个库中的不同表格放到多个独立的库里，系统访问需要访问不同的库来使用这些表格数据的数据库结构就叫垂直拆分。垂直拆分单独存在，使得每一个库中的表格都是完整数据，说明没有数据分片。

2. 水平拆分  

   当某张表格数据量大到一定程度==（百万级）==，可以将表格切分到不同的数据库中，多个数据库的表格形成一个整体数据。最终形成多个数据分片的数据库集群。mysql表格上限千万级。orcale 亿级别。

   ![](https://note.youdao.com/yws/api/personal/file/E8FD57E5EEF44E2C81EA5216A0CFFED8?method=download&shareKey=3d0e97e96c22141b41e7a075ac7fa044)

#### 2.数据库主从复制

mysql、oracle也支持主从复制，一旦经过垂直拆分，水平拆分后形成数据集群，每一个存储数据的数据库都要具备主从数据复制，形成故障转移的前提条件，才能实现高可用结构。

![](https://note.youdao.com/yws/api/personal/file/6CB5475C11A843A3B820CA7C6157A1AB?method=download&shareKey=9ea90a2b9aabe1d5f699025ab2971e89)

##### 2.1 mysql主从复制原理

mysql支持一主多从，可以实现高可用集群中某个分片的高可靠性。

mysql中可以将启动的数据库,配置主节点和从节点的关系，实现同步原理：

![](https://note.youdao.com/yws/api/personal/file/C6F36A4F12244851A49260D1218FC12A?method=download&shareKey=83335f8f081b4931a61d9293301cc0fe)

1. 主节点：

   开启二进制日志文件，记录所有主节点客户端写操作命令。日志中以pos指针值指向文件末尾，表示本次记录的更新判断。

2. 从节点：

   1. 开启了i/o线程，SQL线程，中继日志。
   2. i/o线程：负责登录主节点，监听主节点二进制文件，判断更新，将更新内容抓取到本地存储在中继日志。
   3. 中继日志：负责存储I/O抓过来的东西。以pos记录最终末尾值。
   4. SQL线程：监听中继日志，判断更新，抓取同步过来sql语句，执行sql，实现了和主节点保持一致的效果。

##### 2.2 安装2个服务器的mysql数据库

Percona[数据库的一种]的mysql数据库。

1. 直接解压到目标目录

   1. 在software下创建一个管理解压文件的文件夹percona  

      ```
      [root@10-9-104-184 software]#  mkdir percona
      ```

   2. 解压安装包到percona文件夹里  

      ```
      [root@10-9-104-184 software]# tar -xf  /home/resources/Percona-Server-5.6.24-72.2-r8d0f85b-el6-x86_64-bundle.tar -C  /home/software/percona/
      ```

2. rpm安装Percona的数据库

   1. 进入到percona文件夹看到rpm所有安装包   

   2. 按照顺序安装不同的rpm文件 安装4个文件  

      [root@10-9-104-184 percona]#  rpm -ivh Percona-Server-==56-debuginfo==-5.6.24-rel72.2.el6.x86_64.rpm

      [root@10-9-104-184 percona]#  rpm -ivh Percona-Server-==shared==-56-5.6.24-rel72.2.el6.x86_64.rpm

      [root@10-9-104-184 percona]#  rpm -ivh Percona-Server-==client==-56-5.6.24-rel72.2.el6.x86_64.rpm

      [root@10-9-104-184 percona]#  rpm -ivh Percona-Server-==server==-56-5.6.24-rel72.2.el6.x86_64.rpm

##### 2.3 调整mysql的环境

1. 启动mysql的服务命令。

   ```
   [root@10-9-104-184 percona]# service mysql start/stop/restart
   ```

2. 给它添加登录名root的密码‘root’  

   默认安装这个rpm文件时，mysql没有root的密码，所以无法实现远程登录。

   ```
   [root@10-9-104-184 percona]# mysqladmin -u root password 'root'
   ```

3. 登录进入客户端，开启远程访问权限  

   在mysql的表格中 mysql.user记录了当前数据库可以登录使用客户端信息表格记录。

   没有允许host为%的客户端登录到mysql服务，开启这个权限，否则想用windows代码连接数据库（持久层）失败的。

   登录到mysql

   \#mysql  -uroot -proot

   mysql>grant all on *[没有空格] .[没有空格] * to 'root'@'%'  identified by 'root';

   grant 权限（select_priv）on 数据库.表格  to 登录时使用用户名@限制远程客户端ip identified by 远程登录使用密码;

4. 准备2个数据库的客户端连接在sqlYog中  

   ![](https://note.youdao.com/yws/api/personal/file/A2555DAF79B24B039534D09A27C85C02?method=download&shareKey=c8c0d9dc8067248f6592a032f8394c87)

   ![](https://note.youdao.com/yws/api/personal/file/F23B8BC2800F4CA2B1AD8FA9D0AF14AC?method=download&shareKey=ab58902f105b460062964f3af024e6d5)

   ![](https://note.youdao.com/yws/api/personal/file/D195B5586A954465B94516766716D913?method=download&shareKey=ddfdae332e74246cc5d99a2b48dd9bbd)

   新建sqlYog的连接选项，可以方便下次启动连接。也可以每次重新输入ip密码登录连接。

5. 使用sqlYog将user表格数据添加密码（多台数据库都得加）  

   后面使用mycat登录，有可能由于本地登录时，默认的hosts文件中有 10-9-104-184的域名，将会让mycat登录mysql时使用域名，mysql的user表格里对应这个域名没有数据。主要没有密码。

   找到mysql.user表格，将其中user为root的所有行数据中的密码password拷贝复制成root加密文字。

   ![](https://note.youdao.com/yws/api/personal/file/5D88EB72D9834B5AB115A49F56772C51?method=download&shareKey=77d24027fbe4be93a9784d26cdc0e362)

   图片中是默认的密码为空，只要使用sqlYog拷贝粘贴就行。

   ![](https://note.youdao.com/yws/api/personal/file/686C2ACD56A640E79E0394B13030FD1F?method=download&shareKey=6944c561ac0a2565816710f54c9300db)

#### 3.主从数据结构配置

##### 3.1 主节点（10.9.100.26）

根据原理讲解，只要做一件事，开启二进制日志文件就行。mysql默认情况不开启的。

1. 找到一个配置文件打开进入vim编辑  

   ```
   [root@10-9-100-26 percona]# vim /etc/my.cnf
   ```

   mysql服务启动时加载默认配置文件，可以在文件中开启二进制日志配置

2. 修改配置内容  

   1. server-id=1  

      表示主从结构就是集群，在同一个主从中，多个服务器分配服务id值，并且id不同

   2. log-bin=mysql-log  

      重启服务器后，会在mysql中生成一个二进制的日志文件 文件名称叫做mysql-log

      ![](https://note.youdao.com/yws/api/personal/file/8605578468D74E939F150BF078CCA2BE?method=download&shareKey=17e874d5307b7f7c9775c3dc42338df6)

   3. 让配置生效，重启服务  

      ```
      #service mysql restart
      ```

      重启之后，修改的配置内容就被mysql加载了

3. 查看当前主节点中生成的日志文件  

   1. 登录进入mysql  

      ```
      #mysql -uroot -proot
      ```

   2. 执行命令查看  

      ```
      mysql> show master status\G;
      ```

      ==\G是为linux的命令客户端提供的一个纵向展示数据后缀，sqlYog不支持。==

      ![](https://note.youdao.com/yws/api/personal/file/E9A93989F902441DAF21FEA1E0A0CBEB?method=download&shareKey=7c0ff1febc1c7a4b8f7e0907cca2f594)

      查看文件名称后一个整数后缀.000001，根据不同的情况做自增。这是一种文件的滚动方式。防止一个日志文件变得过大。

      重启数据库时会发生变动。

      当文件达到大小上限。

      无论什么情况，上一个文件mysql.000001与下一个文件mysql.000002,之间的指针指向是相连的。前一个的文件指针末尾指向新文件的起始。

##### 3.2 从节点

1. 配置my.cnf的配置文件  

   1. server-id=2   
   2. log-bin=mysql-log(为后续双机热备双向主从做准备)  

2. 重启服务，让配置生效  

3. 在从节点执行挂接命令 

   登录到从节点执行change  master to的命令  

   ```
   mysql>CHANGE MASTER TO
   MASTER_HOST='10.9.100.26',
   MASTER_PORT=3306,
   MASTER_USER='root',
   MASTER_PASSWORD='root',
   MASTER_LOG_FILE='mysql-log.000001',
   MASTER_LOG_POS=318
   ```

4. 执行从节点show 命令 

   ```
   mysql> show slave status\G;
   ```

5. 从节点挂接主节点参数完成后，启动IO和sql线程 

   登录到从节点mysql客户端

   mysql>start  slave;

   命令的执行就是将从节点设置的io和sql线程启动。

   常见错误：挂接时，参数给错了，导致start时候，io无法启动，或者运行状态不正确的问题

   1. 停：停止slave进程  io sql停掉  

      mysql>stop slave

   2. 重新挂接：修改表格参数  

      mysql>change master to命令修改正确参数再执行一遍

   3. 重新启动：启动io  sql线程  

      mysql>start  slave

##### 3.3 测试主从

1. 测试同步数据

   1. 在主节点中创建数据库，创建表格，填写数据   

   2. 观察从节点是否同步了数据

       没有出现问题，同步成功

2. 从节点写入数据  

   会出现问题，但是根据主从原理，很容易的解决这个问题

   在从节点新增 4 张凤雏一条数据

   在主节点新增 4 钢铁侠一条数据

   A 从节点的数据跟随主节点新增发生变动

   B 主节点的数据因为从节点有数据，新增的虽然是钢铁侠，但是数据库表格记录张凤雏

   ==C  主从各自新增都成功，各自保存各自的数据==

   原因：底层的主从结构已经被破坏了。==SQL线程宕掉了。==

   解决方案：可以将错误数据 4  张凤雏从从节点删除，重启slave的io线程和sql线程

   delete从节点错误数据

   stop  slave

   start  slave

3. 结论：

   从节点虽然mysql技术端没有限制只读，但是从数据同步逻辑，业务逻辑角度，从节点不可以写数据。会造成主从数据错乱甚至是失效。

## 二、高可用分布式中的概念

#### 1.故障转移

数据库集群中，只要可以对某个数据分片实现库和表格的主从复制，客户端连接的主节点宕机之后，就能够连接从节点进行数据读写功能，从主节点替换到从节点---故障转移。

故障转移谁来完成：redis sentinel（哨兵）。redis-cluster由主节点来完成。

数据库集群中：由中间件完成（和sentinel逻辑类似）。

#### 2.读写分离

多台数据库实现多个分片的高可用主从配置。主节点主要使用写操作执行。从节点不能写。但是从节点可以实现读的分离。这种结构，将写操作在主节点实现，但是读的操作，分配到其他从节点的逻辑称为读写分离。

从节点的逻辑--读写分离

​	实现写操作的：写池

​	实现读操作的：读池

避免了主节点进行读写效率低，将一部分读的并发分出去

![](https://note.youdao.com/yws/api/personal/file/8C4A4FDD4CFA4314B954D4728B1C20CE?method=download&shareKey=be543d2f333ad32567c822d48f27c3f9)

#### 3.双向的主从复制

主从复制，实现了单向的主从复制，这种结构在故障转移时，只能实现一次性的转移，主节点从故障中恢复之后，无法再继续添加到集群提供服务，宕机期间，丢失很多数据，

![](https://note.youdao.com/yws/api/personal/file/14268A898BD54B2C9C9790574E8FDC59?method=download&shareKey=dfa53bbb20ed4d5e256dfe28c01dc077)

可以利用双向的主从复制使得当前分片结构实现多次的故障转移。

![](https://note.youdao.com/yws/api/personal/file/5BB53BA7CE39482EB4C3A025AFD3AFF5?method=download&shareKey=64480bea5a90172855e8e93c6cc79f30)

实现这个结构只要将原来的从节点看成新的主节点，原来的主节点看成新的从节点，反向挂接就可以。

==只要多个双向主从，有一个正常，就可以不断的返回的故障恢复故障恢复。--双机热备==

## 三、数据库中间件

#### 1.mycat

mycat是国内性能非常高，企业级数据库中间件。一些常见的互联网应用，双11，京东等都在使用mycat

#### 2.特点

性能高

1. 支持水平切分中，分片表格中总数据量百亿级别（官方指南说千亿），一般数据量达到千亿，都会使用大数据存储技术 hdfs hbase等等。
2. 读写分离性能速度高，取决于底层计算逻辑。而且不是任何一个中间件都能支持读写分离的。

#### 3.mycat使用结构

1. 中间件---代理意思类似。之前客户端直接访问数据库。有了中间件之后，客户端系统要通过中间件才能使用后端数据库集群。

   ![](https://note.youdao.com/yws/api/personal/file/F2B48320F5E5478E8B8FDFC61C77D28F?method=download&shareKey=d46530c741902b25da7aa7aa2ba182a7)

2. mycat工作逻辑

   1. 客户端软件系统整理SQL，发送给mycat，mycat接收到SQL语句

   2. 开始计算数据分片

      1. 通过SQL语句的解析，得到计算分片结果。
      2. 找到对应数据库分片位置。
      3. 一个数据的分片可能是主从结构的数据库集群。

   3. 开始计算读写分离

      1. select show都是读操作，访问主从结构中的从节点或者主节点都能实现读逻辑。
      2. create drop insert等都是写操作，访问主节点执行。

   4. 确定了当前操作的sql语句具体将要访问后端哪个数据库节点，从而从连接池中，获取连接资源发送sql到后端真实库，将返回数据返回给客户端使用 。

      ![](https://note.youdao.com/yws/api/personal/file/C42E8726472D415F9066DA36FD61300E?method=download&shareKey=cf67021c91a79111c13ad080a3291243)

#### 4.连接数据库的假死

原因：中间件后端连接数据库的线程逻辑是==阻塞线程==，导致假死的出现。

![](https://note.youdao.com/yws/api/personal/file/810E6E274E9D4AB79916E7378C1DBD7B?method=download&shareKey=4bfccc7215ef00df48d01fee876b0312)

假死：当中间件后端连接数据库的线程是阻塞线程（一直跟着一个任务，直到任务完成。）会从SQL进入mycat到连接后端数据库获取数据返回信息，一直处于繁忙状态，很有可能在高并发访问中间件时，有一段时间（时间很短）会发生线程被占满了，导致错误判断该连接的这个后端数据库连接不上--宕机，其实没有宕机，进行故障转移，记录宕机的操作--假死。

mycat在这里使用后端连接数据库的线程是==非阻塞线程NIO。==

#### 5.阻塞线程和非阻塞线程

线程被分配是执行代码任务

阻塞线程在等待调用结果返回之前，挂起状态（占用），不能执行其他任务。

非阻塞线程在等待调用结果返回之前，可以执行其他功能。

高并发下区别，一个并发处理能力高，一个并发处理的能力低。

## 四、mycat的使用

#### 1.mycat的安装和启动

##### 1.1 安装和解压

从/home/resources下拿到Mycat安装包解压到/homse/software中

==基于java语言==开发的，运行要在jdk环境下运行 mycat版本1.5-->jdk1.7

mycat使用登录命令，就是mysql登录命令。所以需要mysql环境支持。

```
[root@10-9-104-184 software]# tar -xf /home/resources/Mycat-server-1.5.1-RELEASE-20161130213509-linux.tar.gz -C ./
```

##### 1.2 启动mycat

1. mycat文件夹结构  

   bin: 命令脚本所在的文件夹

   conf:所有xml配置文件所在的文件夹

2. 启动mycat  进入bin目录  

   \#mycat start --后台运行，日志输出将会进入到logs文件夹中wrapper.log的文件中

   \#mycat console --控制台运行，打印日志。

##### 1.3 登录mycat命令

1. 使用mysql 命令登录 

   必须指定mycat的端口，指定mycat运行服务器ip地址，使用mycat的用户名和密码

   ```
   [root@10-9-104-184  conf]# mysql -utest  -ptest -h10.9.104.184 -P8066 
   ```

2. sqlYog登录  

   sqlYog确实可以登录mycat但是这个软件不是专门为mycat开发的客户端软件。sqlYog界面中各种按钮，点击都会发送sql语句到服务器，如果使用sqlYog连接，只能实现一些常用常见的命令发送。没有配置好mycat的服务文件，使用sqlYog。

#### 2.mycat的配置文件

在mycat根目录有conf文件夹，其中有三个xml配置，都会使用到。==一旦涉及到修改xml，习惯备份原文件。==

##### 2.1 server.xml

###### 2.1.1 标签结构

1. system

   property  

2. user

   property  

3. quarantine

   1. whitehost   
   2. blacklist 

###### 2.1.2 标签和属性

1. system：内容与mycat进程启动时占用的资源、内部配置属性有关

   property:可以配置压缩格式，配置端口号，配置占用系统的线程资源等等。 

2.  user:mycat登录的用户配置、权限配置等

   1. name:用户名 
   2. password:用户密码  
   3. schemas：该用户可以访问的mycat中的数据库。可以访问多个数据库以 ，隔开，必须是mycat中存在的库，如果有任何一个库不存在，将会报错   
   4. readOnly：用户只读

3. quarantine：防火墙配置

   1. whitehost:ip白名单  

      只有客户端ip地址在白名单范围内，才被允许访问mycat

      ```
      <host host="127.0.0.1"  user="mycat"/>
      ```

      只有客户端是从127.0.0.1的ip来访问mycat，并且使用mycat用户登录才能登录成功

   2. blacklist:sql黑名单  

      在黑名单中列出的sql规则，都不可以使用。例如：drop不允许执行，没有where条件的delete不能执行。

      ```
      <blacklist  check="false"></blacklist>
      ```

      标签中值是空的不检查

      例如：限制客户端不能使用select * 这种语句

      ```
      <blacklist check="true">selectAllColumnAllow<blacklist>
      ```

##### 2.2 schema.xml

###### 2.2.1 标签结构

1. schema
   1. table
      1. childTable
         1. childTable  
2. dataNode  
3. dataHost
   1. heartbeat   
   2. writeHost
      1. readHost  

###### 2.2.2 标签和属性

1. schema:可以在一个schema.xml配置多个schema的标签，每一个都表示客户端可以看到一个数据库--不是真实的数据库。

   ```
   <schema name="TESTDB"  checkSQLschema="true" sqlMaxLimit="100">
   ```

   属性:

   1. name:客户端可以看到和使用的数据库名称。sehema.xml中配置的多个schema标签，对应server.xml用户互配置标签中schemas属性。   
   2. checkSQLschema：可以配置true和false，表示所有sql语句是否需要添加表格的所在库的数据名称。例如：表格  student,所在库  db1.配置fasle，sql发送到mycat，select * from  student.不会拼接db1,但是如果是true,变成select * from  db1.student.在mycat中存在多个库，多个表格，可以唯一定位表格的名称。   
   3. sqlMaxLimit：整数值，当sql语句做批量查询，mycat防止性能浪费，在判断sql语句语句中没有limit关键字自动拼接limit 0,100  查询前100条。 

2.   table：在一个schema中，存在的数据库表格，可以有多个table标签，表示多个表格。  

   ```
   <table name="t1_student" primaryKey="s_id" dataNode="dn1,dn2,dn3" rule="auto-sharding-long" />
   ```

   属性

   1. name:表格名称，所有表格数据都一定来自真实库，表格名称要和真实库对应，并且同一个schema标签中只能存在唯一一个table名称 。
   2. primaryKey:主键名称，对应表格的真实数据中的主键名字，默认是id，当不是id时，需要配置这个属性  。
   3. dataNode:table表格中的数据可以根据数据量大小，实现水平切分，对应的数据分片计算绑定在dataNode标签，这里可以设置当前表格被切分到了几个数据分片中,可以对应一个分片，可以对应多个分片。   
   4. rule：当表格需要进行对应多个分片数据切分时，指定切分数据数据分片计算逻辑，可以给配置rule规则，例如：auto-sharding-long  整数范围约束,表示以主键某个字段的整数做分片计算，0-500万对应第一个分片，500万-1000万对应第二个分片，1000万-1500万对应第三个分片。字符串可以使用一致性hash  sharding-by-murmur.  

3. dataNode:在mycat管理一个主从数据库集群作为分片使用时，需要将集群包装在一个数据分片对象中，一个dataNode标签表示一个数据分片--最主要的作用就是计算分片的，利用名称，下标实现分片计算。  

   ```
   <dataNode name="dn1" dataHost="localhost1" database="db1" />
   ```

   属性

   1. name:分片名称，按照配置顺序每个dataNode还有一个下标从0开始，可以做一致性hash计算。   
   2. dataHost:  一个分片不负责数据库集群的管理，只是绑定数据库集群管理对象dataHost，dataHost使用名字来绑定。  
   3. database:当前分片绑定真实数据库集群使用，真实数据库中database可能有多个，当前分片用的是哪个库（垂直划分），指定真实库名称  

4. dataHost：负责管理一个真正的数据库主从集群。连接池，读写分离都是有这个标签的对象实现的计算。

   ```
   <dataHost name="localhost1" maxCon="1000" minCon="10" balance="0"  writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
   ```

   属性

   name：当前dataHost的名字，绑定dataNode时使用

   maxCon:当前dataHost管理的数据主从集群每个节点的连接池中最大连接

   minCon:最小连接

   balance:读写分离的读逻辑

   writeType:读写分离的写逻辑

   dbType:默认mysql，数据库软件类型

   dbDriver:默认mysql叫做native，如果是其他数据库给定driver全路径值

   switchType:故障转移有关

   slaveThreshold:100是毫秒数，表示当前主从集群，从节点sql延迟100毫秒以上时，将不会使用该节点处理读数据逻辑。

5. hearbeat：dataHost连接使用后端数据时，实现心跳检测sql语句，一般有2中常用的，select  user(),show slave status(只有使用该语句，slaveThreshold:100才能生效)  。

   ```
   <heartbeat>select  user()</heartbeat>
   ```

6. writeHost：写主机，表示在一个主从集群中的真实库连接标签。只能在其中配置主节点 。

   ```
   <writeHost host="hostM1" url="10.202.4.39:3306" user="root"  password="sf123456">
   ```

   属性

   host:代号名称，相当于name  一般会使用默认结构 hostM1 第一个主节点

   url：ip：port连接 

   user:登录数据用户名

   password：登录密码

7. readHost：读主机，表示主从集群中真实库连接，主从节点都可以在这里配置，一般都是使用从节点。  

   ```
   <readHost host="hostS2" url="192.168.1.200:3306" user="root" password="xxx" />
   ```

   属性

   host:代号名称，相当于name  一般会使用默认结构 hostM1S1 第一个主节点第一个从节点

   url：ip：port连接 

   user:登录数据用户名

   password：登录密码

   ![](https://note.youdao.com/yws/api/personal/file/7A6298FB94AE43908C3962D03050FD5D?method=download&shareKey=52be01ae5bbb63fea4757d4f23357b40)

#### 3.mycat配置的入门案例

##### 3.1 结构

体会mycat作为中间件代理的使用，和schema.xml文件配置

![](https://note.youdao.com/yws/api/personal/file/D75F7DF9377C4E538E100EEE8FE2430C?method=download&shareKey=161f6a052d695641c90b433e00f7ee7f)

最终通过客户端，登录到mcyat 操作TESTDB.t1_student，实际数据来源从后端真实库t1_student

##### 3.2 配置步骤

1. 从2个数据库选取一个测试使用  

   创建出一个 真实库haha,真实表t1_student,数据库结构 id  int name varchar

2. 配置schema.xml  

   ==注释不能写中文，会报错，编解码错误。==

   ```xml
   <?xml version="1.0"?>
   <!DOCTYPE mycat:schema SYSTEM "schema.dtd">
   <mycat:schema xmlns:mycat="http://org.opencloudb/" >
   	<schema name="TESTDB" checkSQLschema="true" sqlMaxLimit="100">
   		<table name="t1_student" primaryKey="id" dataNode="dn1"/>
   	</schema>
   	<dataNode name="dn1" dataHost="localhost1" database="haha"/>
   	<dataHost name="localhost1" maxCon="1000" minCon="10" balance="0"
   		writeType="0" dbType="mysql" dbDriver="native" switchType="1"  
   		slaveThreshold="100">
   		<heartbeat>select user()</heartbeat>
   		<writeHost host="hostM1" url="10.9.100.26:3306" user="root" 	password="root"/>
   	</dataHost>
   </mycat:schema>
   ```

   当前入门案例中没有数据分片计算，没有读写分离的。不能实现高可用的故障转移。


#### 4.mycat测试案例1--读写分离

基于高可用结构中主从数据库，实现在主节点写数据，从节点读数据的计算分配。

##### 4.1 为了观察将目前主从结构关闭

1. 双向热备:每个数据库执行  

   mysql>stop  slave

2. 单向热备：在从节点执行  

   mysql>stop  slave

##### 4.2 读写分离的逻辑和结构

根据数据库主从结构不同，可以在另一个dataHost中准备不同的标签配置。

![](https://note.youdao.com/yws/api/personal/file/039140923B4D4953A37DDF63E368F5C4?method=download&shareKey=b7d73aed439f127562907c26fc4c51f0)

##### 4.3 修改schema.xml

在入门案例的基础上，使用一个writeHost无法完成读写分离，可以添加另一台云主机数据库到当前mycat管理中。

```
<writeHost host="hostM1" url="10.9.100.26:3306" user="root" password="root"/>
<writeHost host="hostM2" url="10.9.104.184:3306" user="root" password="root"/>
```

##### 4.4 读写分离的属性

dataHost标签管理一个数据库主从集群的标签，绑定到dataNode使用。由他来实现读写分离，涉及到两个属性。

1. writeType:控制当前dataHost数据库集群的写逻辑。

   1:1.5以上mycat不推荐使用1，.writeType=1时，会覆盖掉读的逻辑，balance的值失效。读和写的操作都会随机的在多个writeHost中进行

   0：writeType=0，当前写操作会在配置的第一个writeHost进行，读操作由balance决定

2. balance：控制读逻辑但是只在writeType是默认值0时生效

   1. 0：表示不开启读的分离，所有的读都在第一个writeHost进行
   2. 1：最常使用的一种配置，会从所有的readHost，和备用的writeHost进行读操作。当并发比较高，第一个writeHost也会分得一部分读的操作。
   3. 2：随机的在所有的host进行读操作。
   4. 3:随机的在所有的readHost进行读操作，如果没有readHost会只在第一个writeHost中进行读操作。

#### 5.高可用的故障转移

##### 5.1 双向热备关系

一个分片中的主从数据库，存在双向的主从2个节点，只要其中一个节点宕机，那么另外一个将会负责这个分片的读写逻辑。

##### 5.2 mycat的配置

dataHost中的属性switchType控制故障转移。

在一个dataHost中如果有多个writeHost将会在加载时给按照顺序配置序号index从0开始

writeType写总会在index下标为0的writeHost进行。故障转移对于switchType来讲执行逻辑就简单了。只需要将下标顺延，将index=1的顺延成0，不会影响写操作，故障转移的逻辑就是如此。

-1：不开启故障转移，宕机了就是宕机了，分片就不可以使用

1：开启故障转移，index=0的 writeHost宕机后，index=1的writeHost会替换到index=0.不影响写操作，读逻辑对于宕机的writeHost从节点保存readHost还进行读的分离么？不进行

从入门案例--读写分离--故障转移，单一分片，没有实现数据分布式数据切分。==读写分离，故障转移，保证一个分片中一个数据库的主从集群如果存在，能保证这个分片的高性能，高可靠的运行。==

#### 6.分片表格配置

##### 6.1 mycat.sql导入到2个真实数据库中。

为2个分片的真实库环境做准备 mycat库名

t1_student:目的是不影响前面测试，实现分片表格id为整数，使用范围约束计算

t2_user:做分片表格，使用一致性hash计算

t3_category:商品分类

t3_product:商品表格 实现全局表分片表配置

t5_order

t6_order_item:ER分片表格配置

##### 6.2 表格的类型

mycat中table标签能表示客户端使用的一个数据库表格。可以根据配置使用方式，数据量大小不同，分为2种类型，一种非分片表，一种是分片表格。

非分片表：测试案例t1_student就是非分片表格，表格数据全部来自于一个分片，一个数据库主从集群。

![](https://note.youdao.com/yws/api/personal/file/0CA3A561125A41128D5FDB108663609E?method=download&shareKey=c336f2065471cee040fbc24059195b38)

分片表格：如果整体数据量在一个表格中比较庞大。千万级，亿级。可以利用mycat实现分片表格配置。一个table中对应绑定多个dataNode，并且利用分片计算规则实现对应分片计算查找。

![](https://note.youdao.com/yws/api/personal/file/6435EAE903CC467084470F672BFAEF9F?method=download&shareKey=d6024f4dcf62872e9697944f80bec963)

##### 6.3 schema配置

```xml
<schema name="TESTDB" checkSQLschema="true" sqlMaxLimit="100">
	<table name="t1_student" primaryKey="id" dataNode="dn1,dn2" 
rule="auto-sharding-long"/>
</schema>
<dataNode name="dn1" dataHost="localhost1" database="mycat"/>
<dataNode name="dn2" dataHost="localhost2" database="mycat"/>
<dataHost name="localhost1" maxCon="1000" minCon="10" balance="0"
writeType="0" dbType="mysql" dbDriver="native" switchType="1"  
slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="hostM1" url="10.9.104.184:3306" user="root" password="root"/>
</dataHost>
<dataHost name="localhost2" maxCon="1000" minCon="10" balance="0"
writeType="0" dbType="mysql" dbDriver="native" switchType="1"  
slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="hostM2" url="10.9.100.26:3306" user="root" password="root"/>
</dataHost>
```

注意：xml语法错误，找到错误异常找Caused by提示。line num column num

mycat是否能创建正确的连接connection取决于提供的host的参数 url user  password是否正确。再有就是检查后端数据库mysql.user表格是否按照前期环境配置对每一个root用户添加了‘root’加密密码

##### 6.4 分片计算规则auto-sharding-long

默认逻辑：以每个表格数据中的列 id为计算分片的字段。并且是==整数计算--规则名称==：整数范围约束。

对id计算：id值在[0-500w]的一批数据计算切分到dn1（第一个数据分片）  [500w-1000w]切分到dn2  [1000w-1500w]切分到dn3(第三个数据分片)； 超过这个范围的1500w以上报错。

##### 6.5 分片计算规则配置文件rule.xml

rule.xml:可以定义所有与分片计算有关的规则和代码。在mycat的根目录conf文件夹里。

1. table Rule

   ```xml
   <tableRule name="rule1">
       //name表示表格分片计算规则名称 auto-sharding-long
   	<rule>
   		<columns>id</columns> 
           //该规则在计算分片时使用的表格字段名称
   		<algorithm>func1</algorithm> 
           //使用这个计算规则rule真正调用的计算逻辑算法的标签function名称
   	</rule>
   </tableRule>
   ```

2. function  

   rule.xml中的每一个function都表示了一种算法的详细计算配置。

   ```xml
   <function name="rang-long" //算法名称，绑定tableRule使用
   class="org.opencloudb.route.function.AutoPartitionByLong">//执行该算法的详细计算代码，代码在mycat的lib库中
   <property name="mapFile">autopartition-long.txt</property>//附加的计算文件，可以根据算法不同，代码不同，属性name值不同，整数范围约束中mapFile定义了整数范围对应分片的规则 0-500w 500w-1000w 1000w-1500w
   </function>
   ```

3. autopartition-long.text也在conf文件夹下。

   ```
   #K=1000,M=10000.
   0-500M=0 0-500万对应dataNode下标是0
   500M-1000M=1 对应dataNode下标是1
   1000M-1500M=2 对应dataNode下标是2
   自定义
   0-30M=0
   30M-90M=1
   ```

##### 6.6 一致性hash的分片计

==实现一个id值为字符串类型的表格的分片==计算，就不能再使用整数范围约束，可以使用一致性hash（也能对整数做分片）

rule.xml定义的一致性hash算法对应function标签

```xml
<function name="murmur"
class="org.opencloudb.route.function.PartitionByMurmurHash">
<property name="seed">0</property><!-- hash桶的种子值，一个mycat中种子值必须唯一，hash数学计算的一个参数，会改变映射结果。默认就是0而且几乎不会发生变化 -->
<property name="count">2</property><!-- 利用一致性hash计算分片时，使用分片dataNode的名字做整数映射，这个2表示分片个数-->
<property name="virtualBucketTimes">160</property><!-- 一个实际的数据库节点被映射为这么多虚拟节点，默认是160倍，也就是虚拟节点数是物理节点数的160倍 -->
<!-- <property name="weightMapFile">weightMapFile</property> 节点的权重，没有指定权重的节点默认是1。以properties文件的格式填写，以从0开始到count-1的整数值也就是节点索引为key，以节点权重值为值。所有权重值必须是正整数，否则以1代替 
例如：weightMapFile
0=1:0号分片权重是1
1=5:1号分片权重是5
-->
<!-- <property name="bucketMapPath">/etc/mycat/bucketMapPath</property> 
详细打印分片映射的整数结果到一个文件，但是没有任何效果 -->
</function>
```

![](https://note.youdao.com/yws/api/personal/file/041AACB09829478193D9CCD6FD403FD7?method=download&shareKey=483fecd122ae34bd9977ee62163cb360)

##### 6.7 自定义分片计算

自定义tableRule的标签，自定义计算的字段名称，例如对user_id做计算

自定义function标签，对应tableRule实现不同字段的计算逻辑。

##### 6.8 实现easy mall项目中某个表格的分片配置

t_product，将其配置在多个数据中，实现一致性hash的计算，定义一致性hash计算规则rule ：easymall-product使用算法 一致性hash

步骤 按照这个提示做，按照提示的点做dataNode

1. 准备2个云主机上数据库的真实表格（sqlYog导入easydb.sql）   

2. 配置

   mycat

   （怕自己配置错误，备份一份正常）

   1. schema.xml  使用2个dataNode（自定义dataNode名称）   

   2. rule.xml  

      自定义t_product计算规则使用字段product_id.使用一致性hash

#### 7.全局表

在配置的大量分片表格中，有很多表需要进行关联操作join。如果在多个表格的数据中，相关的数据产生了跨分片的存储---mycat没办法跨分片操作相关数据。

##### 7.1 跨分片的数据

![](https://note.youdao.com/yws/api/personal/file/32833215D4AF4CC6B724DA8312846249?method=download&shareKey=4dffc8e73c20e8c948d7c972b6ef91d4)

当多个表格中有分片表格，相关数据产生了跨分片的存储。，mycat无法实现相关查询，返回不正确数据

##### 7.2 全局表

###### 7.2.1 什么是全局表？

上述例子中，多个表格，其中有的是分片表格（数据量大），有的表格是非分片（数据量不大）。解决他们这种之间的跨分片配置，可以使用全局表。

全局表特点：数据量不大，数据变化稳定--工具字典表。例如：做业务查询时，可以使用工具字典表存储（200--成功，201--** ，202--**....）

例如：1101199802090988 解析出来地市，时间，操作人员 关联查询地市表，和实施员工表。

全局表的思路：将全局表个完全的复制到多个dataNode

全局配置：table标签中指定多个dataNode但是没有分片计算规则，默认复制使用。

t3_category

t3_product分片做到mycat管理的2个table标签中，

其中category就是全局表，product就是正常分片表格，使用的分片计算规则  auto-sharding-long

###### 7.2.2 全局表配置

1. 有了真实库中的对应表格

2. schema.xml

   ```
   <!--sharded product-->
   <table name="t3_product" primaryKey="id" dataNode="dn1,dn2"
   rule="auto-sharding-long"/>
   <!-- globle table category-->
   <table name="t3_category" primaryKey="id" dataNode="dn1,dn2"/>
   ```

###### 7.2.3 总结

全局表解决是非分片表格与分片表格关联的底层跨分片。

如果是分片表格与分片表格做关联操作？？？

#### 8.mycat的跨分片数据查询--ER分片表

多个分片表格有相关数据需要关联操作，更容易在没有合理的分片计算规则计划之下出现跨分片操作。

##### 8.1 多个分片表格跨分片

![](https://note.youdao.com/yws/api/personal/file/FEE3F1626AA34D93A47BEDB4FA643952?method=download&shareKey=0da0c8717b4d5cef25cf5506b93373c4)

多个分片表格如果设计不好分片规则计算，更容易出现跨分片操作。

##### 8.2 mycat独创-ER分片

ER分片表解决思路：相关数据，统一分片计算方法。

![](https://note.youdao.com/yws/api/personal/file/480BA033F4234C02A0DE8352ABCD46C2?method=download&shareKey=173321eb81124c3157b8348754c060e2)

配置一个测试案例t5_order和t6_order_tem表格。

==配置ER分片表，可以处理1对多关系，但是无法解决多对多。==

这种一对一，1对多关系可以分为父表和子表父表是1，子表是多。

区分父表子表：明显特征，子表中有关联字段，父表没有。业务意义区分，子表的任何一条数据都不能独立于父表存在。

就可以利用父表进行分片计算的统一（所有子表，孙表都是根据父表的关系进行分片计算）。可以使用table配置父表，定义分片，定义计算规则，使用table包含的childTable定义与父表的关系。

```
<!--er table-->
<table name="t5_order" primaryKey="id" dataNode="dn1,dn2"
rule="auto-sharding-long">
<!--child table -->
<childTable name="t6_order_item" primaryKey="id" joinKey="o_id"   parentKey="id"/>
</table>
```

table定义父表，使用多少分片，使用什么计算规则

childTable定义子表

==joinKey:表示子表中与父表的关联字段的名称==

==parentKey:子表的关联字段，在父表中叫的名字==

海量的多对多关系无法在mycat实现分片存储。

| 老师表格 | 学生表格 | 外键关联表格 |
| -------- | -------- | ------------ |
|          |          |              |

只能使用大数据数据存储，计算之间的关系。比如hadoop。hdfs存--mapreduce计算数据。

#### 9.mycat问题

##### 1.mycat高可用

mycat实现高可用：Haproxy  keepalive实现多个mycat并行运行

实现客户端访问某一个mycat宕机情况下转向访问另一个。

和nginx的高可用一样。

 使用zookeeper 分布式服务协调代理实现代码连接多个mycat实现的高可用。

##### 2.vim编辑器操作临时文件问题

在使用vim编辑一些文件。由于各种原因，修改了文件内容，但是么有保存

窗口关闭或者云主机网络断开。再次使用vim编辑出现一个头疼的内容。

原因：存在临时文件。临时文件名称 .源文件名称.swp.

提示内容：问你如何处理临时文件。

可以手动编辑原文件之前，删除这个临时文件。