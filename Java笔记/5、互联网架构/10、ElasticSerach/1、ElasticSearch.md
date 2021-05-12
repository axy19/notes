[TOC]

# EalsticSearch

## 一、Lucene的不便之处

#### 1.没有实现分布式

存储在一个节点，应该引入多台服务器，实现一个索引文件的数据拆分，共同维护索引的数据，保证拆分后的结果形成高可用。

![](https://note.youdao.com/yws/api/personal/file/ECF4A6966249495A99D42AF2AB5CE719?method=download&shareKey=63d85c9b73214ea4bf08982f17292720)

#### 2.对其他语言不友好

Java编写全文检索，可以使用Lucene，使用C语言，Python PHP c#等语言的开发过程。想使用Lucene，第一，将Lucene的逻辑转化其他语言；第二，学习Java开发Lucene。

#### 3.使用Lucene设计一个技术

一旦出现了封装了Lucene的技术，可以启动软件、进程，让各种各样的客户端通过访问通信协议（RPC,REST,HTTP.TCP/IP,数据库协议）

## 二、ElasticSearch

#### 1.介绍

elasticsearch是基于Lucene（软件中使用了Lucene的代码，Java开发）的搜索服务（可以启动web应用）。可以提供大量的客户端使用协议访问软件（HTTP）。

![](https://note.youdao.com/yws/api/personal/file/E157178D056042B099387EE91AB743DC?method=download&shareKey=4c485b4d1a550336fac4be2d18933fd3)

#### 2.rest风格

是HTTP协议创始人在2000年一篇论文上，提出的一种对HTTP协议使用的风格建议。

rest风格设计2点内容：

1.  ==URL地址表示资源==，同类资源可以用路径中不同的数据表示。

   例如：访问一个用户数据

   1. 我们：http://localhost:8080/user/manage/query?username=eeee不满足rest风格，没有体现URL是资源的概念。

   2. rest风格：

      http://localhost:8080/user/manage/query/eee

      http://localhost:8080/user/manage/query/admin

2. ==http请求方式表示操作==

   对同一个资源有多种不同的操作。

   1. 我们

      /user/manage/save

      /user/manage/query

      /user/manage/update

      三个URL地址可能操作同一个用户资源

   2. rest风格

      put请求  /user/manage/{userId} 请求体参数  表示新增
      
      get请求  /user/manage/{userId} 请求体参数  表示查询
      
      delete表示删除
      
      post表示更新

3. springmvc支持rest风格

   ```Java
   //支持REST风格
   @RequestMapping(value="haha/{id}",method = RequestMethod.DELETE)
   public void delete(){ }
   @RequestMapping(value="haha/{id}",method = RequestMethod.PUT)
   public void save(){ }
   @RequestMapping(value="haha/{id}",method = RequestMethod.POST)
   public void update(){ }
   ```

4. 满足rest风格

   1. URL定义资源，必定存在路径参数。
   2. 请求方式定义了操作put新增，post更新，delete删除，get查询。

5. 不一定所有开发都必须满足rest风格

   微服务集群中，很多情况下对同一个资源除了操作增删改查之外，还有很多细小的功能请求。

   一般在这种情况下，还是需要很多自定义的请求URL，自动以业务逻辑，违反了rest风格。

6. elasticsearch

   1. 请求URL的设计，是给客户端使用的。
   2. 如果涉及URL地址很随意，客户端使用elasticsearch很不方便。（20000个URL怎么记）

#### 3.elasticsearch基本结构

1. 存储层：存储生成的索引和索引中的文档数据，倒排索引表数据。

2. 功能扩展：基于Lucene实现了索引文件的管理（创建、搜索）。实现了分布式和搭建集群的各种功能 。

3. 用户协议层：HTTP协议的REST风格，给所有的客户端提供访问功能。 

   ![](https://note.youdao.com/yws/api/personal/file/E93E57D6C1D444DC85BDC9D3E969BF05?method=download&shareKey=247bb99990303ca593a32d19a1512d5b)

#### 4.模拟使用Lucene封装一个web应用

开发一个模拟es的web应用，对外提供两个暴露的接口地址URL。

1. localhost：8080/index01 

   1. put提交新建一个索引文件
   2. get提交返回索引状态
   3. post修改索引
   4. delete删除索引

2. localhost:8080/search？filedName=productName&text=哈哈;

   get提交，实现搜索数据返回。

#### 5.postman的使用

课前资料 00window有一个postman安装软件。测试http请求，封装http请求中各种头，各种参数使用的插件软件。

![](https://note.youdao.com/yws/api/personal/file/580AFF8AFAE14D1AA63F239961D48476?method=download&shareKey=68f5b2ad55a31feafd6fa7af32853eef)

#### 6.火狐插件---RESTCLIENT

![](https://note.youdao.com/yws/api/personal/file/6C0EB5F792A34003838D2AA08D53E900?method=download&shareKey=01777ae85ec5b25ce66f3585f94ded2c)

![](https://note.youdao.com/yws/api/personal/file/C463A4889B3A446B94F9F2BD914C0A6F?method=download&shareKey=f929c82fdc8eb41b8a10de3a931ef646)

![](https://note.youdao.com/yws/api/personal/file/9811068F8FBA4F84BAA4C58BB36D2382?method=download&shareKey=51fe1135276f4f351447d6d17ef32b75)

![](https://note.youdao.com/yws/api/personal/file/2B05C450FF34498E80A288CD1D520286?method=download&shareKey=ade072e6c8b2544869127b2a54090982)

## 三、elasticsearch的安装和使用

#### 1.安装elasticsearch到服务器

最终要使用3台云主机，启动一个es的高可用分布式集群

##### 1.1 解压安装包到software目录下

[root@10-9-104-184 ~]#  tar -xf /home/resources/elasticsearch-5.5.2.tar.gz -C /home/software/

##### 1.2 目录结构

![](https://note.youdao.com/yws/api/personal/file/EE969073243A491DAD941DF86944A18F?method=download&shareKey=ed5a6b9c879ac49be28e1eae0682d135)

1. bin:启动命令所在   
2. config：核心配置文件elasticsearch目录  
3. plugins：ik分词器插件。

##### 1.3 变换用户用户组

==es对安全性有要求，不允许root用户直接启动==（es无法限制root的权限）。得使用一个普通用户，赋权使用。

```
[root@10-9-104-184 software]# chown -R es:es /home/software/elasticsearch-5.5.2/
```

#### 2.elasticsearch配置文件和启动

即使使用es用户，进入bin目录执行elasticsearch命令启动，也会出现各种问题，其中，只允许localhost客户端访问，不允许外界。

##### 2.1 修改elasticsearch.yml

1. 17行集群名称  

   是es启动整合集群时使用的名称标识。

   ![](https://note.youdao.com/yws/api/personal/file/40A15E6977E442E194861F12A30725E7?method=download&shareKey=574a77b9fce7a3b12bd2b5c1e5a47f1f)

2. 23行节点名称  

   每一个集群中的es节点，都应该有一个唯一的名称。如果注释，将会每次启动都会分配一个随机名字。

   ![](https://note.youdao.com/yws/api/personal/file/E6E22708B6C14C9D96207BDE6C726438?method=download&shareKey=199d99e2661b9019cc7d0b53929d7d48)

3. 43行关闭bootstrap的使用  

   centOS6.5没有安装bootstrap相关插件，所以es启动时如果要求开启bootstrap，会报错误，虽然不影响es使用，但是可以在配置文件将其关闭.

   ![](https://note.youdao.com/yws/api/personal/file/9FF8A75811604DA990BFA30121293EC7?method=download&shareKey=c97c19968b6d2bdfb37d1ef224f39cc2)

4. 56行修改host地址 

   默认使用localhost 所以只允许本地请求访问到es节点，修改成服务器的ip地址。

   ![](https://note.youdao.com/yws/api/personal/file/904C9755C0134401A143FE7389C2632B?method=download&shareKey=d1b701da76e75895b9074dacacf04513)

5. 60行打开==http协议访问端口9200==

   还有一个==tcp/ip协议的java语言访问的端口9300==

   ![](https://note.youdao.com/yws/api/personal/file/33FD3DCA3B7B4DF1A4BFBE23D34725E8?method=download&shareKey=317c58b3ec929747a3596bd2fd783875)

   如果搭建集群，3个节点集群，还要在多配制2行内容，

   一个是：协调器

   一个是：最小有效master数量，防止脑裂出现。

##### 2.2 启动elasticsearch

使用普通用户登录，进入到bin文件夹执行启动命令

[es@10-9-104-184 bin]$  elasticsearch

[es@10-9-104-184  bin]$ elasticsearch -d  可以后台运行

## 四、es的操作命令

#### 1.es中的数据管理结构

与数据库结构做对比

| mysql数据库  | elasticsearch全文检索 |
| ------------ | --------------------- |
| database库   | index索引             |
| table表格    | type类型              |
| rows行数据   | document文档          |
| column列数据 | field域属性           |

#### 2. 操作es，但是看不到视图效果

可以对启动es实现命令操作，本质就是http请求，满足REST风格。

1. 在es节点添加一个索引文件。课前资料--10elasticsearch--02其他资料中--es命令txt文件  

   后续会使用这个文件，直接粘贴命令来测试访问es效果。先将文件的ip地址修改成自己的es节点服务器ip

   ![](https://note.youdao.com/yws/api/personal/file/54BBEB4C008942FF80BE54DBCCB481B2?method=download&shareKey=829f58f580182b5677fdf90aaba0aa1a)

2. 每操作一个命令，就是发送一个http请求，直接在linux系统粘贴调用 。

   curl -XPUT <http://10.9.104.184:9200/index01>： 新增一个叫做index01的索引文件到es节点中

   curl：linux提供的一个支持http协议的命令脚本，有很多选项可以携带http协议的头，参数等

   -X 后面跟着这次请求的方式 PUT  DELETE GET POST

   -d 请求体内容

   -h 请求头的内容

3. es中创建索引的结构  

   在linux调用curl命令访问es节点，返回了一个json字符串。

   {

      "acknowledged": true, 操作确认

      "shards_acknowledged": true 分片成功

   }

   index01这个名字的索引空的，就在es创建成功。

   ![](https://note.youdao.com/yws/api/personal/file/DC42B399F4CF413FA5196E9BB3C498FF?method=download&shareKey=7ec1c2b10ca9165ad49934d46f783773)

4. 索引文件名称不是原名index01而是id值，只要知道id就能到该文件夹找数据。  

   先查询index信息，从中获取id，就能找到文件夹对应谁。调用curl命令

   ```
   {
     "index02": {
       "aliases": {
       },
       "mappings": {
       },
       "settings": {
         "index": {
           "creation_date": "1582533919910",
          "number_of_shards": "5",
          "number_of_replicas": "1",
           "uuid": "neShnvvZQyiT2-3BLXmYJA",就是当前索引id值
           "version": {
            "created": "5050299"
           },
          "provided_name": "index02"
         }
       }
     }
   }
   ```

5. document数据一旦新增到某个索引中，到底存储在哪个分片？

   ![](https://note.youdao.com/yws/api/personal/file/61756CC7B75747A4BB2C58AE19B8506E?method=download&shareKey=e1cda1866675c7494a453481818eeec9)

   大量document存储在index01，由于index01被切分成了5个分片，将会利用document的唯一值_docId 计算hash取余（对分片个数取余）一个索引文件被切分成多个分片创建之后，不允许修改分片数量。

6. head插件视图化查询es中数据显示出来  

   前面在es配置文件里，http.cors.enabled: true

   在/home/presoftware/elasticsearch-head-master

   进入这个目录，这是head插件的根目录 修改根目录Gruntfile.js的文件。

   [root@10-9-104-184  elasticsearch-head-master]# vim Gruntfile.js   

   将93行的host改成云主机ip地址。

   启动head插件在head文件夹根目录执行

   \#grunt  server 

   \#grunt server & 后台运行

7. head插件访问 

   html5编写的访问es的插件，通过http请求，连接es，发送各种命令操作es（http请求）

   ![](https://note.youdao.com/yws/api/personal/file/2441409F5D474914BAEE570B081A0DB0?method=download&shareKey=012ad9c69f4ce29c78cf07b533056df7)

   在连接标签值中，填写连接访问es的地址 <http://ip:9200>

   ![](https://note.youdao.com/yws/api/personal/file/627BEB39DC4C46128D75ACE0851A877D?method=download&shareKey=6e301d20e4399f52a9e6bfd25e637579)

   es01:节点名称，elasticsearch.yml配置的  node-name

   星星符号：表示当前es集群的master节点（之前主从复制master意义不同）

   0-4方框：每个索引切分的分片文件夹标号（取余数字）

   unassigned：集群最小结构是2个，如果启动一个，认为另外一个没有启动，并且从分片虽然计算出来了，但是没有第二个节点时并不会分配写数据。

#### 3.操作命令

##### 3.1 索引管理

1. 新增索引  

   \#curl  -XPUT <http://10.9.104.184:9200/{indexName}>

   es根据请求方式put，根据请求url地址中资源定义  indexName为索引名称，将会在es中创建indexName的索引。成功之后返回响应

   {

      "acknowledged": true, //表示确认成功

      "shards_acknowledged": true //分片创建成功

   }

2. 删除索引  

   \#curl  -XDELETE <http://10.9.104.184:9200/{indexName}>

   提供索引名称，使用delete请求，可以将索引数据从es中删除。

   {

     "acknowledged": true

   }

3. 查看索引

   \#curl  -XGET <http://10.9.104.184:9200/{indexName}>

   在es中保存的索引文件，都会对应有各种状态存储对象信息，可以通过get查询将这些信息返回。aliases别名 mapping映射 settings配置

   {

     "index01": {

   ​    "aliases": { //别名，可以通过别名操作索引

   ​    },

   ​    "mappings": { //默认是空，一旦数据新增，mapping将会出现动态结构

   ​    },

   ​    "settings": { //索引的各种配置信息

   ​      "index": {

   ​        "creation_date": "1582594169179",

   ​        "number_of_shards": "5",

   ​        "number_of_replicas": "1",

   ​        "uuid": "sDnRylu6RXu2INuyDXctbA",

   ​        "version": {

   ​          "created": "5050299"

   ​        },

   ​        "provided_name": "index01"

   ​      }

   ​    }

     }

   }

4. 关闭打开索引  

   \#curl  -XPOST <http://10.9.104.184:9200/{indexName}/【下划线】close>

   \#curl  -XPOST <http://10.9.104.184:9200/{indexName}/【下划线】open>

   索引文件在es中创建后，会存在保存状态的持久化文件，其中一个状态叫做打开关闭，一旦将索引文件进行关闭操作，这个索引就不能再使用。直到再被打开。

5. 读写权限限制  

   可以对索引实现不能读的限制操作

   \#curl -XPUT -d '{"blocks.read":true}' <http://10.9.104.184:9200/{indexName}/【下划线】settings>

   blocks.read: true 表示阻止读

   blocks.read: false 表示不阻止读

##### 3.2 文档管理

http协议访问es时，新增文档数据，一定保证json字符串结构正确。需要将文档结构按照json整理。例如：

document：  id,title,content

{"id":1,"title":"哈哈","content":"真不错"}

1. 新增文档（覆盖文档）

   \#curl -XPUT -d '{"id":1,"title":"es简介","content":"es好用好用真好用"}' <http://10.9.104.184:9200/{indexName}/{typeName}/{_docId}>

   在已存在的索引中，通过url指定索引类型 和文档id，将文档数据作为请求体参数发送到es处理。

   {document对象的基础属性

     "_index": "book", 所属索引

     "_type": "article",  所属类型

     "_id": "1",  document的id值

     "_version": 1,  版本号，作为判断document，判断数据是否最新的一个数字，随着对document的操作执行，自增处理。在分布式高可用的集群中，判断哪些数据可用，哪些延迟。

     "result": "created",

     "_shards": {

   ​    "total": 2,

   ​    "successful": 1,

   ​    "failed": 0

     },

     "created": true

   }

2. 获取文档

   \# curl -XGET <http://10.9.104.184:9200/{indexName}/{typeName}/1>

   使用get请求，对索引，类型，document的id值。携带存储的json字符串数据。

   {

     "_index": "book",

     "_type": "article",

     "_id": "1",

     "_version": 3,

     "found": true,

     "_source":  {  //source表示数据内容

   ​    "id": "doc1",

   ​    "title":  "java编程思想",

   ​     "content": "这是一本开发的圣经"

     }

   }

3. 删除文档

   curl  -XDELETE <http://10.9.104.184:9200/book/article/1>

   指定索引名称类型、类型名称和doc的id就可以删除，即使删除version也自增了1

   {

     "found": true,

     "_index": "book",

     "_type": "article",

     "_id": "1",

     "_version": 4,

     "result": "deleted",

     "_shards": {

   ​    "total": 2,

   ​    "successful": 1,

   ​    "failed": 0

     }

   }

##### 3.3 搜索功能

es基于lucene它也支持大量搜索功能例如：词项、布尔、多域、模糊等等。使用http协议访问es进行搜索使用时，只需要按照不同结构的query对象传递实现类的json结构就行。

1. 将数据添加到索引中

   \#curl -XPUT -d  '{"id":1,"title":"es简介","content":"es好用好用真好用"}' <http://10.9.104.184:9200/index01/article/1>

   \#curl -XPUT -d  '{"id":1,"title":"java编程思想","content":"这就是个工具书"}' <http://10.9.104.184:9200/index01/article/2>

   \#curl -XPUT -d  '{"id":1,"title":"大数据简介","content":"你知道什么是大数据吗，就是大数据"}' <http://10.9.104.184:9200/index01/article/3>

2. match_all  查询所有。

   \#curl  -XGET http://10.9.104.184:9200/index01/_search  -d '{"query":  {"match_all": {}}}'

   ![](https://note.youdao.com/yws/api/personal/file/FFA5F1999D8643A08E2645DE70599832?method=download&shareKey=b349ab93a269db02102790428ca8d9e9)

3. term_query  

   词项查询是查询的基础方式。通过词项查询看到一些现象，比如生成document时使用的分词器？？

   \#curl -XGET <http://10.9.104.184:9200/index01/_search> -d  '{"query":{"term":{"title":"java"}}}'

   可以搜索到数据 java编程思想这本书

   \# curl -XGET <http://10.9.104.184:9200/index01/_search> -d '{"query":{"term":{"title":"编程"}}}'

   搜索不到数据，表示新增document时，进行分词计算的过程没有把java编程思想--java ,编程，思想

   总结：

   在lucene中封装document时，给每一个属性都赋值了各种参数，使用writer时也确定分词器的绑定，但是在es中这些内容都没有看到。

   1. document的属性对应类型，对应存储，对应分词计算   
   2. 分词器默认是：standard是默认分词器   
   3. 如何指定自定义的分词器，如何使用IK分词器做中文计算？  

4. match_query  

   curl -XGET <http://10.9.104.184:9200/index01/_search> -d '{"query":{"match":{"title":"java编程思想"}}}'

   是在搜索功能时使用的一个查询query对象。可以对文本做分词解析，默认也是用standard。将匹配到的所有document都返回

   底层实现分词计算后，配合title实现termquery

   java编程思想--> java  编，程，思，想

   title:java

   title:编

   title:程

## 五、Es的mapping和ik分词器

#### 1.安装ik分词器

##### 1.1 将ik插件包解压到es的plugins目录下

三台云主机的es都要进行解压，否则后续2台没有ik分词器插件，将会在搭建集群时报错。

[root@10-9-104-184 resources]#  unzip /home/resources/elasticsearch-analysis-ik-5.5.2.zip -d  /home/software/elasticsearch-5.5.2/plugins/

##### 1.2 测试使用ik分词器

1. 重启es   

2. 测试访问使用分词器计算文本

   1. curl  -XPOST <http://10.9.104.184:9200/_analyze>  -d '{"analyzer":"ik_max_word","text":"JAVA编程思想"}'   

   2. 访问浏览器  

      <http://10.9.104.184:9200/index01/_analyze?analyzer=ik_max_word&text=中华人民共和国>

      浏览器get测试，访问一个已存在的索引文件。参数 analyzer分词器名称 text测试的文本

#### 2.mapping映射

在es中，对数据处理有管理结构，索引下有类型，类型下有document，文档中有域属性。类型可以使用mapping来实现该类下document的数据结构的定义（非常类似table结构 schema）。mapping可以设置一个类下document的各种不同域属性的详细类型，不同属性使用不同的分词器。

##### 2.1 动态mapping

根据document中新增时携带的数据，决定对应的各种field域属性的类型，和分词器使用。

1. 新建的索引，mapping默认是空的，直到新增一个document出现mapping结构，这种就是动态mapping  

   [root@10-9-104-184  logs]# curl -XPUT <http://10.9.104.184:9200/index02>

   [root@10-9-104-184  logs]# curl -XGET <http://10.9.104.184:9200/index02/_mappings>

   {"index02":{"mappings":{}}} 

   新增一条document数据，动态mapping生成对应一些数据，实现当前索引的该document的映射。

   ```
   {
     "index02": {
       "mappings": {
         "article": {  表示mapping中的类型
           "properties": { 类型的属性
             "content": {  域名称
               "type": "text",  域的类型相当于TextField "analyzer":"standard",
               "fields": { 对当前域扩展
                 "keyword": {
                   "type": "keyword", keyword相当于StringField
                   "ignore_above": 256 虽然默认对字符串做整体存储，但是认为一旦长度超过256字节，就超过整体存储的默认要求
                 }
               }
             },
             "id": { 域属性
               "type": "long" 域类型
             },
             "title": { 域属性
               "type": "text", 相当于TextField
               "fields": {
                 "keyword": {
                   "type": "keyword",
                   "ignore_above": 256
                 }
              }
            }
           }
         }
      }
     }
   }
   ```

##### 2. 2 静态mapping

1. 手动设置静态mapping 

   手动在创建索引的时候，就将自定义生成的静态mapping结构一并的添加进去。

   curl    -XPUT  -d '{"mappings":{"article":{"properties":{"id":{"type":"long"},"title{"type":"text","analyzer":"ik_max_word"},"content":{"type":"text","analyzer":"ik_max_word"}}}}}' 
   <http://10.9.104.184:9200/index03/>

2. 测试数据  

   1. 新增一条document数据  id title content 

      curl -XPUT -d '{"id":1,"title":"es简介","content":"es好用好用真好用"}' <http://10.9.104.184:9200/index03/article/1>

   2. 使用term_query查询  title:编程  

      1. 如果返回数据，说明该数据的分词词项有编程2个字，ik分词器生效

         curl -XGET <http://10.9.104.184:9200/index03/_search> -d '{"query":{"term":{"title":"编程"}}}'

## 六、ES集群

#### 1.es集群搭建逻辑

和redis mycat数据库集群有区别，启动web应用，封装了扩展的功能

可以通过发现节点的协调器将一个集群所有启动节点（在同一个网络汇中）自动添加到一起实现集群的功能。

#### 2.ES集群的结构

##### 2.1 节点角色

1. master:整个集群的大脑，管理集群所有的元数据（meta-data）信息；

   元数据：记录数据信息的数据。

   master管理的元数据：索引有哪些，索引分片几个，索引分片副本几个，分片存放到位置，副本存放到位置。

   master可以修改元数据，集群中唯一一个可以有权限修改元数据的角色，其他节点角色要想使用元数据，只能从master同步获取。

2. data：数据节点，读写数据。让我来读，我就读，让我来写我就写，存储就是索引的分片数据

3. ingest:集群节点可以很多，对外访问的连接节点。

4. 协调器：众多的节点和角色要想按照运行逻辑完美的整合和添加到一个集群中工作，需要引入协调器。

##### 2.2 结构图

一个es节点，可以同时具备多个角色。

![](https://note.youdao.com/yws/api/personal/file/3FA63FFF109A424AB8D70BA3C79FFAEF?method=download&shareKey=e267bf89beae899fe76ad59e3073a016)

#### 3. 集群的搭建

##### 3.1 三台es节点

保证环境正确：

​	网络环境能够连通。

​	配置信息：

​		节点名称不同 es01,es02,es03

​		集群名称要相同 elasticsearch

​		ip地址host不同

​		ik分词器安装相同

##### 3.2 配置elasticsearch.xml

1. 协调器

   三个节点，节点角色取决于配置文件中配置的node值

   node.master:  true

   node.data:  true

   node.ingest:  true

   默认情况每一个es都会配置master data ingest表示当前节点可以是三个角色。

   协调器：任意一个es节点都可以配置为集群的协调器。保证协调器在配置时出现宕机无法使用，一般这里都需要配置多个协调器。需要从3台es节点选择2个作为协调器。

   69行配置ip地址

   ![](https://note.youdao.com/yws/api/personal/file/F19417D8B6B045169865CD7131489666?method=download&shareKey=2c6901ea54302335b929f5f2e744089a)

2. 最小有效master个数（过半数量）  

   73行配置防止脑裂出现的最小master数量

   ![](https://note.youdao.com/yws/api/personal/file/0065AE03F5C24C0A9111B4733C405BC4?method=download&shareKey=8f78ec6ac8b3f8029c0b713dec3054dc)

   脑裂：在一个es集群中，现役master由于网络波动，导致集群判断它宕机，进行新一轮的选举，出现新的现役master。当网络波动恢复，整个集群就相当于被2个master同时管理，出现两份meta数据。造成集群数据混乱

   ![](https://note.youdao.com/yws/api/personal/file/F4428E0406554572958B103AE9A3E439?method=download&shareKey=8000e0ea4b4b588136db10288339cd26)

   es采用配置过半的集群有效master数量，防止脑裂的出现。

   结论：无论如何出现master的网络波动，至多集群存在一个有效的master

##### 3.3 启动和选举逻辑

挨个启动es节点，最后通过head插件可以看到一个分布式的高可用的集群es。

启动之后，数据会经过es计算，实现迁移，将分片做到多个节点的分布式集群中。

配置分片和副本数量，适当调整集群的分布式和高可用。

分片数量调高：一个集群就扩展更多的节点。

副本分片数量调高：支持更高的高可用。

###### 3.3.1 选举逻辑

集群运行过程中，选举逻辑一直存在，只要协调器不全部宕机，集群就可以正常执行选举逻辑。

1. 每个节点（具备node.master：true）启动时都会连接ping通协调器（连任何一个都可以），获取集群当前的状态。其中有个对象activeMaster保存了现役master信息。进入第二步   
2. 判断acitiveMaster是否为空，为空则证明没有选举结束，进入第三步选举逻辑。不为空，说明选举已经执行并且选择出了一个master，选举逻辑就结束（选举逻辑总是在这里结束）   
3. 当前节点将集群中所有master节点都存放到一个备选人list中，candidata{es01,es02}进入第四步判断  
4. 判断备选人list里的master节点数量是否满足最小master数量，如果满足从中比较节点==id最大的为现役master==并且将选好的master信息存放在activeMaster中。如果不满足最小master数量重新回到第一步。

节点宕机重新选举

1. 现役master宕机，剩余master重新执行选举逻辑（activeMaster空了）。
2. 备选的master宕机，只要剩余master满足最小master数量不影响集群使用。

###### 3.3.2 内存不够的问题

1. es进程的jvm默认判断需要最高占用2G。 

2. 修改配置文件config/jvm.options。

   ![](https://note.youdao.com/yws/api/personal/file/1EF43FFEAD454BA78F96097A41EF6A98?method=download&shareKey=8296b4f22d0ba92ddc881a89ebd97763)

## 七、ES的客户端代码

#### 1.TransportClient的java客户端

理论上来讲通过http协议的访问操作es，也可以使用RestTemplate操作es的java代码。

TransportClient客户端，包装了TCP/IP，可以通过访问。连接es节点的9300端口实现方法api的调用操作es。

##### 1.1 依赖资源

1. 不能和lucene测试6.0.0的依赖在同一个系统中  

   会冲突，es的依赖包含了lucene 5.5.2lucene版本6.6

2. dependency配置  

   1. elasticsearch  

      ```
      <!--elasticsearch 核心包-->
      <dependency>            			 
      		<groupId>org.elasticsearch</groupId>
      		<artifactId>elasticsearch</artifactId>
          <version>5.5.2</version>
       </dependency>
      ```

   2. transport客户端  

      ```
      <!--transportclient-->
      <dependency>            	
      		<groupId>org.elasticsearch.client</groupId>
          <artifactId>transport</artifactId>
          <version>5.5.2</version>
      </dependency>
      ```

##### 1.2 测试代码

1. 索引管理

   ```Java
   /*
           索引管理，增加索引，判断存在，删除等
        */
       @Test
       public void indexManage(){
           //通过client拿到索引管理对象
           AdminClient admin = client.admin();
          // ClusterAdminClient cluster = admin.cluster();
           IndicesAdminClient indices = admin.indices();
           //TransportClient中有2套方法，一套直接发送调用
          //一套是预先获取request对象。
           CreateIndexRequestBuilder request1 = indices.prepareCreate("index01");//不存在的索引名称
           IndicesExistsRequestBuilder request2 = indices.prepareExists("index01");
           boolean exists = request2.get().isExists();
           if(exists){
               /*System.out.println("index01已存在");
               return;*/
               indices.prepareDelete("index01").get();
          }
           //发送请求request1到es
           CreateIndexResponse response1 = request1.get();
           //从reponse中解析一些有需要的数据
           System.out.println(response1.isShardsAcked());//json一部分 shards_acknowleged:true
           System.out.println(response1.remoteAddress());
           response1.isAcknowledged();//json acknowledged:true
       }
   ```

2. 文档管理

   ```Java
   /*
   文档管理：向es中操作文档 新增，查询，删除
   */
       @Test
       public void documentManage() throws JsonProcessingException {
           //新增文档，准备文档数据
           //拼接字符串 对象与json的转化工具ObjectMapper
           //String docJson="{\"\"}"
           Product p=new Product();
           p.setProductNum(500);
           p.setProductName("三星手机");
           p.setProductCategory("手机");
           p.setProductDescription("能攻能守，还能爆炸");
           ObjectMapper om=new ObjectMapper();
           String pJson = om.writeValueAsString(p);
           //client 获取request发送获取response
           //curl -XPUT -d {json} http://10.9.104.184:9200/index01/product/1
           IndexRequestBuilder request = client.prepareIndex
                   ("index01", "product", "1");
           //source填写成pJson
           request.setSource(pJson);
           request.get();
       }
   ```
   
   ```Java
       @Test
       public void getDocument(){
           //获取一个document只需要index type id坐标
           GetRequestBuilder request = client.prepareGet
                   ("index01", "product", "1");
           GetResponse response = request.get();
           //从response中解析数据
           System.out.println(response.getIndex());
           System.out.println(response.getId());
           System.out.println(response.getVersion());
           System.out.println(response.getSourceAsString());
           client.prepareDelete("index01","product","1");
       }
   ```
   
   
   
3. 搜索使用

   ```Java
    public void search(){
           //其他查询条件也可以创建
          MatchQueryBuilder query = QueryBuilders.matchQuery("productName",
                   "三星手机");
           //封装查询条件，不同逻辑查询条件对象不同
           //TermQueryBuilder query = QueryBuilders.termQuery
                  // ("productName", "星");
           //底层使用lucene查询，基于浅查询，可以支持分页
           SearchRequestBuilder request = client.prepareSearch("index01");
           //在request中封装查询条件，分页条件等
           request.setQuery(query);
           request.setFrom(0);//起始位置 类似limit start
           request.setSize(5);
           SearchResponse response = request.get();
           //从response对象中解析搜索的结果
           //解析第一层hits相当于topDocs
           SearchHits searchHits = response.getHits();
           System.out.println("总共查到："+searchHits.totalHits);
           System.out.println("最大评分："+searchHits.getMaxScore());
           //循环遍历的结果
           SearchHit[] hits = searchHits.getHits();//第二层hits
           //hits包含想要的查询结果
           for (SearchHit hit:hits){
               System.out.println(hit.getSourceAsString());
           }
       }
   ```

#### 2.transportClient整合到springboot

##### 步骤

1. 创建一个搜索工程

   1. quickstart   
   2. pom：
      1. 继承   
      2. 依赖：
         1. common-resource   
         
         2. eureka-client  
         
         3. elastcisearch2个  
         
            ```
             <!--elasticsearch 核心包-->
                    <dependency>
                        <groupId>org.elasticsearch</groupId>
                        <artifactId>elasticsearch</artifactId>
                        <version>5.5.2</version>
                    </dependency>
                    <!--transportclient-->
                    <dependency>
                        <groupId>org.elasticsearch.client</groupId>
                        <artifactId>transport</artifactId>
                        <version>5.5.2</version>
                    </dependency>
            ```
         
         4. 持久层3个jdbc,mysql,mybatis  
   3. application.properties
      1. 10006   
      2. searchservice  
      3. 按照配置整合准备es信息  
   4. 启动类
      1. springbootapplication   
      2. enableeurekaclient   
      3. mapperscan  

2. 整合springboot的transportclient

   1. 配置类@Configuration   

   2. bean方法@Bean初始化一个TransportClient对象  

   3. @ConfiugrationProperties读取属性

      es.nodes=10.9.104.184:9300,10.9.104.184:9300  

## 八、ELK家族

#### 1.搜索系统结构

1. 数据整理封装，放到es中保存，index   

2. 客户端系统访问es封装请求的参数为query对象查询数据  

   ![](https://note.youdao.com/yws/api/personal/file/D358249C3CA74182A31BF947D11DF282?method=download&shareKey=63ff0e26843151e1bf1ffa3e6d87497c)

   通过业务逻辑考虑数据一致性，数据库和索引文件。数据库的数据源可能发生各种数据变动，考虑保证es中index的一致性。

#### 2.ELK家族

基于elasticsearch的全文检索功能行程一整套技术体系。包含了数据导入、数据更新、全文检索数据管理和数据视图化展示和分析。

E：elasticsearch--有大量的数据 index

L：logstash--日志数据收集器，将各种数据源的数据导入es中

k：kibana--视图展示，数据分析工具 

这样一个结构，使得存储在es中的数据可以实时更新，并且除了提供搜索以外，还可以实现更高的价值

