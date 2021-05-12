[TOC]

# Lucene

## 一、全文检索概述

#### 1.搜索需求

##### 1.1 信息过载

由于云平台、云计算、云服务、大数据兴起。用户量增长迅速，系统多。

例如：

微信图片上传 一天图片上传量 PB级别。

视频网站 一份中上传视频播放600小时以上

电信系统一天日志受TB级别

网页数量-千亿级别甚至更多。

##### 1.2 系统的搜索功能

常用各种软件，app都存在搜索，搜视频，搜日志，搜博文，搜商铺，搜商品等等。

继续需求：快，准，支持大数据量，支持丰富的查询功能。

##### 1.3 传统搜索功能

1. 文件搜索

   window各种操作系统对文件内容的检索功能，将文件加载到内存，使用内存搜索。---数据量小

2. 数据库搜索

   数据量由于数据库高可用分布式集群，可以实现海量存储，但是搜索做不到快和准，并且搜索功能有限制。

#### 2.全文检索技术

##### 2.1 全文检索

==全文数据库是全文检索系统的主要构成部分==（全文检索是围绕着一个全文数据库展开）。所谓全文数据库是将一个完整的信息源（数据整体，一个网页，一个表格的行数据）的全部内容转化为计算机可以识别、处理的信息单元而形成的数据集合。全文数据库不仅存储了信息，而且还有对全文数据进行词、字、段落等更深层次的编辑、加工的功能，而且所有全文数据库无一不是海量信息数据库。

##### 2.2百度搜索引擎架构

搜索引擎属于检测系统，检索系统包含三部分组成。

信息采集：从数据源收集导入数据的过程，需要采集系统完成。

信息整理：采集过来的数据绝大可能是非结构化。需要对非结构化数据进行整理，编辑成可用的结构。

搜索服务系统：给用户使用的前端系统。使用到整理之后的数据。

![](https://note.youdao.com/yws/api/personal/file/05A8D23C802B41558DDCDCF1ECF69248?method=download&shareKey=d55732ecf4cb5dffdee0770528bff760)

## 二、Lucene

#### 1.Lucene概括

##### 1.1 定义

属于全文检索技术之一。Lucene是全文检索技术的==工具包==。利用Lucene可以实现快速方便开发全文检索技术。在没有Lucene这种工具包之前，就已经存在了全文检索技术。创建索引，搜索索引过程每一个细节，不同的技术实现思路一样。

##### 1.2 和Hadoop关系

创始人是Doug Cutting（国内昵称狗哥）。2000年左右研发Lucene，后来才有的Hadoop。有了Lucene开始创建nutch搜索引擎，但是卡在海量数据存储问题。引入了Hadoop第一个版本hdfs分布式文件系统。

##### 1.3 特点

==基于Java原生语言开发==

1. 索引文件创建速度快
2. 占用栈内存空间小
3. 支持多种丰富的搜索功能

##### 1.4 索引文件结构

Lucene创建的索引，使用倒排索引算法，在索引文件中除了数据（网页，数据库数据整理的内容）保存了很多关系表。

索引文件的创建过程，需要对数据文本做词、字段落加工。

1. 分词：将一个文本数据切分成更小单位的词、字、段、句等存储在索引文件形成大量分词结果。（词项（term））。用来保存与数据的对应关系。

   例如：原数据：中华人民共和国

   分词计算：

   字处理：中，华，人，民，共，和，国

   词处理：中华，华人，人民，中华人民，人民共和国

   句处理：中华人民共和国

2. 文档（document）：==整理后存储在索引文件中的数据单位==，对应源数据一条或者一页，是一个整体内容。例如，海量网页，每个网页在索引文件中对应一个文档对象，日志中每一行日志对应索引文件中一个文档对象。只要根据不同的数据源给文档对象定义各种不同属性。

##### 1.5 倒排索引计算步骤

1. 数据源读取。

   1. web1：
      1. 疫情到达拐点（标题）
      2. 科学家预测月底到达拐点（内容）
      3. 新华社（出版社）
   2. web2： 
      1. 疫情已经被控制住（标题）
      2. 全国新增病例呈现下降趋势（内容）
      3. 新华社（出版社）

2. 封装document文档对象

   1. doc1封装web1
      1. id：1
      2. title：疫情达到拐点
      3. content：科学家预测月底到达拐点
      4. publisher：新华社
   2. doc2封装web2
      1. id:2
      2. title：疫情已经被控制
      3. content：全国新增病例呈现下降趋势
      4. publisher:新华社

3. 计算分词，对词的加工。每一个分词计算结果term都可以携带很多计算过程出现的参数，比如分词term所在的document的id，出现频率，出现位置。term（docid,属性名）

   doc1：.疫情（1，title）,达到（1，title）,科学家（1，content),拐点（1，conten）;新华社（1，publisher）

   doc2:疫情（2，title）,控制（2，title）,全国（2，content）,趋势（2，content）,新华社（2，publisher）

4. 合并分词形成倒排索引表

   | 词项\doc | doc1(1) | doc2(2) | doc3(3) | doc4(4) |
   | -------- | ------- | ------- | ------- | ------- |
   | 疫情     | 1       | 1       | 0       | 0       |
   | 达到     | 1       | 0       | 0       | 0       |
   | 科学家   | 1       | 0       | 0       | 0       |
   | 新华社   | 1       | 1       | 0       | 0       |
   | 控制     | 0       | 1       | 0       | 0       |
   | 全国     | 0       | 1       | 0       | 0       |
   | 趋势     | 0       | 1       | 0       | 0       |

   只要表格中记录词项与document之间的关系。假如计算，查询关键字

   全国疫情趋势-->全国，疫情，趋势。

   1. 查询，包含所有3个词语的document文档对象  

      疫情：1100

      全国：0100

      趋势：0100

      位的与运算得到：0100.直接获取查询结果document2存在3个词语

   2. 查询，只要有其中一个词语的document就算查到  

      疫情：1100

      全国：0100

      趋势：0100

      位的或运算：1100，doc1,doc2中存在这些词语

5. 将倒排索引表和document数据一并输出到索引文件 形成了能够快速定位查询的数据结构

   ![](https://note.youdao.com/yws/api/personal/file/AFEEFC05EE5C4928B5177F2210FA97B5?method=download&shareKey=5484dabef2a22d528e19317ec399ca78)

#### 2.Lucene中的分词器

在我们要使用的数据源中形成最终的索引文件的过程，必须要经过数据的分词计算。数据源中的文本内容是非常丰富的语言例如：德文、俄文等。底层二进制都是不一样的编码，使用同一个分词计算的原理，不能实现对所有文本数据进行分词计算的需求。Lucene不可能提供具体详细的分词计算逻辑。所以Lucene对于分词提供了一些基础的分词计算，同时提供了==创建分词计算的规范--接口Analyzer。==可以在不满足需求的情况下自定义接口实现类完成对应分词计算。

##### 2.1 Lucene提供的分词器

字，词，句，段都可以处理的一些分词器。

StandardAnalzyer:标准分词器---字。中华人民 中，华， 人，民 词 who are you

SimpleAnalyzer:简单分词器---按照标点，空格，回车切分文本

WhitespaceAnalyzer:空格分词器---按照空格切分

SmartChineseAnalyzer:智能中文分词器---中文分词，我们都是好朋友--我们，都是，朋友

##### 2.2 代码测试分词器

1. 引入依赖  

   ![](https://note.youdao.com/yws/api/personal/file/098DF02A046E4D4A852083EAF70075D7?method=download&shareKey=1c2f8a1dbc71cf0c32b491cffb81a2c9)

2. 测试代码

   ![](https://note.youdao.com/yws/api/personal/file/2A187ABB0F5346279BC1CC7EF4473C2E?method=download&shareKey=9a6ebf0f0f03647e103f182231e1fbc9)

   ```Java
    /*
       测试方法，实现对一个字符串的使用分词器实现类解析
       的过程，不需要关心分词计算底层，只要使用实现类
       将分词结果打印出来 看到词，字，段的文字
        */
       //org.apache.lucene.*
       public void analyze(Analyzer analyzer,String msg)throws Exception{
           //将字符串对象，转化成流数据
           StringReader reader=new StringReader(msg);
           //使用分词器对象，获取切分后的流数据
           TokenStream stream = analyzer.
   
                  tokenStream("test", reader);
           //二进制计算完分词，已经指针指向末尾
           stream.reset();
          //查看stream中的各个切分之后的词项的文本属性打印
           //拿到文本属性
           CharTermAttribute attribute =               stream.getAttribute(CharTermAttribute.class);
           //循环遍历每一个二进制，打印文本属性
           while(stream.incrementToken()){           System.out.println(attribute.toString());
           }
       }
      //编写测试代码，提供多个不同的实现类对象
       @Test
       public void run()throws Exception{
           //准备一个字符串
           String msg="我们的课程,还要更深入的,研究疫苗形成";
           //分词器实现类
           Analyzer a1=new StandardAnalyzer();
           Analyzer a2=new SimpleAnalyzer();
           Analyzer a3=new WhitespaceAnalyzer();
           Analyzer a4=new SmartChineseAnalyzer();
           System.out.println("************标准**********")
               analyze(a1,msg);
           System.out.println("************简单**********");
               analyze(a2,msg);
           System.out.println("************空格**********");
               analyze(a3,msg);
           System.out.println("************智能**********");
               analyze(a4,msg);
       }
   ```

3. 中文分词器IK  

   即使使用像SmartChinese这样的分词器对象，也很难满足要求。原因分词词语，都是固定，不能发展变化。词语必须支持发展变化。随着网络发展，新词语出现速度、频率大大增加。

   常用的中文分词器中，IK分词器是基于词典实现的分词计算。可以通过编辑词典、扩展、停用的使用。停用一些无效词语分词：一个，我们，好多。

##### 2.3 ik分词器

引入一个依赖：maven中本地依赖，将这个jar包添加到当前maven工程。中央库没这个包。

1. ik分词器jar包放到一个么有中文没有空格的目录中  

   ![](https://note.youdao.com/yws/api/personal/file/D621652327C44DFDB3D13ADD199F1F52?method=download&shareKey=fe55591a575d323c825cd6010658b2f4)

   G://IKAnalyzer2012_u6.jar

   在pom文件的dependency中的scope标签。表示当前依赖的使用范围

   1. compile：编译，运行，测试，打包安装。默认值   
   2. test:只在测试时生效，运行，打包，安装，发布都不会使用这个jar  
   3. runtime：compile一样，不需要编辑，运行测试，打包安装发布都存在；spring中jdbc.  
   4. provided:运行最后打包安装部署不使用。编译时检查。servlet-api。web应用为了编辑servlet代码引入这个jar包，否则编译异常。但是最终运行代码项目时是在tomcat容器如果servlet-api打包运行了，会和tomcat中冲突  
   5. system:使用本地jar包的scope范围，配合一个本地路径，将jar包导入到当前工程，由maven管理。  

2. 将已经实现的analyzer 的ik实现类拷贝到工程中。  

   ![](https://note.youdao.com/yws/api/personal/file/8E13F7FCE4174878A2A2F2489B9F779E?method=download&shareKey=c90ce373ca12512a4ae299ca95714682)

   放到测试环境中

3. ik分词器的词典使用。  

   1. IKAnalyzer.cfg.xml  配置词典配置文件，需要在ik加载到这个文件。将这个文件放到当前工程resources   

   2. **.dic后缀文件就是ik分词器可以在配种使用的各种词典。

      ![](https://note.youdao.com/yws/api/personal/file/4125B514AD3D4FC989F52E381DF43244?method=download&shareKey=2e266fcba477c29113e761d36c885725)

   3.   词典文件**.dic需要和配置文件同目录 

### 三、Lucene实现创建索引和搜索索引

#### 1.创建索引文件

全文检索技术围绕全文数据库进行索引检索。

##### Lucene创建索引文件步骤

1. 读取源数据，读取数据库数据（t_product）

   jdbc，持久层，手动拼接模拟获取的数据源

2. 创建索引文件的输出目录

3. 生成输出流对象writer（指定输出时计算分词的分词器）

4. 封装document对象

5. 将document输出到索引文件形成一个完整的索引文件

属性类型：TextFiled与StringFiled区别

存储索引数据逻辑：Store.YES与Store.No区别。

![](https://note.youdao.com/yws/api/personal/file/FFFA0D49323B425E85304D81BF265F86?method=download&shareKey=0ccea0a1254874efe41aaaf90bf25656)

cfe,cfs,si存储数据的文件。对应存储很多倒排索引表，存储了数据对象2个document。

```Java
package cn.tedu.lucene;
import com.jt.common.pojo.Product;
import org.apache.lucene.document.*;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.index.IndexWriterConfig;
import org.apache.lucene.store.FSDirectory;
import org.junit.Test;
import java.nio.file.Path;
import java.nio.file.Paths;
/**
 * 模拟lucene创建索引，封装数据源过程
 */
public class CreateIndex {
    @Test
    public void createIndex()throws Exception{
        //读取数据源。String模拟
        //商品数据
        Product p1=new Product();
        p1.setProductId("1");
        p1.setProductCategory("手机");
        p1.setProductDescription("3亿像素，高清摄影，照亮景色的美");
        p1.setProductName("达内PN3 pro手机");
        p1.setProductNum(500);
        Product p2=new Product();
        p2.setProductId("2");
        p2.setProductCategory("电脑");
        p2.setProductDescription("游戏发烧的选择，泰坦2080TI");
        p2.setProductName("达内杀手3000");
        p2.setProductNum(200);
        //准备索引文件的输出目录
        Path path = Paths.get("D:/index01");//索引文件目录
        FSDirectory dir=FSDirectory.open(path);
        //给writer输出流配置一个配置对象
        IndexWriterConfig config=new
                IndexWriterConfig(new IKAnalyzer6x());
        //定义创建索引的模式追加-覆盖
        //create 覆盖，每次执行都会对已有的同名目录做覆盖
        //append追加，同名文件夹存在，会对数据追加
        //create_or_append 有则追加，无则创建
        config.setOpenMode(IndexWriterConfig.OpenMode.
                CREATE);
        //创建输出流对象
        IndexWriter writer=new IndexWriter(dir,config);
        //封装document 从2个原数据p1 p2封装2个索引需要的doc数据
        //doc的数据结构需要和原数据一致，自定义doc中属性
        //属性分为2大类，一类字符串类，一类数字类
        Document doc1=new Document();
        Document doc2=new Document();
        //TextField StringField存储字符串数据
        //name自定义属性名称
        //value：属性值
        //store: 表示是否存储在索引中
        doc1.add(new TextField("productId",p1.getProductId(),
                Field.Store.YES));
        doc1.add(new TextField("productCat",p1.getProductCategory(),
                Field.Store.YES));
        doc1.add(new TextField("productDesc",p1.getProductDescription(),
                Field.Store.YES));
        doc1.add(new TextField("productName",p1.getProductName(),
                Field.Store.YES));
        doc1.add(new IntPoint("num",p2.getProductNum()));
        doc2.add(new TextField("productId",p2.getProductId(),
                Field.Store.YES));
        doc2.add(new TextField("productCat",p2.getProductCategory(),
                Field.Store.YES));
        doc2.add(new StringField("productDesc",p2.getProductDescription(),
                Field.Store.YES));
        doc2.add(new TextField("productName",p2.getProductName(),
                Field.Store.NO));
        doc2.add(new StringField("num-view",p2.getProductNum()+"个", Field.Store.YES));
        doc2.add(new IntPoint("num",p2.getProductNum()));
         //将document封装到writer
        writer.addDocument(doc1);
        writer.addDocument(doc2);
        //创建索引
        writer.commit();//文件夹出现默认空环境结构，没有数据
    }
}
```

#### 2.使用luke软件打开索引文件观察内容

![](https://note.youdao.com/yws/api/personal/file/E974663A6B97440DAA03D8EBEBB11C79?method=download&shareKey=54aead60fba79d8fed837c1423142507)

StringField和TextField的区别:

同时处理字符串数据。TextField要计算分词，StringField不计算分词。不是所有的字符串数据都需要计算分词，例如：uuid的主键值，web网页链接地址。

Store.YES/Store.NO:是否在索引中保存该数据。

当搜索之后获取的数据没有使用到的时候就不需要在索引文件中保存起来。不保存，不等于不计算分词。不保存，也可以计算分词，并且使用分词查询到该数据，但是即使查询，也读不到数据。 

存不存到索引 Store决定，分不分词计算是TextField StringField决定。

整数类型 IntPoint LongPoint DoublePoint  FloatPoint处理整数

既不计算分词，也不存储数据。仅仅用来实现范围数字搜索的特性。搜索num在【200,500】之间的所有数据。

如果说既要使用数字做范围搜索，又要拿到数字。使用StringField类型，存储一个同名的field域的数据。

## 四、Lucene搜索功能

#### 1.Lucene不同查询条件

Lucene支持丰富的查询功能，其实将不同的查询逻辑封装到了不同的query类中，在这通过对不同实现类的使用，查询创建索引文件中的数据。

##### 1.1 词项查询

TermQuery：封装了一个词项，到索引文件中找到唯一对应的一个词项，词项对应的document获取

###### 1.1.1 搜索实现步骤

1. 指向一个索引文件。
2. 输入流创建搜索对象。
3. 构造不同的查询条件。
4. 利用Lucene的==浅查询==遍历结果获取数据。

###### 1.1.2 浅查询

1. 搜索海量数据时，实现分页查询使用的一种计算逻辑。浅查询==只会对数据的标识（数据的下标。）做计算，然后利用计算结果再去获取数据。==
2. 对比深查询分页逻辑。==分页最后一项数据之前的内容全部读取出来，然后抛弃分页数据以外的内容==（算法简单）

```Java
/*
    词项查询 对域名，词项值匹配，一旦匹配返回查询结果
     */
    @Test
    public void termQuery() throws Exception {
        //获取索引文件通道,指向索引文件
        Path path = Paths.get("d:/index01");
        FSDirectory dir=FSDirectory.open(path);
        //获取输入流，构造一个搜索对象
        IndexReader reader= DirectoryReader.open(dir);
        IndexSearcher searcher=new IndexSearcher(reader);
        //创建一个搜索对象 TermQuery
        Term term=new Term("productName","手机");
        Query query=new TermQuery(term);
        //浅查询遍历数据结果
        //拿到对标识做了计算的一个数据 topDocs
        TopDocs topDocs = searcher.search(query, 10);//拿到前10条数据
        // doc id计算结果 里面只封装了 查询评分和docid
        //从中解析封装docId的对象数组
        ScoreDoc[] scoreDocs = topDocs.scoreDocs;
        //数组每个元素就是docId封装
        for(ScoreDoc scoreDoc:scoreDocs){
            //解析需要搜索的分页的id值            System.out.println("docId:"+scoreDoc.doc);
            //通过id读取document对象
            Document doc = searcher.doc(scoreDoc.doc);
            //使用数据，打印数据值
            System.out.println("productName"
                    +doc.get("productName"));
            System.out.println("productCat"
                    +doc.get("productCat"));
            System.out.println("productDesc"
                    +doc.get("productDesc"));
            System.out.println("productNum"
                    +doc.get("num"));
        }
    }
```

##### 1.2 多域查询

TermQuery可以查询数据中某一个filed属性，而MultiFieldQuery可以将不同的filed数据封装在同一个查询条件中，构造一个可以同时查询多个属性对应的document的查询条件。

```Java
@Test
    public void multiFieldQuery() throws Exception {
        //指向搜索数据索引文件
       Path path = Paths.get("d:/index01");
        FSDirectory dir=FSDirectory.open(path);
        //拿到搜索对象
        IndexReader reader=DirectoryReader.open(dir);
       IndexSearcher searcher=new IndexSearcher(reader);
        //构造查询条件
            //多个域
        String[] fields={"productName","productCat","productDesc"};
            //解析查询文本的解析器 需要使用分词器
        MultiFieldQueryParser parser=new
                MultiFieldQueryParser(fields,new IKAnalyzer6x());
            //当使用一个字符串进行查询时，先进行分词计算
            /*
            "达内手机顶呱呱"-->手机，顶呱呱
            排列组合
            proudctName:手机，productName:顶呱呱
            productCat:手机，productCat:顶呱呱
            productDesc:手机，productDesc:顶呱呱
            一旦任何一个词项匹配到了document，就将其放到
            返回结果中使用             */
            //提供一个查询关键字文字
        String text="达内手机顶呱呱";
        Query query = parser.parse(text);//向上造型多域查询
        //遍历循环。
        TopDocs topDocs = searcher.search(query, 10);
        ScoreDoc[] scoreDocs = topDocs.scoreDocs;
        for(ScoreDoc scoreDoc:scoreDocs){           System.out.println("documentId:"+scoreDoc.doc);
            //获取document对象
            Document doc = searcher.doc(scoreDoc.doc);
            //使用数据，打印数据值
            System.out.println("productName"
                    +doc.get("productName"));
            System.out.println("productCat"
                    +doc.get("productCat"));
            System.out.println("productDesc"
                    +doc.get("productDesc"));
            System.out.println("productNum"
                    +doc.get("num"));
        }
    }
```

##### 1.3 布尔查询

可以在lucene中实现多种不同查询功能，每一个都对应一个query对象，每个对象查询都可以对应一批结果集。可以利用布尔的逻辑，实现多个query的逻辑关系处理例如：并集、交集。多个query就可以形成布尔查询的子条件使用。

```Java
 @Test
    public void booleanQuery() throws Exception {
        Path path = Paths.get("d:/index01");
        FSDirectory dir=FSDirectory.open(path);
        IndexReader reader=DirectoryReader.open(dir);
        IndexSearcher searcher=new IndexSearcher(reader);
        //构造查询条件
            //多个布尔子条件查询
            Query query1=new TermQuery(new Term("productName","手机"));
           Query query2=new TermQuery(new Term("productCat","手机"));
           //子查询封装成布尔的子条件
        //Occur发生逻辑值
            //must:查询结果一定属于该子条件的一部分
            //must_not：查询结果一定不属于该子条件
            //should：查询结果可以包含，也可以不包含该解条件，MUST同时使用
        //以must为准
           //filter：must相同的效果，
        // 但是使用filter的条件对应的结果，没有评分
        BooleanClause bc1=new BooleanClause(query1,
                BooleanClause.Occur.MUST);
        BooleanClause bc2=new BooleanClause(query2,
                BooleanClause.Occur.MUST_NOT);
            //利用2个子条件封装布尔
        Query query=new BooleanQuery.Builder().add(bc1).add(bc2).build();
        //遍历循环。
        TopDocs topDocs = searcher.searh(query, 10);
        ScoreDoc[] scoreDocs = topDocs.scoreDocs;
        for(ScoreDoc scoreDoc:scoreDocs){          System.out.println("documentId:"+scoreDoc.doc);
           //获取document对象
            Document doc = searcher.doc(scoreDoc.doc);
            //使用数据，打印数据值
           System.out.println("productName"
                   +doc.get("productName"));
            System.out.println("productCat"
                    +doc.get("productCat"));
            System.out.println("productDesc"
                   +doc.get("productDesc"));
            System.out.println("productNum"
                    +doc.get("num"))
        }
    }
```

##### 1.4 范围查询

对一些数字特性的属性，可以利用数字实现范围查询，最常见的范围搜索，价钱（100元-300元），尺寸（20寸-50寸）容量（256G-1Tb）；属性使用了什么Point就使用什么Point获取这个范围搜索的query对象

```Java
/*
    数字范围搜索
     */
    @Test
    public void rangeQuery() throws Exception {
        Path path = Paths.get("d:/index01");
        FSDirectory dir=FSDirectory.open(path);
        IndexReader reader=DirectoryReader.open(dir);
        IndexSearcher searcher=new IndexSearcher(reader);
        //构造查询条件
            //使用num域实现范围查询
        Query query=IntPoint.newRangeQuery("num",100,300);
        //遍历循环。
        TopDocs topDocs = searcher.search(query, 10);
        ScoreDoc[] scoreDocs = topDocs.scoreDocs;
        for(ScoreDoc scoreDoc:scoreDocs){
            System.out.println("documentId:"+scoreDoc.doc);
            //获取document对象
            Document doc = searcher.doc(scoreDoc.doc);
            //使用数据，打印数据值
            System.out.println("productName"
                    +doc.get("productName"));
            System.out.println("productCat"
                    +doc.get("productCat"));
            System.out.println("productDesc"
                    +doc.get("productDesc"));
            System.out.println("productNum"
                    +doc.get("num"));
        }
    }
```

##### 1.5 模糊查询

FuzzyQuery query=new FuzzyQuery(new  Term("name","tramp"))；

可以利用模糊查询，实现提供的参数模糊比对数据。

提供：tramp

分词数据：trump

日和曰 品 精 又 双

##### 1.6 通配符查询

| ？   | 标识字符，字符串 |
| ---- | ---------------- |
| *    | 字符串           |

WildcartQuery query=new WildcartQuery(new  Term("name","王*"))；当词项在name属性里有王军，王翠花，王首富都能被查询条件比对到

 