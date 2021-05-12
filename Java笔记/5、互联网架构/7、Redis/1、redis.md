[TOC]

# Redis

## 一、Redis概述

#### 1.定义

Redis是nosql,key-value数据结构，非关系型，可持久化，支持分布式，实现缓存的内存数据库。

#### 2.详细介绍

1. no sql : not only structured query language,不仅结构化查询语言，指的是操作Redis的命令统称为nosql，就像操作mysql数据库的语言叫做SQL

2. key-value数据结构： Redis处理的是非结构化数据

   结构化数据：关系型数据库 MySQL，oracle表格数据都是结构化，有规律可循，有结构可做。

    ==非结构数据==：Redis /mongDB/hdoop没有整体结构，没有规律。常见的非结构化数据：页面，系统日志。

3. 非关系型：由于非结构化数据特点，无法实现数据与数据之间的关系

   Redis处理非结构化数据，存储数据格式不是表格而是key-value的内存结构，操作Redis语言叫做nosql，不能体现数据间关系，所以叫做非关系型。

5. 内存运行：Redis进程==基于内存运行==，数据存放在在内存中。

   特点：读写速度快，断电丢失数据

5. 可持久化：redis运行时，数据虽然存储在内存中，但是为了防止断电丢失数据，提供了可以将内存数据输出保存在磁盘中的方式（rdb aof）,一旦出现丢失内存数据时，可以启动Redis时，将磁盘持久化数据重新加载到内存。利用持久化的能力，Redis很容易解决容灾（容错灾难天灾人祸的发生）问题。降低了使用Redis实现缓存功能时==雪崩==的出现。

   持久化：将数据存储在磁盘中。

   雪崩：与缓存逻辑有关

   easymall中存在缓存逻辑：例如商品查讯系统

   ![](https://note.youdao.com/yws/api/personal/file/2A812BA5496B4B1C94718AA9536DFE9F?method=download&shareKey=c2935c286dedc38a93d477bfbf16da32)

   redis的内存逻辑，导致断电丢失，缓存数据丢失，就会瞬间造成系统高并发涌入数据库。数据库有可能无法承受瞬间的并发访问，导致宕机，系统崩溃造成缓存雪崩。

   redis的持久层能力，降低缓存出现雪崩的概率。

10. 分布式：分布式结构，有效的提升内存容量上限

    ![](https://note.youdao.com/yws/api/personal/file/C299E028CB2A4E6E92554C826ABC2E3C?method=download&shareKey=f0572343df5738921e9e83aa2f35beb3)

#### 3.服务端进程是单线程

1. 减少了一个进程中由于多线程的存在造成资源争抢和死锁。他们会浪费很多性能   

2. 减少了cpu在切换线程之间 资源转换，切换速度。  

3. 单线程不一定比多线程性能、处理速度差（吞吐量）。  

   单线程的redis的吞吐量，和多线程的缓存软件对比，吞吐量非常高。

   吞吐量---单位时间处理的数据量。秒级万条，秒级3条

4. redis中这个单线程是通过非阻塞IO多路复用实现的。秒级万条足够处理任何场景的数据吞吐量。---使用分布式缓存逻辑中，瓶颈卡在网络带宽。  

##  二、Redis的安装和运行

#### 1.登录链接云主机

##### 1.1 保证云主机运行的

使用tts找到云主机，点击启动，确保运行云主机。

注意：云主机总使用时长有限一定要记得关机！！！

##### 1.2 开启vpn软件

![](https://note.youdao.com/yws/api/personal/file/2ECCB308D0804A918EF10622053B3316?method=download&shareKey=b13a1cec19636317cf51e7f0a5c8a19c)

双击右下角的图标，弹出填写用户名密码的窗口

![](https://note.youdao.com/yws/api/personal/file/2143A934322542269968B1F4FE35AA56?method=download&shareKey=32a0aab8ca3f5aea07e27925dcb05861)

用户名密码:yzanxiaoyan     anxiaoyan

右下角出现绿色图标表示VPN登陆成功

![](https://note.youdao.com/yws/api/personal/file/BEFCDC28697E4CEAAD97BA633CA4748E?method=download&shareKey=c7c20d603773d54bf7264708d605815e)

使用云主机时，不能上传和下载大文件。所有的操作都在云主机进行，否则会占用VPN服务器的带宽。

##### 1.3 确保VPN开启正常的情况下，使用xshell链接登录

 配置xshell访问主机IP地址，填写主机用户名密码

用户名：root

密码：tarena2017Up;

1. 链接方式1：ssh root@IP地址  --》输入密码--》链接成功

2. 链接方式2：

   1. 新建会话

      ![](https://note.youdao.com/yws/api/personal/file/D526BB8B9BDE43B9907ECABDDDE4EB30?method=download&shareKey=5677169b008a3cc0bfbfa6fe0905f55a)

   2. 填写完成之后，可以链接登录这个节点，按照提示，填写服务器的用户名名称

      ![](https://note.youdao.com/yws/api/personal/file/396A66E385324669881AABD5BC82FC85?method=download&shareKey=e13f3eebcabf03104b9593256f28d89e)

   3. 最终看到登录成功

      ![](https://note.youdao.com/yws/api/personal/file/15387FB0EBD74743A1B2F4200AB72E61?method=download&shareKey=71e3318b29ebc50e51edcede8ba55f8e)

##### 1.4 注意的问题

1. 不能上传下载大文件  大于46k的都不能上传下载
2. 保证每天使用完云主机要关机处理，否则时长可能不够

#### 2.云主机环境

##### 2.1 云主机镜像

没有提供单独的文件，创建服务其环境的镜像。

自己的环境，需要自行搭建。（开发操作各种软件的可能性不大，但是具备linux基础知识是必要的）

##### 2.2 文件夹

在访问 home家目录时能看到4个文件夹，说明你的环境和我一样

```
#cd /home 
#ll
```

![](https://note.youdao.com/yws/api/personal/file/A1F92F1D17D341EEA80C8B0D7F6C91BE?method=download&shareKey=1e418e06408bd18b1ec2b71773fd8d1c)

es:一个普通用户es的家目录

presoftware：提前安装的以下环境需要的软件，比如语言环境 jdk node.js ruby等

resources：所有安装包，配置文件，jar包资源都在这里准备好了

software:课程中所有需要手动安装的软件，安装目录按照习惯都是安在/home/software下

#### 3.Redis的安装

##### 3.1 获取安装包

/home/resources文件夹下，在home、software去安装解压安装包

 安装包的名称：redis-3.2.11.tar.gz

```
[root@10-9-104-184 ~]# cd /home/software/
[root@10-9-104-184 software]# cp /home/resources/redis-3.2.11.tar.gz ./
```

software中就存在了一个redis的tar的安装包

![](https://note.youdao.com/yws/api/personal/file/8CC61970F609454497A592F14153FD8D?method=download&shareKey=e61779959084370b365d44f7c1b950d4)

##### 3.2 解压安装tar包

还是在同一个目录下software，执行tar的解压命令，使用选项 -xf

[root@10-9-104-184 software]# tar -xf redis-3.2.11.tar.gz 

解压完成后，在software中出现redis的软件根目录redis-3.2.11

![](https://note.youdao.com/yws/api/personal/file/153ED0F508D045B4A3FFEAD912E097B5?method=download&shareKey=7e361da91eefdd75568629e455650e4c)

##### 3.3 进入redis的根目录执行编译和编译安装

redis是C语言开发的， 安装包中存在很多源码，需要执行编译生成操作redis的所有命令，执行编译安装，让linux的环境认识记住这些命令，后续操作redis这些命令直接使用

```
[root@10-9-104-184 software]# cd redis-3.2.11
[root@10-9-104-184 redis-3.2.11]# make && make install
```

成功后看到5个INSTALL提示

![](https://note.youdao.com/yws/api/personal/file/96E6825B327E477696030A806081923F?method=download&shareKey=b6f26ba8b0ecb7f7560903fa8fb97f85)

## 三、入门启动和登录redis

#### 1.redis单进程单线程服务

redis访问使用的结构。非常类似数据库有服务端，有客户端

![](https://note.youdao.com/yws/api/personal/file/A7FA0E4BBD1144F697DCB058E51CDE40?method=download&shareKey=60255f4b4b328de183b6ce1d2fdae7a8)

redis-server服务端：是一个==单进程单线程==（单例）。

优势：减少了cpu切换线程的性能损耗，减少了线程安全问题，底层使用的是==非阻塞io多路复用==（可以同时处理多个不同的客户端线程资源）redis的这种结构，性能上限：==秒级处理万条数据。==通过网络访问redis时，不会出现使用redis性能瓶颈，先出现的瓶颈反而是网络带宽--吞吐量。

所有的开发者，运维人员和代码都是redis的客户端。都需要通过启动客户端进程。所以运行启动redis，访问发送命令，同时启动服务端和客户端进程。

#### 2.服务端和客户端脚本

##### 2.1 服务端脚本

进行解压之后，执行make编译时产生了操作redis的脚本命令

redis-server：启动redis服务端

redis-cli：启动连接服务端的客户端

脚本命令语法：

 ==redis-server== 配置文件。配置文件中可以指定配置服务端占用服务器的端口等配置内容。如果在执行命令时，不添加配置文件作为脚本参数，默认会有一批配置属性，例如，默认占用6379作为服务端的端口号，占用控制台，使用CTRL+C退出控制台日志打印，一并服务端进程也消失了（单独使用xshell窗口运行服务端）

由于make install的执行，在任意一个位置执行脚本命令都可以启动这个进程

[root@10-9-104-184 ~]# redis-server

日志提示内容最后一句话：

The server is now ready to accept connections on port 6379

##### 2.2 客户端脚本

redis-cli可以登录你在服务器上想要连接的一个服务端redis进程。

命令语法：

redis-cli 

==-h==:表示客户端进程要登录到服务端启动的服务器ip地址，10.9.39.13，默认127.0.0.1登录本地

 ==-p==:表示客户端进程要登录连接的服务端在服务器上占用的端口 8888，默认值6379

```
[root@10-9-104-184 redis-3.2.11]# redis-cli
127.0.0.1:6379> quit
[root@10-9-104-184 redis-3.2.11]# redis-cli -h 10.9.104.184（测试使用默认服务端时不要使用-h 登录服务端拒绝访问,服务端开启了保护默认）
10.9.104.184:6379>
```

可以使用客户端发送nosql命令，实现对服务端6379的内存数据，redis数据的操作。

```
[root@10-9-104-184 redis-3.2.11]# redis-cli
127.0.0.1:6379> set name wanglaoshi
OK
127.0.0.1:6379> get name
"wanglaoshi"
127.0.0.1:6379>
```

## 四、redis五种数据结构和命令

#### 1.redis的基础命令

set/get非常常用的字符串类型数据的写/覆盖，和读

以下的命令都是==不区分数据类型==（key-value类型是总类型，在redis中对于value数据结构是严格的区分的，存在五种不同的value数据类型）

##### 1.1 keys *

表示在客户端查看当前redis服务端内存中所有的数据key值。

```
127.0.0.1:6379> keys *
1) "location"
2) "gender"
3) "age"
4) "name"
127.0.0.1:6379>
```

将已有的数据返回，没有数据时，返回空。这里返回的都是内存中保存的数据key值

##### 1.2 exists key值

表示要查看一下对应的key值数据是否已经存在在redis内存中。

```
127.0.0.1:6379> keys *
1) "location"
2) "gender"
3) "age"
4) "name"
127.0.0.1:6379> exists age
(integer) 1
127.0.0.1:6379> exists haha
(integer) 0
```

##### 1.3 expire/pexpire key time

在redis中，可以根据需求对写入的数据设置超时时间，一旦到达超时条件将会在内存中把数据删除，==expire 对某个key的数据做秒单位的超时，pexpire对key值做毫秒单位的超时。==没有使用相关超时的数据写入时，默认是永久数据。

```
127.0.0.1:6379> expire location 5
(integer) 1
127.0.0.1:6379> keys *
1) "gender"
2) "age"
3) "name"
```

##### 1.4 ttl/pttl key

在执行设置了超时时间的key值上，查看这个key的剩余时间。

==ttl操作一个key能够看到剩余时间单位是秒，pttl看得是毫秒。==

```
127.0.0.1:6379> expire name 100
(integer) 1
127.0.0.1:6379> ttl name
(integer) 89
127.0.0.1:6379> pttl name
(integer) 83322
127.0.0.1:6379>
```

##### 1.5 del key删除数据

del可以对指定的key-value进行删除操作。

```
127.0.0.1:6379> keys *
1) "gender"
2) "age"
127.0.0.1:6379> del gender
(integer) 1
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379>
```

##### 1.6 save 存储持久化

redis支持持久化，将内存数据输出到持久化文件，内存数据保存在磁盘上。

redis重新启动时自动加载保存的持久化文件，将数据恢复回来。

save命令的调用，就是将内存数据刷出到持久化文件中保存。

```
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> get age
"18"
127.0.0.1:6379> save
OK
127.0.0.1:6379>
```

将redis服务端关闭 ctrl+c；内存中一定不会再存在age的数据。但是只要重新启动，由于持久化文件中存在数据，会启动时恢复到内存中。

#### 2.String类型数据

redis是以key-value结构存储数据的，但是根据不同的应用场景，可以使用完全不同的value结构存储数据。包括：String字符串，Hash，List链表，Set集合，ZSet有序集合

##### 2.1 set key value

redis中可以对字符串类型进行写操作调用set命令，也可以对已有的数据进行覆盖操作。redis的大量命令都可以携带很多不同的参数选项。使用详细的选项和参数，可以从redis官网(百度搜索redis第一个)去查看。

EX: 可以在set时直接设置超时秒数

PX:可以在set时直接设置超时毫秒数

```
127.0.0.1:6379> set bomb tnt EX 50
OK
127.0.0.1:6379> ttl bomb
(integer) 46
127.0.0.1:6379> 
```

NX：在执行set时，会判断redis中有没有该key值，如果有则无法set，没有则可以set成功。表示只有第一个set数据的人可以成功，后续都会失败。

```
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> set age 22 NX
(nil)
127.0.0.1:6379> set name wanglaoshi NX
OK
127.0.0.1:6379>
```

XX：在执行set时，会判断redis中有没有key值，有的时候才会set成功，没有则不成功。表示，使用XX的客户端没有新建的权限。

```
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> set gender male XX
(nil)
127.0.0.1:6379> set age 55 XX
OK
```

##### 2.2 get key

从redis中读取key值的value数据。==在redis中value最大数据长度1GB。==

```
127.0.0.1:6379> get age
"55"
127.0.0.1:6379> 
```

##### 2.3 incr/incrby decr/decrby

执行计步器，可以增加数值，减少数值。对应value字符串数据必须是纯数字

```
127.0.0.1:6379> incr age
(integer) 56
127.0.0.1:6379> decr age
(integer) 55
127.0.0.1:6379> incrby age 10
(integer) 65
127.0.0.1:6379> decrby age 20
(integer) 45
```

常见的应用使用计步器：

​	记录排队人数（拿号，自增，叫号后，前剩余人数自减）；

​	在线人数统计（每秒钟上下变动）

##### 2.4 数据类型String应用场景

一般使用String类型的value数据实现缓存的功能。并且可以利用代码的序列化和反序列化的方法，将对象序列化为字符串（user-->{“userName”:“wanglaoshi”}）。在easymall中使用序列化将product对象变成json，以商品id作为唯一key值操作商品在redis的缓存数据。

#### 3.hash类型

hash在redis中==底层双层map形式==存在，key-value是map，value在hash结构中又是一个map。所以他可以对应对象的数据结构。

![](https://note.youdao.com/yws/api/personal/file/1562868858CA4E6D8EFC0BE4242AE091?method=download&shareKey=46327d43730f1deaf2bfd08a145fa3d1)

##### 3.1 hset key field value

在redis中创建一个key值为key 对应第二层map的key为field，

第二层map的value为value

```
127.0.0.1:6379> hset user age 18
(integer) 1
127.0.0.1:6379> hset user name wanglaoshi
(integer) 1
127.0.0.1:6379> hset user gender male
(integer) 1
127.0.0.1:6379> keys *
1) "age"
2) "user"
127.0.0.1:6379>
```

##### 3.2 hget key field

由于hash结构双层map，hget可以读取到一个属性的值，指定某个key的 某个属性读取

```
127.0.0.1:6379> hget user name
"wanglaoshi"
127.0.0.1:6379> hget user age
"18"
127.0.0.1:6379>
```

##### 3.3 hkeys/hvals key

hkeys key从key值的hash数据结构中将所有的field属性名称返回

hvals key 从key的hash数据结构中将所有的field的值返回

![](https://note.youdao.com/yws/api/personal/file/9CA3CD7EA81340DB86A3DA1EE0DB8A36?method=download&shareKey=51673b706cf9509200038155656c8bdd)

```
127.0.0.1:6379> hkeys user
1) "age"
2) "name"
3) "gender"
127.0.0.1:6379> hvals user
1) "18"
2) "wanglaoshi"
3) "male"
127.0.0.1:6379>
```

##### 3.4 删除数据

hdel key field：删除的是一个hash结构中的某个属性和值。

hdel key :将整个hash结构删掉，直接调用 hdel key 全部删除。

```
127.0.0.1:6379> hkeys user
1) "age"
2) "name"
3) "gender"
127.0.0.1:6379> hdel user age
(integer) 1
127.0.0.1:6379> hkeys user
1) "name"
2) "gender"
127.0.0.1:6379>
```

##### 3.5 应用场景

也可以想String一样，执行某个项目中的缓存逻辑。不同环境使用不同结构，有不同的效果。

例如，缓存数据的结构比较复杂，属性不仅仅是Integer、String 还有数组，list，还有set，优先使用String类型，如果对象属性简单可以使用hash（造成代码编写复杂）

#### 4.List双向链表

list==底层双向链表==，可以从头和尾部处理数据，实现队列的结构（就为了处理消息队列逻辑）。

![](https://note.youdao.com/yws/api/personal/file/0AFD48406910462B8DCF07ADD7FFE1CB?method=download&shareKey=36cf15d9533c81f0ac26e7712fa527de)

##### 4.1 lpush/rpush key value

l/r 表示左和右。lpush从链表头部，插入数据，rpush从链表的尾部，插入数据

```
127.0.0.1:6379> lpush student wangcuihua
(integer) 1
127.0.0.1:6379> lpush student liuyoucai
(integer) 2
127.0.0.1:6379> rpush student zhangshoufu
(integer) 3
127.0.0.1:6379>
```

行程链表结构顺序：

liuyoucai 

wangcuihua 

zhangshoufu

##### 4.2 lrange key start end

可以对一个list链表中的元素范围内的数据读取返回。

lrange student 起始下标 结束下标

```
127.0.0.1:6379> lrange student 0 4
1) "liuyoucai"
2) "wangcuihua"
3) "zhangshoufu"
127.0.0.1:6379> lrange student 1 4
1) "wangcuihua"
2) "zhangshoufu"
127.0.0.1:6379> 
```

有时候，咱们并不会确定不知道元素的个数，要想查看所有的元素可以使用-1的end结尾

表示一直到尾部。start=0 end=-1 就可以查询一个list所有内容

```
127.0.0.1:6379> lrange student 0 -1
1) "liuyoucai"
2) "wangcuihua"
3) "zhangshoufu"
127.0.0.1:6379> 
```

##### 4.3 lset key 元素下标index 修改值

对list的某个下标为index的元素值，做修改。

```
127.0.0.1:6379> lrange student 0 -1
1) "liuyoucai"
2) "wangcuihua"
3) "zhangshoufu"
127.0.0.1:6379> lset student 1 haha
OK
127.0.0.1:6379> lrange student 0 -1
1) "liuyoucai"
2) "haha"
3) "zhangshoufu"
127.0.0.1:6379>
```

##### 4.4 rpop/lpop key

rpop从链表的尾部删除元素，并且将删除的元素值返回

lpop从链表的头部删除元素，将元素值返回（remove）

```
127.0.0.1:6379> lrange student 0 -1
1) "liuyoucai"
2) "haha"
3) "zhangshoufu"
127.0.0.1:6379> rpop student
"zhangshoufu"
127.0.0.1:6379> lpop student
"liuyoucai"
127.0.0.1:6379> lrange student 0 -1
1) "haha"
127.0.0.1:6379>
```

##### 4.5 应用场景

初始目的就是利用list的类型实现排队队列的处理逻辑，先来先得，先到先处理。

可以在元素中封装一些消息属性，先进入队列，优先被pop出去进行处理。

easymall使用list数据，实现高并发争抢线程资源的权限设置，解决防止线程安全的出现。

#### 5.set集合

可以将不同的，不重复的元素值，放到一个没有顺序概念的集合中，实现value数据在redis中的管理。

![](https://note.youdao.com/yws/api/personal/file/27A7D7A609E740A589C6DAEDDFAC5E72?method=download&shareKey=50b8143af9297ad84d3f4304538a295d)

##### 5.1 常用场景

登录系统时，给用户帖的标签。例如：

登录一些今日头条这种推荐文件，推荐网站的系统，注册后选择自己兴趣爱好：军事，数学，历史，天文，登陆后会跟你选择的内容，随机推荐不同的文章。

直播网站：lol 王者荣耀 dota2其他，根据兴趣推送相关直播

还可以实现好友保存，计算共同好友

![](https://note.youdao.com/yws/api/personal/file/8AF5256121A649FEB9577750098B1829?method=download&shareKey=febd2c768098131a380265a8f2ba99dc)

##### 5.2 sadd key member

新增元素到集合中。

```
127.0.0.1:6379> sadd favor math english geo
(integer) 3
```

##### 5.3 srandmember key [count]

随机在key对应set集合中选取count个元素。

```
127.0.0.1:6379> srandmember favor
"english"
127.0.0.1:6379> srandmember favor 2
1) "math"
2) "geo"
```

##### 5.4 srem key 元素值

可以对集合中某个元素进行去除的操作，当删除成功，返回1，删除失败返回0。

```
127.0.0.1:6379> srem favor math
(integer) 1
127.0.0.1:6379> srem favor mathdasfdads
(integer) 0
127.0.0.1:6379>
```

#### 6.ZSET有序集合

在set基础之上，实现了排序的方式，元素也是不可以重复，就是在元素的数据上绑定了一个评分的数据（实际应用场景中，评分可以不同业务意义，例如点击率，例如播放量，例如投票数量等）

![](https://note.youdao.com/yws/api/personal/file/FEFD12F5C92D4DAE9D509DA614E9D7E7?method=download&shareKey=8dd5668e2b3b03d073cfbad71e047a9e)

##### 6.1 常用应用场景

网站各种排名，都可以使用ZSET有序集合

视频网站：热播剧，top10。

热搜：话题搜索次数。

小说网站：订阅量排序，月票排序

##### 6.2 zadd key score member

可以将一个元素绑定一个分数后，写入到一个有序集合中

```
127.0.0.1:6379> zadd score 98 piaoqian
(integer) 1
127.0.0.1:6379> zadd score 97 haoxia
(integer) 1
127.0.0.1:6379> zadd score 80 caoyang
integer) 1
127.0.0.1:6379> zadd score 15000 xiaoxuwei
(integer) 1
```

##### 6.3 排序查询rank/range

在有序集合中主要就是为了体现排序，所以使用命令最多的查询方式

就是排序相关内容

1. zrank key member:看看member的排名

   ```
   127.0.0.1:6379> zrank score piaoqian
   (integer) 2
   ```

2. zrange key start stop：查询排名从start开始到stop范围内所有元素起始排名是0，可以对stop=-1查到末尾,默认升序排列。

   ```
   127.0.0.1:6379> zrange score 0 -1
   1) "caoyang"
   2) "haoxia"
   3) "piaoqian"
   4) "xiaoxuwei"
   127.0.0.1:6379> 
   ```

3. zrangebyscore key minscore maxscore:在上限评分和下限评分之间的所有元素和排序

   ```
   127.0.0.1:6379> zrangebyscore score 50 200
   1) "caoyang"
   2) "haoxia"
   3) "piaoqian"
   ```

easymall中如何使用redis的服务端，如何封装redis的客户端。java作为redis的客户端代码，是有对应封装客户端的jar包资源的，只要在代码中引入jar包资源就可以实现通过代码调用api的方式像在这里操作执行nosql命令一样去操作redis的数据

对于以上的执行命令的，自习中，执行一遍有印象即可，对于常用的set get exists expire ttl方法去调用。

## 五、redis的配置文件

#### 1.介绍

使用redis一定会接触到这个配置文件，配置自定义的各种redis属性，资源。redis给提供了一个配置文件模板 在redis根目录 redis.conf.

#### 2.配置文件实现自定义配置

使用java无法直接连接，因为默认启动的redis-server 开启了保护模式。一旦有保护模式，只允许本地客户端通过127.0.0.1访问，外界无法访问。并且后续搭建简单分布式结构需要在同一台服务器上启动多个redis-server，不可能3个多个redis同时占用6379端口，所以需要根据自定义结构来配置redis启动时配置文件，通过启动server加载配置文件实现后续学习和操作。

#### 3.redis的配置文件

redis-server脚本（cd cp ifconfig mv tar都是系统的命令文件），在执行make install才会添加到当前系统的环境变量中，使得在任意系统位置目录下都能都调用。

启动脚本可以携带一个启动参数，这个参数就是自定义配置的redis配置文件

redis-server 配置文件名称

怎么配置，才能实现自定义属性：端口自定义，启动方式自定义，超时剔除客户端自定义。

只要找到根目录中有模板文件。/home/software/redis-3.2.11/redis.conf

![](https://note.youdao.com/yws/api/personal/file/24BD4072D7854B8897497E4F8EA638BD?method=download&shareKey=62002f8453d927ab01254097092d9197)

#### 4.修改模板文件

##### 4.1vim打开配置文件

[root@10-9-104-184 redis-3.2.11]# vim redis.conf 

vim使用注意：使用键盘的快捷键操作，使用键盘的上下键换行，使用pageup pagedown翻页，不要使用鼠标滚轮。

vim使用方式：

进入配置文件：一般模式

​	dd删除光标所在一行

​	ndd从光标所在行向下删除n行（删除的内容会存储到剪切板）

​	yy复制光标所在行

​	nyy复制n行。

​	p粘贴剪切板内容到光标所在行下一行

​	u回退一次操作。

​	i进入编辑模式。

​	ESC从编辑模式退出，如果使用xshell从window的文件中粘贴文本到配置文件，必须在编辑模式

​	退出编辑模式输入命令：:q :q! :w :wq

##### 4.2 配置文件讲解

###### 4.2.1 61行：bind绑定ip地址

bind注释掉，防止限制登录客户端。如果开启，redis服务端只允许使用127.0.0.1登录连接访问的客户端。只允许本地客户端登录。

![](https://note.youdao.com/yws/api/personal/file/6A77145F229746D88C9AF5C852F3D960?method=download&shareKey=9fb2d98837d852b124ca847c3920cf41)

###### 4.2.2 80行：protected-mode 安全模式关闭

默认redis服务端开启安全模式，在安全模式下，即使远程客户端能够连接到redis，也无法操作redis执行各种安全相关要求的命令，比如不能set数据。

![](https://note.youdao.com/yws/api/personal/file/FFE9ABF11DC548E5A6AD07011EF790FF?method=download&shareKey=1c590a2fd592c25f97cd53bf53346970)

###### 4.2.3 84行：端口配置值

不用改变

使当前模板文件就是使用6379作为redis服务端一个标识内容。很多配置和端口有关。

![](https://note.youdao.com/yws/api/personal/file/207D95852382489C927FCB142F509B7D?method=download&shareKey=1ab6d20f825f3cbf99891327df0455b7)

###### 4.2.4 128行：后台启动开启

默认配置中，redis服务端都是占用控制台启动的，&才能后台运行。redis提供一个配置，默认启动就占用后台进程。

![](https://note.youdao.com/yws/api/personal/file/6D3EF2C7056D4424AB373C31BED8B41E?method=download&shareKey=d4a9c85c734333dff15ec273c6712e76)

###### 4.2.5 150行：pid文件

pid文件，==在同一个服务器上运行的不同redis服务端的pid不一样的==，不能让他们公用一个pid文件。redis_6379.pid redis——6380.pid.....区分这些文件对应的不同redis-server

![](https://note.youdao.com/yws/api/personal/file/504C9725A6AD48E3BA29C5D5CE0F0A75?method=download&shareKey=002f6c27e99e6144832ed74b0d33681e)

###### 4.2.6 163 行：指定日志文件名称

默认使用6379redis.log名称在redis的根目录根据启动的进程创建对应的日志。

![](https://note.youdao.com/yws/api/personal/file/8911752608F9477FB412AE4BD83C13B2?method=download&shareKey=da574fc146cfcfd6378db1bfcf18273d)

###### 4.2.7 237行：持久化文件dump对应redis端口命令

在同一个服务器上启动多个redis服务端进程，不能有相互冲突的东西存在。端口号，pid文件，log日志，dump持久化数据

###### 4.2.8 修改完成后的操作

1. 从编辑模式点击esc,进入一般模式:wq保存退出

2. 加载这个模板文件启动

   ```
   [root@10-9-104-184 redis-3.2.11]# redis-server redis.conf
   ```

   关闭这个redis-server的两种方式

   1. ps查看进程pid，然后kill

      ```
      [root@10-9-104-184 redis-3.2.11]# ps -ef|grep redis
      ```

      ![](https://note.youdao.com/yws/api/personal/file/4E6EFAD0D5F94FE2ABBCB946CB11BA2F?method=download&shareKey=9c15ec63e8e0bcbbdf251d1c645b81e0)

   2. 使用redis-cli 命令执行shutdown

      ```
      [root@10-9-104-184 redis-3.2.11]# redis-cli shutdown
      ```

#### 5.三个redis服务启动

1. 拷贝模板文件成为三个节点的配置文件

   ```
   [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis6379.conf
   [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis6380.conf
   [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis6381.conf
   ```

   redis6379的内容已经不需要修改了

2. 修改6380 6381对应的配置内容，否则会出现同时占用6379端口冲突

   1. 6380

      vim编辑器打开redis6380.conf

      ```
      [root@10-9-104-184 redis-3.2.11]# vim redis6380.conf 
      ```

      所有6379的名称 端口 日志 pid 持久化文件都需要改成6380。

      在一般模式进入命令替换的输入：

      ```
      :%s/6379/6380 //将原文中6379替换成6380
      ```

   2. 6381:

      替换过程与6380相同

3. 使用三个配置文件启动三个redis-server进程

   1. #redis-server redis6379.conf
   2. #redis-server redis6380.conf
   3. #redis-server redis6381.conf

4. 确定当前3个节点redis-server都启动成功

   ```
   #ps -ef|grep redis
   ```

   ![](https://note.youdao.com/yws/api/personal/file/8FEED26AB36C478F899833D9D288F61B?method=download&shareKey=6ec35578182dfc61eb6a8e638d4f00b7)

5. 常见问题：如果发现启动失败。

   1. 首先检查生成的log文件是否存在，比如6381没启动，根目录检查6381的log文件。如果没有配置文件日志端口没改，如果有打开看看日志内容（养成习惯，任何软件启动运行，故障都要看日志）。
   2. 把配置文件删除，重新拷贝配置。

## 六、redis的Java客户端

#### 1.客户端

redis作为流行的缓存软件，支持非常丰富语言环境 例如：C C# C++ Python等等。我们使用Java语言的客户端--jedis。

![](https://note.youdao.com/yws/api/personal/file/BC6E7FEB4706462A9E0457F05BDC84FA?method=download&shareKey=fbff45ea55bc180ecdf891dbdf4328ae)

有很多的客户端jar资包源。不需要学习所有客户端代码的所有连接操作逻辑，所有的api方法，只需要学习一部分重要的常用的api。

#### 2.jedis的代码测试

##### 2.1 工程中添加相关依赖

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-redis</artifactId>
</dependency>
```

##### 2.2测试案例

1. 使用jedis对象链接操作一个redis服务节点

   left左边，right右边。

   ```Java
   /*
   	如何使用jedis链接到对应的redis服务端
   */
   @Test
   public void test01(){
   	//相当于从java代码执行 redis-cli -h 10.9.104.184 -p 6379
   	Jedis jedis=new Jedis("10.9.104.184",6379);
   	//可以利用jedis对象调用api方法操作redis
   	jedis.set("name","王老师");	      
     System.out.println(jedis.get("name"));
   	jedis.exists("name");
   	jedis.lpush("list01","1");
   	jedis.rpop("list01");
   	jedis.hset("user","age","18");
   }
   ```

2. 利用jedis对象实现登陆逻辑

   1. 了解登陆执行，用户状态获取的流程（对应接口文件中2个方法）

      ![](https://note.youdao.com/yws/api/personal/file/D2B38CA2295746CE9BF6611E0B961780?method=download&shareKey=6b0542b0ebfa8fb5b5d97ad93ffde6e8)

   2. 登录

      | 后台接收 | /user/manage/login                                           |
      | -------- | ------------------------------------------------------------ |
      | 请求方式 | Post                                                         |
      | 请求参数 | User user 只有用户名和密码,查询需要加密password              |
      | 返回数据 | 返回SysResult对象的json,其结构:Integer status; 200表示成功,其他表示失败String msg;成功返回 “ok”,失败返回其他信息Object data;根据需求携带其他数据 |
      | 备注     | 根据查询结果 ，判断登陆合法。决定如何使用redis存储数据。在成功时编写cookie相关代码实现将数据关键key返回（ticket） |

   3. UserController

      ```Java
      //接受登陆请求，判断合法和存储redis
      @RequestMapping("login")
      public SysResult doLogin(User user, HttpServletRequest req, HttpServletResponse res){
      	//判断 业务层返回数据，是否生成了存储在redis中的key值 ticket
      	String ticket= us.doLogin(user);	
        if("".equals(ticket)||ticket==null){
      		//说明业务层没有存储redis，说明用户名密码不正确
      		//登陆失败
      		return SysResult.build(201,"登陆失败",null);
      	}else{
      		//正确存储到redis 登陆成功，控制层使用cookie技术，将返回ticket值
      		//带回给浏览器，浏览器客户端才能在登陆之后，有了一张类似票的概念的数据
      		//后续访问系统只要判断这张票是否合法 是否超时。
      		//调用common-resources cookieUtils
      		//其中set方法需要request和response对象 在controller里可以从springmvc拿到			   
      		CookieUtils.setCookie(req,res,"EM_TICKET",ticket);
      	//返回数据，告诉ajax登陆成功的
      	return SysResult.ok();
      }
      }
      ```

   4. UserService

      - ObjectMapper两个api方法
        - writeValueAsString:将对象转化成json
        - readValue:将json字符串转化成对象


      ```Java
      public String doLogin(User user){
      /*
      	1 判断合法
      	2 存储redis
      */
      	// 准备一个返回的字符串，默认值""
      	String ticket="";
      	//使用user参数 查询数据库，判断是否存在user行数据 验证合法
      	//user里明文password加密
      	user.setUserPassword(MD5Util.md5(user.getUserPassword()));
      	User exist=um.selectUserByNameAndPw(user);
      	//select * from t_user where name= and pw=
      	if(exist==null){
      		//说明没有查询到user对象登陆时不合法的
      		return ticket;
      	}else{//说明合法数据
      		//创建key-value的数据将用户信息存储在redis供客户端后续访问使用
      		//准备一个数据 userJson字符串 可以将密码做空 redis的value
      		//调用一个对象的api方法 ObjectMapper 可以将json和对象进行相互转化 writeValueAsString
      		//readValue
      		exist.setUserPassword(null);
      		//将exist 转化成字符串
      		ObjectMapper om= MapperUtil.MP;
      		//准备好链接redis的对象jedis
      		Jedis jedis=new Jedis("10.9.104.184",6380);
      		try{
      		String uJson = om.writeValueAsString(exist);
      		//给ticket赋值，ticket体现特点：用户不同时，ticket要不同，同一个用户，不同
      		//时间登陆，生成的ticket也不同		                	
      		ticket="EM_TICKET"+System.currentTimeMillis()+user.getUserName();
      		//需要存储在redis的key值和value都准备号了，下面可以set方法调用6380
      		jedis.setex(ticket,60*60*2,uJson);//不能set永久数据，假设超时时间2小时
      	}catch(Exception e){
      		e.printStackTrace();
      		return "";
      	}finally {
      		if(jedis!=null){
      			jedis.close();
      		}
      	}
      	//当上述逻辑全部执行完毕，ticket该赋值，就赋值了
      	return ticket;
      	}
      }
      ```

   5. 获取登陆存储用户信息

出现DENIED错误，原因：redis服务端没有关闭protected-mode

解决办法 config命令：可以通过配置文件**.conf去修改

#### 3.jedis连接池

##### 3.1 Pool连接池对象

直接创建一个Jedis对象，使用api操作redis服务端。从实现功能角度没有什么问题，但是在代码中使用一个对象，需要使用完了销毁，性能比较低。使用池的结构，创建一批可以连接redis节点的Jedis对象，使用就获取一个，用完了就还回一个，还可以根据应用场景，应用性能要求，对池的结构做调整。初始化个数，最大个数，最大空闲书，最小空闲数等等。

![](https://note.youdao.com/yws/api/personal/file/D1F5315230684FB09F4032797415A389?method=download&shareKey=e67d6b3697de2b738150e16d8b5cea18)

##### 3.2 测试代码

```Java
	@Test
	    public void test04(){
	        //创建连接池对象，先准备一个连接池属性配置对象
	        JedisPoolConfig config=new JedisPoolConfig();
	        //定义最大连接数 最小空闲，最大空闲，初始化等等
	        config.setMaxTotal(200);//最大连接数，当最小空闲不满足时，创建新的连接，上限200
	        config.setMaxIdle(8);//最大空闲数，当jedis空闲数量超过最大空闲，会关闭多余的jedis
	        config.setMinIdle(3);//最小空闲数,不满足最小空闲说明忙，按照最大空闲的上限，补充jedis
	        //使用config对象创建jedisPool
	        JedisPool pool=new JedisPool(config,"10.9.104.184",6380);
	        //连接池底层就是按照给定属性，创建一批连接6380的jedis
	        Jedis resource = pool.getResource();//相当于从连接池拿到一个空闲的jedis对象
	        resource.set("name","王老师");
	        pool.returnResource(resource);
	    }
```

##### 3.3 分片连接池对象

像JedisPool管理的是多个Jedis对象一样，ShardedJedis管理的是多个ShardedJedis连接对象，从中获取的分片对象，使用完毕可以还回资源

![](https://note.youdao.com/yws/api/personal/file/AEF5C1A86D1A45B892D136F4E80BCB84?method=download&shareKey=17ea95e66e60ed06a7355ede2059f614)

## 七、redis的三节点分布式

#### 1.分布式三个节点结构

![](https://note.youdao.com/yws/api/personal/file/59A5114E3E474CEB8B6BB78431EA31F9?method=download&shareKey=0b2ce5aefcf7d043a01511182e17fdda)

使用多个节点对应数据生成的系统的处理数据的读写工作，多个节点每个都处理了总数据量一部分数据---数据分片。

#### 2.数据分片计算

![](https://note.youdao.com/yws/api/personal/file/49DDAB31102A46E09F253B41203F44B1?method=download&shareKey=03e0ead0bd96a744a7a1bd3fd198cd79)

大量的数据即将发送给redis节点进行处理之前，必须计算完毕，某一条数据应该交给谁交给那个节点去进行读写的工作，而且考虑单调性 。

单调性：存哪里了，就应该去哪去找数据。

##### 2.1 hash取余

###### 2.1.1 hash取余的计算公式

hash取余可以实现一个简单的分片计算逻辑，进行大量数据的切分计算然后交给不同节点管理维护数据，并且保证了单调性。任何一条key-value如果存储在节点A，必定也能实现读取时找到节点A。

1. Java可以利用一个公式来完成hash取余算法的代码编写：(key.hashCode()&Integer.MAX_VALUE)%n
2. key:表示redis要处理的key-value数据，key的取值范围就是字符串范围
3. key.hashCode():hashCode()方法，是Object类型的散列计算方法。可以将任意一个内存java对象映射到对应的一个integer整数范围区间。并且，只要对象不变（equal），整数值不会发生变化的。但是可正可负。
4. key.hashCode()&Integer.MAX_VALUE:利用&做位的与运算，实现31位的保真运算，保证整数是正数。

保真运算：根据保真运算的1的位数，做N位保证，保证N位二进制和之前相同，其他的都是0

![](https://note.youdao.com/yws/api/personal/file/0AFCFE764F6942879E5A8CEF5BEB6CFD?method=download&shareKey=428781b44b91f99e79360ecf2e41dcd6)

任意一个负数int类型，二进制特点 第一位一定是1，一共32位，一旦与最大正整数（31个1）做位的与运算，将会把第一位的1变成0.一定是个正整数。

![](https://note.youdao.com/yws/api/personal/file/C3D283E9515847888A10797C7FC9703B?method=download&shareKey=bd23a4f194737bbbbe1fe4fdf91d49e6)

一个整数与上最大正整数之后，一定是正整数。而且key值不变，正整数值不变。

(key.hashCode()&Integer.MAX_VALUE)%N

​	N:就是数据分片的个数（redis结构中，就是redis节点个数）就是3. key值任意取值，上述公式得到的结果就是[0,1,2]其中一个。

可以利用hash取余算法，实现key值对应节点的对应关系

```
伪代码：
对key做hash取余得到0,1,2其中一个数字
if(==0){
	该key值交给 6379处理
}
if(==1){
	该key值交给6380处理
}
if(==2){
	该key值交给6381处理
}
```

1.模拟一个包装了hash取余算法的分片计算对象的逻辑

单独开发一个对象MyShardedJedis，创建这个对象时，给它包装

一个底层所有redis节点集合，利用集合实现分片计算

1. 编写一个自定义的分片对象

   ```
   				public class MyShardedJedis {
   				    //属性，list节点信息，保管了所有的集群节点内容
   				    //有三个节点 list元素3个，5个节点 list元素5个
   				    List<Jedis> nodes=new ArrayList<>();
   				    //构造方法，每次构造使用这个对象，必须传递所有节点的信息。
   				    public MyShardedJedis(List<Jedis> nodes){
   				        this.nodes=nodes;
   				       //nodes={new Jedis(6379),new Jedis(6380),new Jedis(6381)};
   				        //下标 取余结果0 1 2
   				    }
   				    //封装一个hash取余的算法，对任意key值，找到对应节点
   				    public Jedis keyAndNode(String key){
   				        //hash取余公式 name 取余 0
   				        int result=(key.hashCode()&Integer.MAX_VALUE)%nodes.size();
   				        return nodes.get(result);
   				    }
   				    //只要对jedis的所有操作key值的方法重新封装一遍，当前分片对象可以使用了
   				    //get set
   				    public String get(String key){
   				        //传进来的key对应了哪个节点
   				        Jedis jedis = keyAndNode(key);
   				        return jedis.get(key);
   				    }
   				    public void set(String key,String value){
   				        //传进来的key对应了哪个节点
   				        Jedis jedis = keyAndNode(key);
   				        jedis.set(key,value);
   				    }
   				    public boolean exists(String key){
   				        //传进来的key对应了哪个节点
   				        Jedis jedis = keyAndNode(key);
   				        return jedis.exists(key);
   				    }
   				    //....
   				}
   
   ```

2. 测试分片对象

   ```
   				 //分片计算逻辑测试
   				    @Test
   				    public void test(){
   				        //创建一个分片对象，提供3个节点的信息
   				        List<Jedis> nodes=new ArrayList<Jedis>();
   				        nodes.add(new Jedis("10.9.104.184",6379));
   				        nodes.add(new Jedis("10.9.104.184",6380));
   				        nodes.add(new Jedis("10.9.104.184",6381));
   				        MyShardedJedis msj=new MyShardedJedis(nodes);
   				        //使用for循环模拟大量不同key-value数据的生成
   				        for(int i=0;i<100;i++){
   				            //每循环一次，模拟系统需要操作redis分布式集群处理的数据一次
   				            String key= UUIDUtil.getUUID()+i;
   				            String value="name"+i;
   				            msj.set(key,value);
   				            System.out.println(msj.get(key));
   				        }
       }
   ```

##### 2.2 jedis的底层计算分片逻辑---一致性hash

1. jedis操作redis的分布式集群时，可以创建一个分片对象ShardedJedis。底层计算hash取余得到的key对应节点的逻辑也保证了单调性。

   ```
   			@Test
   			    public void test03(){
   			        //创建一个可以实现分片计算的对象ShardedJedis
   			        //收集节点信息，交给分片对象
   			        List<JedisShardInfo> nodes=new ArrayList<>();
   			        //6379,6380 6381节点信息存到nodes中
   			        nodes.add(new JedisShardInfo("10.9.104.184",6379));
   			        nodes.add(new JedisShardInfo("10.9.104.184",6380));
   			        nodes.add(new JedisShardInfo("10.9.104.184",6381));
   			        //构造一个分片对象ShardedJedis
   			        ShardedJedis sj=new ShardedJedis(nodes);
   			        for(int i=0;i<100;i++){
   			            String key=UUIDUtil.getUUID();
   			            String value=""+i;
   			            sj.set(key,value);
   			            System.out.println(sj.get(key));
   			        }
       }
   ```

2. 一致性hash

   1. 当前环境中，使用redis集群中，做hash取余分布式数据切分计算，存在隐患。

      hash取余会造成集群扩容（加节点），缩容（减节点）时，数据未命中的概率较高。
      
      ![](https://note.youdao.com/yws/api/personal/file/9C6AA998C95E4CCBAE20411D8E9A956B?method=download&shareKey=e88e1322290710b98f09c806e3082ef4)
      
      数据未命中的概率会随着节点增多而提高。数据未命中概率越高，出现扩容后雪崩的概率就越高。所以，在redis这种集群计算数据分片的环境中，不能光考虑单调性，也要考虑这种扩容缩容对单调性的影响。可以使用一致性hash算法。
      
   2. 一致性hash的逻辑

      1. 介绍

         1997年由麻省理工提出并创造的一种散列计算。核心结构就是散列计算，可以将任意数据经过散列计算后得到[0<--->2^32-1(43亿)]这样一个正整数的区间。我们称这样的一个区间叫做hash环，底层是二进制，只关心32位，所以0-1=最大正整数 最大正整数+1=0。

         ![](https://note.youdao.com/yws/api/personal/file/14982FCE8B3D4E98AB98BA7EE4D6CE6E?method=download&shareKey=1e40b7f538e853bd5c6e600f1576eaf1)

      2. 节点映射

         redis包括其他技术中，启动的节点信息 10.9.104.184:6379可以作为字符串数据使用，不同的节点这个字符串不相同。可以利用这样的数据做散列计算得到节点对应hash环中的整数。

         ![](https://note.youdao.com/yws/api/personal/file/5785B74FFCC948D7B74A3D43C9AE2E24?method=download&shareKey=a509f8410d752e144bbf3ac64a91d68b)

      3. 数据key值的映射

         key值作为字符串也可以在hash环上做散列计算整数映射。

         ![](https://note.youdao.com/yws/api/personal/file/921537A80D484C12A984E33BCEDEBB75?method=download&shareKey=8c051738cbe9545a904ed41de7a6677b)

      4. 数据对应节点的关系

         使用hash取余也好，使用一致性hash也好，都需要找到key对应的节点，并且保证key不变对应node节点也不变。

         在hash环中，一致性hash算法利用整数，把key的整数做==顺时针寻找==最近的节点整数关系，得到对应结果。

         ![](https://note.youdao.com/yws/api/personal/file/7A7B8375674D459A9D9E6185DFB7168B?method=download&shareKey=420cf22cbb7f30fd7309aa223dfd0d61)

         上图中 6379存储 key 5,2,1

         6380处理key3

         6381处理key4

         单调性保证：key值不变，节点不变，整数值不变，顺时针寻找的结果就不变，到哪存的就会到哪去取。

      5. 扩容时出现的情况

         ![](https://note.youdao.com/yws/api/personal/file/85404F76A7784C42B7AFA211546C10F4?method=download&shareKey=a838bd71a6393db0ff800875d502e581)

         新加入的节点依然做hash散列映射到hash环中，key值映射不变，依然做顺时针寻找最近节点整数对应关系，所以当原始节点越多时，扩容添加的节点，不会造成越大的数据量未命中，恰恰相反，原始节点越多，扩容缩容的影响就越小。

      6. 平衡性

         当节点数量不多时，刚好两个映射整数靠的比较近，会造成数据分片倾斜

         ![](https://note.youdao.com/yws/api/personal/file/C387DE58C23E42B5AB770CC5B8BDF6DE?method=download&shareKey=235d2d5eb705178cd20e3862160406a6)

         一致性hash的平衡性的问题，引入了虚拟节点的概念。每一个真实节点并不是独立的计算到整数环中，使用每个真实节点，计算一批数据。

         ![](https://note.youdao.com/yws/api/personal/file/3A52C26AEFD1468BB692111955CF9898?method=download&shareKey=4020007676f8b7d76290d36aa0432b9c)

         引入的虚拟节点，也做整数映射，这时，key值依然做顺时针寻找最近节点的逻辑，找到虚拟节点就对应到该节点真实节点处理数据。虚拟节点计算个数越多，hash环被真实节点切分的弧线越平均。并且可以利用虚拟节点的个数的比例，实现数据的对应比例的权重计算。默认情况，jedis实现一致性hash每个节点的虚拟节点个数为 160*weight权重值。可以按照服务器性能，容量的比值，去分配不同权重，实现虚拟节点个数的计算比例，完成数据权重划分。

## 八、redis设置密码

#### 1.设置密码

##### 1.1 持久化密码

配置文件中配置一个key-value

requirepass 123456

##### 1. 2临时密码

需要在本地客户端登录 通过config set requirepass 重新启动的redis有没有密码取决于配置文件

##### 1.3 客户端使用密码

![](https://note.youdao.com/yws/api/personal/file/4218DBE5BC1348E6A999FD596736DDF0?method=download&shareKey=e8e79d4ab217b964127e02588fcb5b26)

使用客户端登录后，使用命令发送，会提示没有提供操作密码

![](https://note.youdao.com/yws/api/personal/file/44FC2111884245D289F32530464F3E94?method=download&shareKey=58f08ad7a53731959fe29d05c3d288ff)

所有客户端在操作redis之前需要先调用命令或者方法jedis

```
jedis auth(密码)
jedis.auth("123456");	
redis-cli auth 密码
127.0.0.1:6380> auth 123456
OK
127.0.0.1:6380> set name wanglaoshi
OK
127.0.0.1:6380> get name "wanglaoshi"
127.0.0.1:6380>
```

#### 2.企业网络环境

很多企业具备内部网络，这些网络中运行的redis服务器可以省略提供密码安全保证

只要是10开始的都是内网的地址

## 十、高可用

#### 1.已有redis结构的问题

##### 1.1 当前系统连接的redis结构图

![](https://note.youdao.com/yws/api/personal/file/F26A6AAA60324B078EF6213B66063125?method=download&shareKey=caf6febf7faf3998c233e729395532c2)

easymall作为redis集群的客户端系统，通过jedis代码连接3个节点，实现一致性hash的计算。

##### 1.2 三个redis不存在高可用

一旦某一个redis由于故障等问题，导致数据不可用，就会影响easymall系统的使用 ，说明当前结构不支持高可用的。

![](https://note.youdao.com/yws/api/personal/file/0C5CAF2E187941399622D10647F95320?method=download&shareKey=7a1d402d0950c8550a2ab7b33e49ce78)

发现用户的cookie存储在6381，将6381模拟宕机故障，导致系统无法继续使用6381中的数据，登录效果消失了。

##### 1.3 解决思路

![](https://note.youdao.com/yws/api/personal/file/0B03986EF6CA4D02A4CF8A74BF9B6C6D?method=download&shareKey=0fd14c7e52eac46fd48ac91d5e29b36b)

引出2个概念

1. 主从数据备份

   实现主从结构，正在提供功能的是主节点，引入一个从节点连接，同时复制备份数据，否则顶替主节点宕机实现访问功能时从节点不具备数据--所有与数据层相关的分布式集群技术中，只要提到高可用，主从数据备份是基础。

2. 故障转移

   ![](https://note.youdao.com/yws/api/personal/file/6FA540021D574E3A973301324164E3EA?method=download&shareKey=f73c21a4e5d9bd60b09067e42ea8d975)
   
   当客户端系统连接主节点实现数据读写时，由于主节点宕机，客户端应该可以立即连接从节点继续处理数据的读写。--故障转移

#### 2.高可用的结构搭建

##### 2.1 redis主从复制

redis支持主从关系（MySQL也支持），而且支持一主多从，多级主从的结构

![](https://note.youdao.com/yws/api/personal/file/6D07C17E60D74FAABDEAD347D5E4DB1E?method=download&shareKey=f536474bbe0bf6c31b558fe71b17f76d)

根据应用经验，==一般使用一级主从，每个主节点最多挂接6个从节点。==如果更复杂，会导致数据同步的故障，不稳定。

##### 2.2 实现主从配置

一主二从的结构，6382是主节点，6383,6384是从节点。

![](https://note.youdao.com/yws/api/personal/file/AA09CB838440459B853B51578BCACF2B?method=download&shareKey=f99effd609a039b4466c34c75f82a2b4)

1. 准备好3个启动的redis节点 6382 6383 6384
   
   1. 模板配置文件redis.conf拷贝3分
   
      ```
      [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis6382.conf
      [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis6383.conf
      [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis6384.conf
      ```
   
   2. 挨个修改对应端口号
   
      替换端口号的vim编辑器命令
   
      ```
      :%s/6379/6382
      :%s/6379/6383
      :%s/6379/6384
      ```
   
   3. 启动这3个新的redis节点
   
      ```
      [root@10-9-104-184 redis-3.2.11]# redis-server redis6382.conf 
      [root@10-9-104-184 redis-3.2.11]# redis-server redis6383.conf 
      [root@10-9-104-184 redis-3.2.11]# redis-server redis6384.conf
      ```
   
2. 查看一下每个进程服务端角色信息

   127.0.0.1:6384> info replication；

   查看到当前节点的主从复制信息。

   三个节点通过加载配置文件启动，默认角色为master，但是没有任何slave挂接。

3. 执行以下挂接

   在登录的从节点客户端中，执行挂接到主节点的命令。

   127.0.0.1:6383> slaveof 10.9.104.184 6382

   执行完毕挂机，info replication会看到该客户端已经从主节点角色变成从节点，同时6382多出来一个从节点的信息。

4. 测试主从结构

   1. 数据是否同步

   2. 从节点是否可以写入数据

      127.0.0.1:6384> set location beijing

      (error) READONLY You can't write against a read only slave.

      ==默认情况，slave节点是只读模式==，防止连接错误，产生错误数据。一般情况下，不会开发可写的模式，都是只读。从使用结构上，slave只能读数据。

   3. 主节点宕机，观察从节点是否角色转化

      将主节点6382宕机之后，没有在从节点中发现角色的变化

主从复制的结构的结论：

支持高可用结构的基础，仅仅能够实现数据备份，不能完成故障转移，从节点在主节点故障后顶替没有实现。

解决故障转移思路：

1.可以在系统代码端通过catch捕捉异常，将故障的节点去掉，将从节点转化为主节点。

2.可以利用redis提供的高可用进程--哨兵来完成。监听的逻辑。

#### 3.哨兵高可用

##### 3.1 哨兵进程

redis专门为主从结构高可用设计的特殊的服务端进程，不负责数据的处理，独立的进程。只负责监听主从结构---哨兵进程。

##### 3.2 哨兵监听主从

###### 3.2.1 结构

![](https://note.youdao.com/yws/api/personal/file/78F3A16BB8C84840A6A7099FF791EED6?method=download&shareKey=b41f0ef5b401a491903622eb3fc400ac)

###### 3.2.2 哨兵运行逻辑

1. 连接主节点，通过主节点info命令，获取主从所有节点的状态信息。
2. 启动监听所有节点的逻辑。通过心跳检测（rpc协议）机制，每秒钟都会发送心跳的检测命令到达所有主从节点，一旦发现超时未响应，判断宕机。
3. 启动对事件投票机制，过半（少数服从多数）投票对一个事件判断是否执行后续逻辑。选举新的主节点。3个哨兵至少2个投票相同，4个哨兵至少3个投票相同---过半。防止少数哨兵由于网络波动造成错误判断。一般不会配置一个哨兵进程作为监听逻辑存在。
4. 最终选举结束，会将原有的主从结构重新进行发送命令的搭建，将宕机的主节点，重新挂接到新的主节点下，将另外的从节点也挂接到新的主节点下，实现高可用故障转移操作。

##### 3.3 搭建配置哨兵集群

1. 启动哨兵进程需要配置文件，修改模板哨兵配置文件。（26379 26380 26381）

   在redis的根目录有一个模板文件 sentinel.conf，vim编辑器打开这个模板修改参数值。

   1. 17行：关闭保护模式

      ![](https://note.youdao.com/yws/api/personal/file/DA800EE124C144479038FD5FF10EFC65?method=download&shareKey=92dcbe1373be255f35bba0cdd10b7718)

   2. 21行：端口号

      ![](https://note.youdao.com/yws/api/personal/file/12385EEBF1D14CF5AAB33DF09660A020?method=download&shareKey=2242136362e139503d899f66f4d50a5f)

   3. 69行：监听主节点配置

      ![](https://note.youdao.com/yws/api/personal/file/D525B487C6A547F58345D093E969E252?method=download&shareKey=d7776db15a05dbd650bc4cab87076f3b)

      sentinel monitor：固定key值，表示哨兵监听的主节点内容

      mymaster:自定义的字符串，表示当前哨兵监听的主从结构的名称代号

      ip:主节点ip地址

      port：主节点端口

      2：主观下限票数。投票数量最少个数。由这个值来限制定义什么时候哨兵失效。

   4. 拷贝3个哨兵启动的配置文件

      ```
      [root@10-9-104-184 redis-3.2.11]# cp sentinel.conf sentinel01.conf 
      [root@10-9-104-184 redis-3.2.11]# cp sentinel.conf sentinel02.conf 
      [root@10-9-104-184 redis-3.2.11]# cp sentinel.conf sentinel03.conf
      ```

   5. 修改02 03中端口号

      26379  26380 26381

   6. 挨个启动哨兵进程

      在确定主从结构redis启动时正常的前提下，在去启动哨兵。

      多开几个xshell窗口，没有配置后台运行，观察运行时的日志文件输出内容。

      启动哨兵使用哨兵进程的启动脚本

      redis-sentinel 配置文件

2. 测试哨兵主从集群

   1. 将主节点宕机处理

   2. 1. 哨兵集群，由leader发起心跳检测到宕机，发起投票，三个节点过半选举新的主节点
      2. 从原有从节点选择6383作为新的主节点
      3. 将6382 6384重新挂接
      4. 发现6382作为从节点 宕机状态。

   3. 宕机的主节点恢复回来

      宕机的主节点恢复后，哨兵记录中已经将该主节点记录为从节点角色，哨兵登录到恢复主节点执行挂接命令，将其转化为从节点

##### 3.4 哨兵集群的代码测试

jedis如何操作哨兵集群，实现最终代码端高可用。

代码端要实现高可用：在技术端主节点正常情况下，代码客户端连接主节点操作读写，一旦主节点宕机，哨兵需要一段时间选举新主节点，选举完成后，代码端可以重新连接新的主节点，才能成为代码的高可用。

jedis连接读写的主节点，必须通过哨兵集群才能实现，不能直接连接。因为哨兵管理着所有主从的相关信息。

###### 3.4.1 测试代码

```
	@Test
	    public void test(){
	        //jedis连接时，最终要想实现读写得连接上主节点，但是必须通过
	        //哨兵获取主节点信息，就可以实现故障转移之后，访问哨兵拿到
	        //最新的主节点信息
	        //提供哨兵的所有节点连接信息 26379 26380 26381，从中选择一个可连接的节点使用
	        //只要技术端哨兵集群有效，就可以完成信息发送和获取
	        Set<String> sentinelNodes=new HashSet<>();
	        sentinelNodes.add(new HostAndPort("10.9.104.184",26379).toString());
	        sentinelNodes.add(new HostAndPort("10.9.104.184",26380).toString());
	        sentinelNodes.add(new HostAndPort("10.9.104.184",26381).toString());
	        //创建哨兵的连接池对象
	        JedisSentinelPool pool=new JedisSentinelPool("mymaster",sentinelNodes);//指定哨兵集群节点，传递主从代码
	        //从连接池中可以获取很多集群信息
	        HostAndPort chm = pool.getCurrentHostMaster();
	        System.out.println(chm);//通过哨兵，获取当前现役master信息
	        //通过哨兵获取master，就可以创建jedisPool对象操作主节点
	        //JedisPool jPool=new JedisPool(new JedisPoolConfig(),chm.getHost(),chm.getPort());
	        //操作主节点
	        Jedis jedis = pool.getResource();//返回值就是当前现役master连接对象。
	        jedis.set("name","王老师");
	        System.out.println(jedis.get("name"));
    }
```

###### 3.4.2 哨兵结论

很难实现分布式的哨兵集群的客户端代码逻辑。仅仅实现了方便快捷的主从高可用结构，但是只能维护一个分片的高可用，不能实现分布式。

 ![](https://note.youdao.com/yws/api/personal/file/B2973599763F4BC6847EAC8E87B68FDC?method=download&shareKey=55b8f9baa560a9a9dfc37cc3abe208dc)

是否能够存在客户端java代码支持这样的分布式结构？？

1. 创建分片连接池

   1. 收集所有分片哨兵集群
   2. 从不同哨兵中获取不同的主从结构信息，拿到主节点
   3. 通过多个分片中主节点 创建分片连接池，一致性hash实现分布式

2. 没办法高可用

   1. 在故障转移之前，获取的3个主节点

      10.9.9.9:6381

      10.9.9.8:6381

      10.9.9.7:6381

      封装分片连接池，一致性hash 存在这三个节点的映射整数

      数据分片存储在这3个节点。

   2. 一旦其中某一个节点出现故障，10.9.9.9:6381 被替换成了10.9.9.9:6382

      10.9.9.9:6382

      10.9.9.8:6381

      10.9.9.7:6381

      6382作为曾经的6381的从节点确实有数据，但是数据在新的一致性hash结构中还会找到10.9.9.9:6382？？？？？

   3. 解决思路：

      把哨兵管理的主从看成一个整体数据，不能继续使用节点ip:port作为计算一致性hash的节点信息。重新封装客户端代码的一致性hash计算逻辑。


需要一种新的结构，既能实现分布式，又能实现高可用---redis-cluster

## 十一、redis-cluster

#### 1.概述

##### 1.1 redis-cluster的结构

之前的redis结构

1. 三个节点，形成的分布式。
2. 高可用的哨兵集群的结构，
3. 理论上也可以利用哨兵集群搭建分布式。

共有特点

1. 这些集群中，节点与节点之间没有数据的通行过程。
2. 数据对应某一个节点中，是强耦合的（一致性hash）。带来的问题是，hash的散列一定存在数据倾斜（将倾斜利用计算方法变小）。不能随便将数据迁移。

##### 1.2 redis-cluster的结构特点

![](https://note.youdao.com/yws/api/personal/file/D089018AE46343D08EE8422FC56DA673?method=download&shareKey=4b04ded1041e9e6e8e18fed07f0e0723)

1. 集群中所有的节点是两两互联的（不区分角色）。他是redis-cluster结构的基础，底层可以通过二进制优化传输速度，形成通信关系。
2. 哨兵进程消失了，但是哨兵的高可用监听逻辑没有消失，由于两两互联，哨兵逻辑整合到了redis-cluster主节点中，由集群中所有主节点实现对集群的监听。最少要求一个集群里有3个master(实现监听最小结构，可以允许一个master宕机)。 
3. 集群的分布式由内部的计算逻辑维护的 hash slot。客户端分布式计算也要遵循这个hash槽的逻辑。redis-cli 只需要连接一个redis-cluster的节点，就可以将所有数据发送过去，实现内部的重定向跳转 。
4. 分布式计算逻辑的hash槽。引入了16384个槽道的概念，这些操作分片下标号0-16383。所有的key-value数据，到集群中任意一节点上，都会计算槽道，key值和槽道是强耦合的hash散列计算，任何一个key计算hash散列都会得到[0,16383]区间的整数。底层就是hash取余（大范围的正整数%16383）--hash取模计算。master负责维护数据读写，每一个master都可以设置管理的槽道数量。由节点与槽道的这种松耦合关系形成 key-->slot-->node,所以 key和节点对应关系是松耦合，可以进行任意的调试。 

#### 2.槽道的分布式概念

##### 2.1 槽道的使用逻辑

![](https://note.youdao.com/yws/api/personal/file/DF21834F6F604E45A80BF157FAFF2E7D?method=download&shareKey=15b3d549e2dea46cdbea89ab3fdcbad9)

##### 2.2 数据和节点松耦合

key值经过hash的取模计算得到固定的槽道号，key不变，槽道号不变，slot与节点管理关系可以进行迁移变换的，key经过slot的计算，可以对应不同的节点。

![](https://note.youdao.com/yws/api/personal/file/B02B5FD5B3E3419CA103E23B2CBA6A71?method=download&shareKey=a8a2624472d7bc905ea85b2fd427fb76)

##### 2.3 理解槽道原理的两个问题

1. 节点计算槽道号，只要调用方法就行，但是得到槽道号后如何判断所属权（是否在当前接受命令处理数据的节点槽道管理范围之内）？
2. 当节点可以计算槽道号判断所属权之后，一旦发现不归自己管时，如何知道正确管理节点？

##### 2.4 槽道原理

###### 2.4.1判断所属权

1. 在每个节点加入到集群后，节点中由于集群的状态变化，对于节点都会维护一个16384位的二进制--本质上是一个byte数组==byte[2048]==数组，一个元素8位，2048获得了16384位二进制。在每一个节点中都会在内存中保存这个二进制。

2. 对二进制进行计算可以得到0/1的值，同时人为定义，对二进制做位移等计算可以得到从0到16383二进制的下标号，从而对应槽道号。换句话说，任何一个槽道号都能从这个二进制中拿到一个二进制下表对应值不是0就是1。

   ![](https://note.youdao.com/yws/api/personal/file/FFFA538A7D4D4BDFB928D68BB123DBA4?method=download&shareKey=141acb85cffec526dcff87af9016ddbb)

   当某一个节点在内存中有二进制值的时候，可以利用对二进制下标的定义对应到槽道号。可以对key值在该节点做槽道计算之后，通过槽道号，找到二进制值。判断所属权。当二进制是1时，所属权是true 该槽道归当前节点管理，当二进制是0时，所属权是false 该槽道不归当前节点管理。

3. 当前集群中二进制的特点  

   如果说使用二进制判断槽道所属权。

   当集群结构是：

   8000master->0-5460

   ![](https://note.youdao.com/yws/api/personal/file/C73371AFF50146778F2197E7E4FDBB67?method=download&shareKey=b8985703a06cfa65a1f4938e9d6a07e6)

   8001master->5461-10922

   ​	0-5460的二进制都是0，5461-10922都是1，剩下也都是0

   8002master->10923-16384

   ​	0-10922二进制都是0，10923-16383都是1

   在一个正常集群中，每一个槽道对应的二进制值，只能有一个节点中的是1，其他都是0

   管理槽道原理的数据，从节点具备主节点的二进制值（备份）。在计算时，不使用这个二进制。

4. 为什么槽道号是16384，怎么不是65536等个数？ 

   **因为两两互联，节点与节点之间的数据传递的协议中，定义了头包含槽道信息大小2kb，大了会导致传递信息速度的下降，小了会导致数据信息携带不够。**

   ![](https://note.youdao.com/yws/api/personal/file/4259CB29EE75478ABF829D1696D3D629?method=download&shareKey=ced93a4be4909073144ea790ce97aaf4)

###### 2.4.2 节点信息的存储

通过对槽道效果演示，知道如果槽道判断管理权失败false，将会知道正确管理节点信息。

1. 节点在创建集群时，相互通信的初始化内容。  

   没有创建集群时，登录节点是可以看到节点信息，这些信息都会保存在当前内存一个节点的对象中。

   ![](https://note.youdao.com/yws/api/personal/file/FC811E41789E4D07983C4CE2548E738A?method=download&shareKey=8529c43c1a7d0e6539f3651605ca0c84)

   ![](https://note.youdao.com/yws/api/personal/file/428D6A4DC9A7457ABBEC37E056D86A62?method=download&shareKey=8e142d10cb5bc5df7662585aeee723bb)

   在没有创建集群的相互连接，通信时，单个节点中就只保存了当前节点的详细信息对象。

   ![](https://note.youdao.com/yws/api/personal/file/15851C8388C24222B382F5AA6281B766?method=download&shareKey=0b2e590a6c1c4caaa880af84ada51517)

   执行创建集群的过程就是相互通信建立过程。

   ![](https://note.youdao.com/yws/api/personal/file/690700473AF3437C8AC1975E5A75E986?method=download&shareKey=1561e51a36a444d90a46d91abc781fe2)

   当建立完集群，信息相互传递两两通信之后，所有节点中保存的本节点数据都会传递到其他节点中，每一个节点都保存了当前集群里所有信息。登录任何一节点，调用cluster nodes时就能看到所有信息。

2. 每个节点具备访问获取其他节点信息的数据 。

   为了解决槽道第二个问题获取槽道正确管理者信息。还需要一个数据保存槽道与节点的对应关系---共享数组

   相互通过二进制底层协议传输数据时，已经具备所有节点对象，通过二进制中携带的值判断，生成对应共享数组。

   ![](https://note.youdao.com/yws/api/personal/file/97C028F86D22410D810C7AD7154C6B13?method=download&shareKey=2520a23c63595ae8351ab084f0469938)

   

   每个节点中，在创建完集群都会生成一个数组对象，数组有16384个元素（下标对应槽道号）。最终生成的数组元素值特点是：下标号对应槽道。该槽道所属的节点是谁，就可以通过对数组元素获取拿到这个正确管理节点的对象信息，所以所有节点都应该保存一个完全一样的数组对象，无论从哪个节点进行数据命令调用，都可以通过数组找对正确管理者。

   ![](https://note.youdao.com/yws/api/personal/file/97C028F86D22410D810C7AD7154C6B13?method=download&shareKey=2520a23c63595ae8351ab084f0469938)

###### 2.4.3 总结槽道原理

1. 所属权：

   槽道管理必须引入所属权逻辑，没有所属权判断，一定不会找到节点存储数据。  

   二进制作为一个16384位的值，可以为0-16383下标号做bit存储 每一位的值是1表示所属，是0表示不所属，通过下标计算刚好对应所有的槽道号。

2. 节点信息获取：  

   在每一个节点中都会保存一个完全一样的数组，数组元素个数16384，下标刚好对应槽道，利用槽道下标，保存该槽道管理节点信息到元素中，每个元素都是一个变量，指向了内存中一个节点对象（只要下标对应元素指向某个节点，表示该下标槽道号对应这个节点处理）

#### 3.搭建redis-cluster步骤

##### 3.1 准备redis-cluster配置模板

1. 复制一下redis.conf使用基本配置内容不变  

   ```
   [root@10-9-104-184 redis-3.2.11]# cp redis.conf redis-cluster.conf
   ```

2. 使用新的模板文件redis-cluster.conf修改集群需要的配置  

   1. 721行：打开Redis的集群模式 

      开启cluster 就是将redis-server进程中添加槽道的计算逻辑。没有槽道无法实现处理数据。

      ![](https://note.youdao.com/yws/api/personal/file/B4B0FCB3CA0249B68590B022D3BBD7C8?method=download&shareKey=2043c075838bc5fb39ded9e1ec5b6d65)

   2. 729行：打开配置文件nodes 

      集群节点有很多，nodes文件记录了当前集群所有节点状态。

      ![](https://note.youdao.com/yws/api/personal/file/C9410D1FCD0449A0AF9224F25A5D5979?method=download&shareKey=70e38e8e1a0fe7026cf1c5d9e30f37c2)

##### 3.2 创建集群节点的文件夹

和前面一样，复制配置文件，修改端口号，就能启动多个redis-cluster进程。后面会提供一个脚本文件--重启集群。按照脚本中的配置文件夹结构，创建管理这些配置文件。搭建集群 8000 8001  8002 8003 8004 8005

创建一批管理配置文件的文件夹。文件夹以端口号命名，就在redis根目录做

1. 创建多个文件夹  

   ```
   [root@10-9-104-184 redis-3.2.11]# mkdir 8000 8001 8002 8003 8004 8005
   ```

2. 将配置文件拷贝到6个文件夹中  

   ```
   [root@10-9-104-184 redis-3.2.11]# cp redis-cluster.conf 8000
   [root@10-9-104-184 redis-3.2.11]# cp redis-cluster.conf 8001
   [root@10-9-104-184 redis-3.2.11]# cp redis-cluster.conf 8002
   [root@10-9-104-184 redis-3.2.11]# cp redis-cluster.conf 8003
   [root@10-9-104-184 redis-3.2.11]# cp redis-cluster.conf 8004
   [root@10-9-104-184 redis-3.2.11]# cp redis-cluster.conf 8005
   ```

##### 3.3 将默认的配置文件端口替换

分别打开配置文件

```
[root@10-9-104-184 redis-3.2.11]# vim 8000/redis-cluster.conf
…后续打开8001 8002 8003 8004 8005
```

利用vim编辑器命令将6379替换成8000

```
:%s/6379/8000/
```

##### 3.4 启动所有节点

1. 调用redis-server命令启动所有的redis集群进程 

   ```
   [root@10-9-104-184 redis-3.2.11]# redis-server 8000/redis-cluster.conf 
   [root@10-9-104-184 redis-3.2.11]# redis-server 8001/redis-cluster.conf 
   [root@10-9-104-184 redis-3.2.11]# redis-server 8002/redis-cluster.conf 
   [root@10-9-104-184 redis-3.2.11]# redis-server 8003/redis-cluster.conf 
   [root@10-9-104-184 redis-3.2.11]# redis-server 8004/redis-cluster.conf 
   [root@10-9-104-184 redis-3.2.11]# redis-server 8005/redis-cluster.conf 
   ```

2. ps查看启动进程 
   确定是否8000-8005全部启动

   确定是否全部启动在cluster模式下

   ```
   [root@10-9-104-184 redis-3.2.11]# ps -ef|grep redis
   ```

   6个进程，末尾以[cluster]表示集群模式。

   ```
   root     11428     1  0 11:22 ?        00:00:00 redis-server *:8000 [cluster]       
   root     12536     1  0 11:22 ?        00:00:00 redis-server *:8001 [cluster]       
   root     12540     1  0 11:22 ?        00:00:00 redis-server *:8002 [cluster]       
   root     12544     1  0 11:22 ?        00:00:00 redis-server *:8003 [cluster]       
   root     12548     1  0 11:22 ?        00:00:00 redis-server *:8004 [cluster]       
   root     12554     1  0 11:22 ?        00:00:00 redis-server *:8005 [cluster]
   ```

   哪个没有启动成功，说明最大的可能问题，就是端口号修改不正确。检查一下对应端口号的配置文件。

##### 3.5 集群的命令

1. 登录集群客户端访问redis-cluster服务端节点  

   redis-cli 使用选项多一个-c表示以cluster模式调用服务端进程，支持cluster一些特性。

   ```
   [root@10-9-104-184 redis-3.2.11]# redis-cli -c -p 8000 -h 10.9.104.184
   ```

2. cluster的2个命令  

   1. 8000>cluster  info 

      命令调用会返回集群运行的各种状态信息，其中包含了slot状态

      cluster_state:fail  运行状态 fail

      cluster_slots_assigned:0 槽道分配个数0

      cluster_slots_ok:0

      cluster_slots_pfail:0

      cluster_slots_fail:0

      cluster_known_nodes:1 集群节点个数

      cluster_size:0 集群大小（体现在槽道被分成了积分，数据分片个数）

   2. 8000>cluster  nodes 

      查看当前集群，所有节点信息，由于两两互联，在集群搭建完成后，通过任何一个节点，都可以获取集群的所有节点信息。

      当前状态是，8000/8001/8002/8003/8004/8005都只能看到本节点信息。

      10.9.104.184:8000> cluster  nodes

      f8bea038f94f5a6dc316a67c2e545bd9693c01c6  :8000 myself,master - 0 0 0 connected

##### 3.6 创建连接集群节点

启动完了6个节点之后，集群中所有节点还没有添加到一起（让之间两两互联，分配槽道管理范围）。集群不能使用的。

redis的安装环境中，提供了一个ruby语言编写的脚本文件，专门用来操作集群，执行集群搭建，转移，数据迁移，槽道分片等功能的客户端脚本。这个脚本在redis根目录的src下。叫做redis-trib.rb

1. 利用ruby脚本创建集群

   命令执行结构

   redis-trib.rb 子命令  各种选项和参数

   ![](https://note.youdao.com/yws/api/personal/file/C2F09C03FFF5451B9B045690440A183A?method=download&shareKey=cad0bde9ae52708542df678180e2a5ec)

   create作为ruby语言脚本的子命令，就是用来创建连接集群的，可以直接后面跟着所有想加入集群的节点，并且可以利用选项实现搭建初始化过程就完成从节点的分配。首先使用create命令将8000 8001 8002作为三个主节点，创建一个最小结构的集群。

   ```
   [root@10-9-104-184 redis-3.2.11]# src/redis-trib.rb create 10.9.104.184:8000 10.9.104.184:8001 10.9.104.184:8002
   ```

2. 执行完命令，以上配置  

   ```
   Can I set the above configuration? (type 'yes' to accept):yes
   ```

3. 看到提示  

   ![](https://note.youdao.com/yws/api/personal/file/E06DDB62C27941DB9917CAFB4D0D80EC?method=download&shareKey=34e419b2ac61af13213ef5a36d2b33c7)

4. 登录到任意一个节点，执行set/get命令 

   ```
   [root@10-9-104-184 redis-3.2.11]# redis-cli -c -p 8000
   127.0.0.1:8000> set name wanglaoshi
   -> Redirected to slot [5798] located at 10.9.104.184:8001
   OK
   10.9.104.184:8001>
   ```

##### 3.7 添加节点（主节点）

1. 动态添加  

   redis-cluster支持动态添加节点，已有的集群结构不用发生任何改变，直接在运行的集群中调用命令就可以做到动态扩展集群（节点变得越来越多，对已有节点不影响）

   刚好8003 8004 8005还没有使用。将8003动态添加到当前集群。

   ![](https://note.youdao.com/yws/api/personal/file/AD3D6245B95D49C39EE0FE6ADE0C68DD?method=download&shareKey=74a486d5d63a673fd2f9e7fad494c865)

   调用ruby脚本中的add-node自命令。选项--slave --master-id与添加从节点有关。

   提示参数：新节点ip:端口(8003) 已存在节点ip:port(8000|8001|8002)

   ```
   [root@10-9-104-184 redis-3.2.11]# src/redis-trib.rb add-node 10.9.104.184:8003 10.9.104.184:8000
   ```

   ![](https://note.youdao.com/yws/api/personal/file/3D91921921A04E7B9E24FC1431DB1A83?method=download&shareKey=7aef9934611ce9a3400c1151d77f9bdf)

2. 添加节点后的节点状态  

   可以登录8003调用命令cluster nodes命令查看集群节点状态信息。

   1. 0730f6c324fa3400aeb24e6da23a54368acf110f   节点id 唯一标识
   2. 10.9.104.184:8001  节点host和端口号
   3. master  节点角色，当前登录人slave myself  master  myself
   4. -表示这个节点的主节点id -表示没有上层主节点
   5. 0 
      1581920977396  时间如果正在登录客户端显示0
   6. 2  序号 集群节点会从1开始2,3,4,5分配序号
   7. connected  连接状态disconnected
   8. 5461-10922  槽道管理范围

   默认添加进来的节点都是主节点，由于槽道已经分配完毕。肯定没有新槽道交给他去维护的。新的节点即使是主节点，不会存储处理数据。

   但是作为主节点可以实现投票，已经是集群中可以投票选举的一员了。

##### 3.8 动态添加从节点

1. 添加从节点  

   add-node调用这个子命令，参数  新节点 已存在节点不变，只要在其中使用2个选项

   --slave --master-id就可以。--master-id表示新节点要添加到的主节点的id值。

   ​	--slave  表示节点以从节点身份添加到集群

   ​	--master-id  <arg> 提供从节点挂接的主节点id值

   例如，使用8004作为新节点添加到集群成为8001的从节点。

   ```
   [root@10-9-104-184 redis-3.2.11]# src/redis-trib.rb add-node --slave --master-id 0730f6c324fa3400aeb24e6da23a54368acf110f 10.9.104.184:8004 10.9.104.184:8002
   ```

   通过登录查看节点状态。就可以看到不一样的一个nodes信息。

   ```
   127.0.0.1:8000> cluster nodes
   ```

   ![](https://note.youdao.com/yws/api/personal/file/2D0BFA1C84BF4D88910067CE850D84C2?method=download&shareKey=3c3e26b416a90fffdbcfed1142d87615)

2. 创建时就使用命令把主从做完  

   可以通过redis-trib.rb的脚本命令，在create执行时，就指定当前集群主从结构。但是没办法实现一对一的指定。

   create 命令下有一个选项  --replicas <arg>  选项表示要将现在创建集群时，提供的所有节点，以主从形式创建完成，<arg>对应一个整数值，例如：1，表示集群在保证完整搭建正常运行结构下（最小三个主），会为每一个主节点都分配至少1个从节点。所以，你提供的节点个数满足这个计算。例如：--replicas 1  至少给6个节点实现三主各有一丛，--replicas 2至少给9个节点三主各自2从。

3. 高可用测试  

   验证当前看不到哨兵进程时，是否有故障转移。将主节点8001干掉，等待一段时间进行选举。实现主从替换。8001恢复回来就老老实实当从节点。

   ```
   [root@10-9-104-184 ~]# redis-cli -c -p 8001 shutdown
   ```

   ![](https://note.youdao.com/yws/api/personal/file/05CEA85D76A345079B9B0C6D2D225670?method=download&shareKey=d25f916683db82dc01af6328f9508b9f)

   当选举结束之后，新的主节点出现了。8004顶替为新的主节点，数据和槽道一并交给8004维护

   ![](https://note.youdao.com/yws/api/personal/file/6FB7199895DC46D091CDB3FA375E8314?method=download&shareKey=b607cac0d9c48ef81d20f19b720dd221)

   将8001恢复，就会变成从节点挂接在8004.

   ![](https://note.youdao.com/yws/api/personal/file/8BC13046C6614AA2BFC2874245EF5A27?method=download&shareKey=5e1dcab14483876a4d45b65b558fcdae)

##### 3.9 删除节点

del-node可以实现对集群中某个节点的删除工作。只能在集群中删除没有槽道管理权的主节点，和删除从节点。特点不处理槽道逻辑，因为槽道和数据是绑定的，一旦删除了有槽道管理范围的节点，集群有一批槽道不可用，集群down状态。

![](https://note.youdao.com/yws/api/personal/file/1FE23FAE1557466F992FC6834F73FA4B?method=download&shareKey=59b2c2cad58212c12e06c3a46e23cf81)

host:port 表示一个已存在节点。

node_id:表示删除的节点的id。

目前结构可以删除8001 8003其中一个。

```
[root@10-9-104-184 redis-3.2.11]# src/redis-trib.rb del-node 10.9.104.184:8003 0730f6c324fa3400aeb24e6da23a54368acf110f
```

![](https://note.youdao.com/yws/api/personal/file/0AE6A24179764C3EB1D603B81F0A305C?method=download&shareKey=4e8d16613749f411db6a6690d5ba65bf)

##### 3.10 重新分配槽道（初始化创建集群）

已经创建集群，由脚本语言命令ruby将槽道分配完毕了，所以当有新的主节点加入时。

没有槽道的，一旦想对槽道进行重新分片，可以调用自命令，控制操作空槽道（没有key对应的槽道）进行迁移操作。reshard 重新分片

![](https://note.youdao.com/yws/api/personal/file/F0B4194670444F6A9FB2EC3747083B35?method=download&shareKey=e851170d9c03d18f8b582bd2d90937d6)

host:port 对哪个集群实现重新分配槽道的操作，就提供哪个集群中已存在节点。

1. 调用重新分片命令  

   8003作为主节点没有槽道管理，新定义一批槽道 200个（确定没有绑定任何key值）

   [root@10-9-104-184  redis-3.2.11]# src/redis-trib.rb reshard 10.9.104.194:8004

2. How  many slots do you want to move (from 1 to 16384)? 

   要从1到16384个槽道数量迁移几个槽道200个

   ![](https://note.youdao.com/yws/api/personal/file/60AA72053A9343BCA44C8A2069ED432E?method=download&shareKey=1f92387dcea4b1fda4d9a7bcbb896baa)

3. What  is the receiving node ID? 

   迁移槽道的目标节点id（槽道迁到哪8003）

   ![](https://note.youdao.com/yws/api/personal/file/4B35CFFB52E948C8B459B62184AEDB86?method=download&shareKey=5895ea94f1ec0309de3d9df741700319)

4. 确定槽道来源  

   当前集群中已有的16384个槽道，全部都已经分配完毕，在迁移的时候一定从一个源节点迁移到目标节点（8003）。源节点可以是1个，也可以是多个，甚至是全部其他拥有槽道的节点。

   ![](https://note.youdao.com/yws/api/personal/file/C0D29AC713F54440A6E92EDEBBF72B96?method=download&shareKey=cff5df85ab49211623b0209b39108f9b)

   1. 第一种方式：自定义从哪些节点作为槽道来运，将节点id依次填写到#1  #2序号后  

      Source  node #1:d3ec4c56a741b1f60833f685e53cccb4cee5b7de

      Source  node #2:done

   2. 第二种：直接输入一个all,将其他所有拥有槽道的节点都作为源节点将平均分配槽道数量  

5. 确定迁移的槽道输入yes  

   Do  you want to proceed with the proposed reshard plan (yes/no)?

   同意yes 不同意no

6. 登录节点，观察集群槽道变化  

   8003多出了200个槽道

#### 4.脚本文件使用

##### 4.1 编辑一个shell脚本包含了停删启动创建所有过程

shell脚本包含了所有重新搭建集群的过程  停删启建，只要保证环境配置文件能够正常启动一个8000-8005的节点环境就可以使用脚本（注意文件夹结构）

##### 4.2 使用脚本注意

我测试案例中使用的讲师云主机ip，需要将文件替换成自己的云主ip地址

##### 4.3 在云主机使用

可以通过上传文件命令，在windows编辑好脚本改完ip通过rz命令上传

![](https://note.youdao.com/yws/api/personal/file/D18605B6BBD642DFB5060E424E323403?method=download&shareKey=2f6da661bba50ff15a06847afc126595)

一定可以搭建三主各自有一丛结构。

#### 5.常见问题

##### 5.1 提示创建添加节点失败

###### 5.1.1 问题提示

10.42.47.192:8000  is not empty. Either the  node already knows other nodes (check with CLUSTER NODES) or contains some key  in database 0.

创建集群时，向集群添加节点，发现节点非空。要不然当前集群已经知道该节点，要不然就是该节点中的redis内存中有已存在的数据

###### 5.1.2 原因

1. 节点非空，可能是读取了其他文件的dump持久化文件，导致新节点启动时就存在了内存数据   
2. 集群已知，该节点已经加入过这个集群。  

###### 5.1.3 解决思路

1. 登录到该节点，执行keys  * 检查节点中是否有数据  

   如果没有数据，说明是第二个原因。

   如果有数据，就将数据清空操作。调用命令flushall。冲刷掉所有数据，内存数据和持久化文件数据一并都消失了。

2. 不能再已经创建的集群节点上再去调用redis-trib.rb  create的命令。  

   删除集群对该节点的记录，就将对应节点的nodes文件

   关闭对应进程，删除nodes-8000.conf文件。重新启动重新执行命令。

##### 5.2 集群操作各种意外解决

操作集群的各种步骤，各种检测功能，如果由于误操作，网络意外导致集群不可恢复，在测试环境中，可以执行以下几步，重新恢复集群的状态。停-删-启-建

###### 5.2.1 停

停止进程，将集群所有进程停掉

redis-cli  -c -p 8000 shutdown

8001  …

###### 5.2.2 删

删除掉当前集群中所有节点对应持久化文件

dump* 每个节点对应持久化文件。不能保证这些文件中，在重新创建集群之前是空，就把他删掉

nodes* 对应节点集群状态持久化文件。每个文件肯定都保存了已经创建好的集群节点信息。

\#rm -f dump*

\#rm -f nodes* 

###### 5.2.3 启

启动所有集群节点。当停和删做完了，当前准备所有节点环境就和最初创建的环境是一致的，可以启动所有节点等待重新搭建

\#redis-server  8000/conf

\#redis-server  8001/conf

…

###### 5.2.4 建

重新对已经启动的多个节点按照想要的结构创建出集群。

src/redis-trib.rb  create node1 node2 node3 node4 node5 node6..

正常通过停删启建四步重新将一个完整的集群创建出来的前提：配置文件得正确  redis-cluster.conf