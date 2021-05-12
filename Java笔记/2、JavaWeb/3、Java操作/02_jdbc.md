[TOC]
# JDBC

#### 一、概念

##### 1.jdbc概念

- jdbc：(java  data  base connectivity)  java的链接技术
- jdbc技术：是由Java提供好的jar包。
- 需要学习的是：jar包的api（常用的类和接口），调用jar包中的方法操作数据库
- jdbc的组成：由两个主要的jar包组成  rt.jar/java.sql包,rt.jar/ javax.sql包
- 开发JDBC应用需要以上2个包的支持外，还需要导入相应JDBC的数据库实现(即数据库驱动)。

##### 2.驱动

*  为了能让程序员利用java程序操作数据库，数据库厂商提供了一套jar包，通过导入这个jar包就可以直接调用其中的方法操作数据库，这个jar包称之为驱动。

* 两个数据库驱动互不兼容。

* 行业中有很多种的数据库，要使用这么多数据库需要学习很多数据库驱动，对于程序员来说学习成本非常高。
* 想要让java程序兼容数据库，所有的数据库驱动都实现了jdbc这套接口。

##### 3.为什么要学习jdbc技术？

因为jdbc是web服务器端的技术，是一段Java程序，该程序支持用户的数据访问以及用户的数据存储！！！在整个web流程，jdbc是不可或缺的一个环节（入库和出库的环节）。

##### 4.jdbc的实现原理：主要作用

1. 可视化工具能操作的步骤以及sql，jdbc同样也可以，支持数据库的增删改查。
2. jdbc专门负责数据库的访问和连接  基本的配置信息：驱动包/登录名/密码/url路径/端口号/ip地址
3. jdbc本身是jar包，jar包中存在了大量的接口。jdbc是一种支持数据库的规范，规定了连接数据库的常用接口。因为不同的数据库厂商，他们的sql需要不同的驱动包，所以只要使用java去操作某一个数据库时，该数据库厂商就必须得提供一套程序，实现jdbc接口。
4. jdbc技术是目前web开发的常用技术，即使在我们学习的后期也会使用（比如 框架，底层还是jdbc）

![](https://note.youdao.com/yws/api/personal/file/D66FB2BCFE6C43D09786E7C320C5B022?method=download&shareKey=9dc2fd3ca33c5f013949a71631118d85)

#### 二、jdbc的入门案例--使用jdbc操作mysql

##### 1.步骤

1. 导入到项目中，相应的mysql的驱动包
2. 使用jdbc配置数据库的连接信息：引入加载驱动程序，url：访问路径 （ip地址，端口号），登陆用户名，登陆密码
3. 建立数据库的链接
4. 对执行的结果进行处理。

##### 2.常用数据库URL地址的写法

Oracle写法：jdbc:oracle:thin:@localhost:1521:sid

SqlServer—jdbc:microsoft:sqlserver://localhost:1433; DatabaseName=sid

MySql—jdbc:mysql://localhost:3306/sid

Mysql的url地址的简写形式： jdbc:mysql:///sid

常用属性：useUnicode=true&characterEncoding=UTF-8

##### 3.程序详解—Connection

*  Jdbc程序中的Connection，它用于代表数据库的链接，Connection是数据库编程中最重要的一个对象，客户端与数据库所有交互都是通过connection对象完成的，这个对象的常用方法：
* createStatement()：创建向数据库发送sql的statement对象。prepareStatement(sql) ：创建向数据库发送预编译sql的PrepareSatement对象。
  *  prepareCall(sql)：创建执行存储过程的callableStatement对象。 
  *  setAutoCommit(boolean autoCommit)：设置事务是否自动提交。 
  *  commit() ：在链接上提交事务。
  *  rollback() ：在此链接上回滚事务。

##### 4.程序详解—Statement

 * Jdbc程序中的Statement对象用于向数据库发送SQL语句， Statement对象常用方法：
   * executeQuery(String sql) ：用于向数据发送查询语句。

   * executeUpdate(String sql)：用于向数据库发送insert、update或delete语句

   * execute(String sql)：用于向数据库发送任意sql语句

   * addBatch(String sql) ：把多条sql语句放到一个批处理中。

   * executeBatch()：向数据库发送一批sql语句执行。 

##### 5.程序详解—ResultSet

* Jdbc程序中的ResultSet用于代表Sql语句的执行结果。Resultset封装执行结果时，采用的类似于表格的方式。ResultSet 对象维护了一个指向表格数据行的游标，初始的时候，游标在第一行之前，调用ResultSet.next() 方法，可以使游标指向具体的数据行，进行调用方法获取该行的数据。
   * ResultSet既然用于封装执行结果的，所以该对象提供的都是用于获取数据的get方法：
   * 获取任意类型的数据
   * getObject(int index)

   * getObject(string columnName)

* 获取指定类型的数据，例如：

  getString(int index)

  getString(String columnName)

* 提问：数据库中列的类型是varchar，获取该列的数据调用什么方法？Int类型呢？bigInt类型呢？Boolean类型？

* ResultSet还提供了对结果集进行滚动的方法：

  * next()：移动到下一行
* Previous()：移动到前一行
  
  * absolute(int row)：移动到指定行
* beforeFirst()：移动resultSet的最前面。
  
  * afterLast() ：移动到resultSet的最后面。

##### 6.程序详解—释放资源

* 为什么要关闭资源?
   * 在安装数据库的时候，设置过最大连接数量，如果用了不还连接，别人就无法使用了。

   * rs对象中可能包含很大的一个数据，对象保存在内存中，这样就十分占用内存。需要将他关闭。

   * 最晚创建的对象，最先关闭。

* Jdbc程序运行完后，切记要释放程序在运行过程中，创建的那些与数据库进行交互的对象，这些对象通常是ResultSet,      Statement和Connection对象。
* 特别是Connection对象，它是非常稀有的资源，用完后必须马上释放，如果Connection不能及时、正确的关闭，极易导致系统宕机。Connection的使用原则是尽量晚创建，尽量早的释放。
* 为确保资源释放代码能运行，资源释放代码也一定要放在finally语句中。

##### 7.释放资源

○ 在关闭过程中可能会出现异常，为了能够关闭资源，需要将资源在finally中关闭。
○ 如果在finally中关闭资源则需要将conn,stat,rs三个对象定义成全局的变量。
○ 在conn,stat,rs三个变量出现异常的时候可能会关闭不成功，我们需要将他们在finally中置为null。conn,stat,rs这三个对象是引用，将引用置为null，它引用的对象就会被JVM回收，也能保证资源的释放。

##### 8.操作步骤

1. 在项目中创建一个lib文件夹，把驱动包复制到项目的lib中 ，右键单击lib选择add as library，加载到项目
2. 创建一个类demo01_jdbc入门.java

```java
package cn.tedu.jdbc;

import com.mysql.jdbc.Driver;

import java.sql.*;

public class Demo01_jdbc入门 {
    public static void main(String[] args) throws SQLException {
        //1.注册驱动程序：也就是引入和加载mysql的驱动包
        /*特别注意：参数指的是：mysql的驱动对象
        *          参数如果是mysql数据库就是new Driver();
        *              如果是oracle数据库就是new OracleDriver();*/
        //DriverManage:驱动管理者，可以支持多个 不同的数据库驱动！！
        DriverManager.registerDriver(new Driver());/*快捷键alt+enter导包*/
        //2.获取一个数据连接：配置基本信息
        String user="root";
        String password="123456";
        /*url使用的是jdbc协议去连接的，连接到某一个database*/
        String url="jdbc:mysql://localhost:3306/javaweb01";
        Connection connection = DriverManager.getConnection(url, user, password);
        //3.编写sql语句，并且先创建一个sql语句的对象
        //创建一个语句对象statement:执行sql语句
        Statement statement = connection.createStatement();
        //4.使用statement对象去执行sql语句
        ResultSet resultSet = statement.executeQuery("select * from teacher ");
        //5.循环遍历结果集
        while(resultSet.next()){//循环的行数
            //循环体是每一行的列的值
            int id = resultSet.getInt(1);//columnindex:列的下标，从1开始
            String t_name = resultSet.getString("t_name");//columnlabel:列名
            System.out.println(id+","+t_name);
        }
        //6.关闭资源：按代码顺序从下往上关
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

#### 三、jdbc执行过程中出现的问题

##### 1、登录访问被拒绝

Exception in thread "main" java.sql.SQLException: Access denied for user 'root'@'localhost' (using password: YES)

解决方案：需要找到配置信息 user变量或者password变量，把他们修改成正确的代码。

##### 2.数据库database不存在

Exception in thread "main" com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown database 'javawe01'

解决方案：找到url路径，然后修改为已存在的database

##### 3、ip地址和端口号写错了

Exception in thread "main" com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure

解决方案：找到url路径，然后修改为正确的端口号3306

##### 4、sql语句的语法不正确

Exception in thread "main" com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'false teacher' at line 1

解决方案：找到sql语句修改为正确的语法格式

##### 5、获取结果集时报错

Exception in thread "main" java.sql.SQLException: Column 't_name1' not found.

解决方案：需要找到while循环体，然后修改为正确的字段

##### 6、资源提前关闭

Exception in thread "main" java.sql.SQLException: Operation not allowed after ResultSet closed

解决方案：需要把关闭资源的代码写到while的外边

#### 四、工具类

- 工具类是为了代码的可重用性设计的，提供公共的方法，一般来说，是静态的方法。

  因为类在加载时会直接加载静态的方法进入到方法区。不需要根据对象的创建去调用，直接根据 类.方法名();调用。

- 把重复的代码提取成一个公用的方法。

  - getConnection()方法

  ```java
  //1.获取连接的方法:返回值是connection
  public static Connection getConnection() {
      Connection connection=null;
      try {
          //1.1注册驱动程序
          DriverManager.registerDriver(new Driver());
          String url="jdbc:mysql://localhost:3306/javaweb01";
          String user="root";
          String password="123456";
         connection= DriverManager.getConnection(url,user,password);
      } catch (SQLException e) {
          e.printStackTrace();
      }
      return connection;
  }
  ```

  - close()方法

  ```java
  //2.关闭连接的方法:参数是结果集，语句对象，数据库连接对象
  public static void close(ResultSet resultSet, Statement statement,Connection connection){
      //2.1先进行非空校验
      if(resultSet!=null){
          try {
              resultSet.close();//close()方法直接有效，比null快速
          } catch (SQLException e) {
              e.printStackTrace();
              resultSet=null;//这里为null时会被jvm虚拟机回收，回收时间不确定
          }
      } 
      if(statement!=null){
          try {
              statement.close();
          } catch (SQLException e) {
              e.printStackTrace();
              statement=null;
          }
      }
      if(connection!=null){
          try {
              connection.close();
          } catch (SQLException e) {
              e.printStackTrace();
              connection=null;
          }
      }
  ```

- 调用工具类的方法：创建一个类：Demo02_jdbc工具类测试

```java
public class Demo02_jdbc工具类测试 {
    public static void main(String[] args)  {
        //1.获取一个数据库连接
        Connection connection = JDBCUtil.getConnection();
        //2.创建一个sql语句的对象
        Statement statement=null;
        try {
            statement = connection.createStatement();
        //执行sql语句
            int rows = statement.executeUpdate("update teacher set t_age=40 where t_id=1");
            System.out.println("受影响的行数"+rows);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally{//最终的代码块：无论sql是否执行成功都需要关闭
            JDBCUtil.close(null,statement,connection);
        }
    }
}
```

- 提取公共资源：提取出用户名，密码，url路径信息

  把这些信息写入到配置文件中即可，以后数据库信息发生变动只需要修改配置文件，不需要修改代码。

  创建一个配置文件一般放在src根目录下 src/jdbc-config.properties

  解释：properties的文本类型：内容写为key=value的键值对

- 提取出一个信息：驱动的类型 com.mysql.jdbc.Driver

  配置文件中维护key，value

  程序中加载驱动时，使用Class.forName();

  等价于：

  Class.forName("com.mysql.jdbc.Driver" , true , this.getClass().getClassLoader())
  Class.forName("oracle.jdbc.driver.OracleDriver" , true , this.getClass().getClassLoader())

  参数一 ："com.mysql.jdbc.Driver" 需要创建对象的类型
  参数二 ：只有 initialize 参数为 true 且以前未被初始化时，才初始化该类
                              true: 以前未被加载过，才初始化该类，
                                    也就是才会创建一个对象，那么这个对象是单例的。
  参数三 ：this.getClass().getClassLoader()类加载器，
                          类加载器负责读取 Java 字节代码(.class文件)，
                          并转换成 java.lang.Class类的一个实例

  配置文件
  
  ```
  driver=com.mysql.jdbc.Driver
  url=jdbc:mysql://localhost:3306/javaweb01
  user=root
  password=123456
  ```
  
  ```Java
  package cn.tedu.jdbc;
  
  import java.io.IOException;
  import com.mysql.jdbc.Driver;
  import java.sql.*;
  import java.util.Properties;
  
  public class JDBCUtil {
      //定义一个properties文本对象，专门负责解析.properties文本
      private static  Properties pros=new Properties();
      //解析的内容通过静态代码块去执行，
      // 因为静态代码块在类加载时就会执行把数据写入到内存中
      static{
          //classLoader(类加载器)：是java的jdk内置的，和类的加载过程有关系
          //类的加载过程：比如JDBCUtil这个类，中的静态方法被加载到内存中，
          // 这个过程就是类加载器去处理完成
          //类加载器是可以通过其他的类也可以获取得到
          try {
              //pros.load()会把输入流加载到pros对象中
              //那么我们就拿到了文本中的key和value
              pros.load(JDBCUtil.class.getClassLoader().getResourceAsStream("jdbc-config.properties"));
              System.out.println(pros);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
      //1.获取连接的方法:返回值是connection
      public static Connection getConnection() {
          Connection connection=null;
  
          try {
              //1.1注册驱动程序
  //            DriverManager.registerDriver(new Driver());
              Class.forName(pros.getProperty("driver"));
  //            DriverManager.registerDriver((Driver) Class.forName(pros.getProperty("driver")).newInstance());
              String url=pros.getProperty("url");
              String user=pros.getProperty("user");
              String password=pros.getProperty("password");
             connection= DriverManager.getConnection(url,user,password);
          } catch (SQLException | ClassNotFoundException e) {
              e.printStackTrace();
          }
  
          return connection;
      }
      //2.关闭连接的方法:参数是结果集，语句对象，数据库连接对象
      public static void close(ResultSet resultSet, Statement statement,Connection connection){
          //2.1先进行非空校验
          if(resultSet!=null){
              try {
                  resultSet.close();//close()方法直接有效，比null快速
              } catch (SQLException e) {
                  e.printStackTrace();
                  resultSet=null;//这里为null时会被jvm虚拟机回收，回收时间不确定
              }
          }
          if(statement!=null){
              try {
                  statement.close();
              } catch (SQLException e) {
                  e.printStackTrace();
                  statement=null;
              }
          }
          if(connection!=null){
              try {
                  connection.close();
              } catch (SQLException e) {
                  e.printStackTrace();
                  connection=null;
              }
          }
      }
  }
  ```

#### 五、数据库的连接池技术

传统的jdbc技术的写法频繁的打开和关闭数据库，性能以及内存受到影响太大，提供一个连接池，把多个connection存入其中，用到时从连接池中取，用完释放到连接池。

##### 1.连接池的实现原理

1. 一个连接池在启动的时候会主动向数据库索要一批连接，
2. 等待用户取出使用，用户去除连接之后，使用完成会将连接归还至连接池中，这个连接可以继续提供连接
3. 如果一时间并发量很大则连接池中会创建很多的连接，等待高并发过后，大量连接无人使用，则这些连接会闲置，此时连接池会在指定的时间内自动销毁一半的连接直到恢复至初始连接数量。

![](https://note.youdao.com/yws/api/personal/file/A02E2602E1C5421A9987CEF62940516C?method=download&shareKey=235531c32952ea20e1f2320639d7b694)

##### 2.dbcp连接池技术

配置文件读取时会默认从src下开始查找文件

创建一个类和一个文件

1. 加载第三方的jar包：lib中添加即可
2. 在类中定义一个数据库连接池对象

```java
public class DBCPUtil {
    //连接池对象
    private static BasicDataSource bds=null;
    //私有化的构造方法，不能new创建对象
    private DBCPUtil(){
    }
    //初始化连接池，也就是把连接池的基本信息获得到
    public static void init(){
        //1.定义一个properties的对象
        Properties pros=new Properties();
        //2.通过流加载dbcp的配置文件
        try {
            pros.load(DBCPUtil.class.getClassLoader().getResourceAsStream("dbcp-config.properties"));
        } catch (IOException e) {
            e.printStackTrace();
        }
        //3.通过pros对象获取参数，配置信息
        try{
            String driver=pros.getProperty("jdbc.driver");
            String url=pros.getProperty("jdbc.url");
            String user=pros.getProperty("jdbc.user");
            String password=pros.getProperty("jdbc.password");
            String initialSize=pros.getProperty("dataSource.initialSize");
            String maxIdle=pros.getProperty("dataSource.maxIdle");
            String minIdle=pros.getProperty("dataSource.minIdle");
            String maxActive=pros.getProperty("dataSource.maxActive");
            String maxWait=pros.getProperty("dataSource.maxWait");
            //4.创建一个连接池对象
            bds=new BasicDataSource();
            //5.把配置信息添加到连接池对象中
            bds.setDriverClassName(driver);
            bds.setUrl(url);
            bds.setUsername(user);
            bds.setPassword(password);
            //如果没有配置连接池的信息则给一个默认的值,有配置信息则把这个值给了
            if(initialSize==null||"".equals(initialSize)){
                bds.setInitialSize(40);
            }else{
                bds.setInitialSize(Integer.parseInt(initialSize));
            }
            if(maxIdle==null||"".equals(maxIdle)){
                bds.setMaxIdle(60);
            }else{
                bds.setInitialSize(Integer.parseInt(maxIdle));
            }
            if(minIdle==null||"".equals(minIdle)){
                bds.setMinIdle(20);
            }else{
                bds.setInitialSize(Integer.parseInt(minIdle));
            }
            if(maxActive==null||"".equals(maxActive)){
                bds.setMaxActive(50);
            }else{
                bds.setInitialSize(Integer.parseInt(maxActive));
            }
            if(maxWait==null||"".equals(maxWait)){
                bds.setMaxWait(1000);
            }else{
                bds.setInitialSize(Integer.parseInt(maxWait));
            }
        }catch(Exception e){
            System.out.println("数据库连接池初始化失败");
        }
    }
    //
    public static Connection getConnection()  {
        if(bds==null){//如果为null需要先初始化信息
            init();//静态方法直接调用，就是上面的方法
        }
        Connection con=null;
        try {
            con=bds.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return con;
    }
    public static void main(String[] args) {
        Connection connection = getConnection();
        System.out.println(connection);
    }
}
```

配置文件（.properties）

```
#1.数据连接的基本信息
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/javaweb01
jdbc.user=root;
jdbc.password=123456

#2.数据库连接池的信息
#初始化连接
dataSource.initialSize=10
#最大空闲连接
dataSource.maxIdle=20
#最小空闲连接
dataSource.minIdle=5
#最大连接数量
dataSource.maxActive=50
#超时等待时间以毫秒为单位6000毫秒/1000等于60秒
dataSource.maxWait=1000
```

总结：dbcp技术在其他的场景中会经常用到，比如tomcat服务器中，对于程序员来说，代码写起来比较繁琐。

##### 3.C3P0的连接池技术

优点：C3P0的性能比DBCP性能更好，c3p0的内置方法会自动加载配置文件，不需要程序员手动写代码了，使用起来更加方便。

```java
public class C3P0Util {
    //1.定义一个成员变量
    //当进行new的对象创建时会自动找到src/c3p0-config.xml
    // 文件不需要进行配置信息初始化
    private static ComboPooledDataSource cpd=new ComboPooledDataSource();
    //2.定义一个Connection连接的方法
    public static Connection getConnection(){
        Connection con=null;
        try {
            con=cpd.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return con;
    }
    //3.定义一个close方法，此方法是把连接释放回连接池
    public static void close(Connection con, Statement sta, ResultSet rs){
        if(con!=null){
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
                con=null;
            }
        }
        if(sta!=null){
            try {
                sta.close();
            } catch (SQLException e) {
                e.printStackTrace();
                sta=null;
            }
        }
        if(rs!=null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
                rs=null;
            }
        }
    }
    public static void main(String[] args) {
        Connection connection = getConnection();
        System.out.println(connection);
    }
}
```

配置文件（.xml  名字必须为c3p0-config）

```xml
<c3p0-config>
    <!--default默认指的是c3p0会直接加载该标签中的内容-->
    <default-config>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/javaweb01</property>
        <property name="user">root</property>
        <property name="password">123456</property>
    </default-config>
</c3p0-config>
```

配置文件2（c3p0.properties）

```
c3p0.driverClass=com.mysql.jdbc.Driver
c3p0.jdbcUrl=jdbc:mysql://localhost:3306/javaweb01
c3p0.user=root
c3p0.password=123456
```

##### 4.连接池技术的实现思路

主要目的：提供多个connection连接，减少内存不断开关的消耗。这些连接时一直在池中存在，用的时候直接拿到。

![](https://note.youdao.com/yws/api/personal/file/72AD4B87CF724755BA70ABF4DF6971F7?method=download&shareKey=0c3d2257ff0ff90d53b34f29710d402c)

#### 六、jdbc核心api

##### 1.statement对象

statement提供了两个方法：

​     executeQuery();//select语句

​    executeUpdate();//insert语句 delete语句  updat语句

statement的这种写法：是把sql语句直接当做参数引入到方法中执行。
很容易出现的问题：

1.sql语句太多时占用的资源较大 ，sql语句执行时，会把sql语句添加到数据库的缓存中。

```
for (int i = 1; i <= 10000 ; i++) {
		sta.executeQuery("select * from teacher where t_id="+i);
	}
```

2.容易被sql注入，把整个sql语句结构改变  sql语句因为是使用变量去拼接字符串

```java
String name="xuning";
String password=" '' or 1=1 ";
String sql = " select * from user where name="+name+"and password="+password;
"select * from user where name='xuning'and password='' or 1=1 "
```

这种写法改变了整个sql语句的结构！！！先and然后or
漏洞：可以使用字符串拼接的方式，绕过登录的验证！！！

3.执行效率低。

```java
public class Demo01_statement {
    public static void main(String[] args)  {
        //1.获取连接：C3P0连接池
        Connection con= C3P0Util.getConnection();
        Statement sta=null;
        ResultSet resultSet1=null;
        //2.创建一个statement的语句对象
        try {
            sta=con.createStatement();
            //3.创建四个语句分别对应CRUD
            String sql1="select * from teacher";
            String sql2="insert into teacher values(null,'测试用户',15)";
            String sql3="update teacher set t_name='11' where t_id=1 ";
            String sql4="delete from teacher where t_id=2";
            //4.执行sql语句
             resultSet1= sta.executeQuery(sql1);
            int row1 = sta.executeUpdate(sql2);
            int row2 = sta.executeUpdate(sql3);
            int row3 = sta.executeUpdate(sql4);
            //

            System.out.println(row1);
            System.out.println(row2);
            System.out.println(row3);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            C3P0Util.close(con,sta,resultSet1);
        }

    }
}
```

##### 2.PreparedStatement对象--预编译语句对象

两者之间的关系：  PreparedStatement implements Statement

写法：预编译对象！！！

 为了防止sql注入，PreparedStatement先规定了一个sql语句的基本结构。

  selete * from user where name=? and password=?

?代表的占位符也就是会进行替换变量的操作。

	    String name="xuning";
		String password=" '' or 1=1 ";

 String sql = " select * from user where name=? and password=? " ;

        ? 被一个字符串替换了 " '' or 1=1 "
        那么，sql语句就会认为这个?对应的是一个  普通的字符串，
        而这个字符串会直接被当做一个【值】传入sql
     	"select * from user where name='xuning' 
     	and password=" '' or 1=1 "
     	这里在查询的时候，就相当于 密码为 '' or 1=1 
     	                而不是 进行 sql语句的拼接了
```Java
public class Demo02_预编译语句对象 {
    public static void main(String[] args) {
        //1.获取数据库连接
        Connection con= C3P0Util.getConnection();
        //2.创建一个预编译语句对象
        PreparedStatement ps=null;
        try {
            ps=con.prepareStatement("select * from teacher where t_id=?");
            //把需要的值赋值给？
            //参数1：是第几个问号， 参数2：问号对应的值
            ps.setInt(1,1);
            //执行sql语句
            ResultSet resultSet = ps.executeQuery();
            while(resultSet.next()){
                String t_name = resultSet.getString("t_name");
                System.out.println(t_name);
            }
            //模拟一个sql注入的场景-->预编译语句对象可以防止sql注入
            String sql="select * from teacher where t_name=?";
            String name="'' or 1=1";
            ps=con.prepareStatement(sql);
            ps.setString(1,name);
            resultSet=ps.executeQuery();
            while (resultSet.next()){
                System.out.println(resultSet.getInt("t_id"));
            }
            //反向验证
            String sql1="select * from teacher where t_name="+name;
            Statement statement = con.createStatement();
            resultSet=statement.executeQuery(sql1);
            while(resultSet.next()){
                System.out.println(resultSet.getString("t_name"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

##### 3.ResultSet结果集---可滚动的结果集

属性：

 Statement stmt = conn.createStatement(type, concurrency);
       PreparedStatement stmt = conn.prepareStatement(sql, type, concurrency);
其中type取值：
TYPE_FORWARD_ONLY：只能向前移动，默认参数
TYPE_SCROLL_INSENSITIVE：可滚动，不感知数据变化
TYPE_SCROLL_SENSITIVE：可滚动，感知数据变化
concurrency取值：
CONCUR_READ_ONLY：只读
CONCUR_UPDATABLE：可更新

方法：next();结果集的光标向下逐行移动

first：指针移动到第一条
last：指针移动到最后一条
beforeFirst：指针移动到第一条之前
afterLast：指针移动到最后一条之后
isFirst：判断指针是否指向第一条
isLast：判断指针是否指向最后一条
isBeforeFirst：判断指针是否在第一条之前
isAfterLast：判断指针是否在最后一条之后
relative：移动到当前指针的相对位置
next：移动到下一条
previous：移动到前一条
absolute：移动到绝对位置

```Java
public class Demo03_ResultSet {
    public static void main(String[] args) {
        Connection connection = C3P0Util.getConnection();
        PreparedStatement ps=null;
        ResultSet rs=null;
        try {
            ps=connection.prepareStatement("select * from teacher",
                    ResultSet.TYPE_FORWARD_ONLY,/*向前查找结果集*/
                    ResultSet.CONCUR_READ_ONLY/*代表只读*/
            );
            rs=ps.executeQuery();
            while(rs.previous()){
                System.out.println(rs.getString("t_name"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            C3P0Util.close(connection,ps,rs);
        }
    }
}
```

#### 七、jdbc的事务

##### 1.事务的概念

事务（Transaction）：数据库中保证交易可靠的机制,若干条sql组成的一组操作要求要么同时完成要么同时不完成，这样的一组操作就是一个事务。

模拟场景：用户A与用户B，发生了一笔交易。A需要给B10元，得到的结果发生两个变化：A的账户减少10元，B的账户增加10元。

```
update account set money=meoney-10 where name='A';
update account set money=meoney+10 where name='B';
```

问题：如果第一条sql语句执行完毕后，在第二条sql语句执行之前，代码出现异常。

比如：

```
update account set money=meoney-10 where name='A';
int i=1/0;
update account set money=meoney+10 where name='B';
```

如果给程序添加了事务管理，那么整个交易过程就是处在一个事务当中，其中一个环节出现异常，其他的代码执行的结果会被还原到交易开始的状态

##### 2.模拟一个事务的场景

使用teacher表，对age字段进行加减的操作，完全模拟account表的操作过程

 完全模拟account的操作过程。

    创建一个类：Demo04_事务.java
    JDBC处理事务的通常模式：
    先将事务的自动提交关闭；
    执行事务中的若干SQL语句；
    事务提交；SQL失败则回滚；
    恢复JDBC的事务提交状态；释放资源。
```java
package gdbc;

import utils.C3P0Util;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class Demo04_事务 {
    public static void main(String[] args) {
        Connection connection = C3P0Util.getConnection();
        PreparedStatement ps=null;
        try {
            //1.先获取事务的自动提交的状态:默认为true
            boolean flag=connection.getAutoCommit();
            //2.手动修改状态为false，也就是禁止自动提交
            connection.setAutoCommit(false);
            //模拟t_id=1的用户  减少10岁
            ps=connection.prepareStatement
                    ("update teacher set t_age=t_age-10 where t_id=1 ");
            ps.executeUpdate();
            //增加一个异常操作
            int i=1/0;
            //模拟t_id=3的用户，增加10岁
            ps=connection.prepareStatement
                    ("update teacher set t_age=t_age+10 where t_id=3 ");
            ps.executeUpdate();
            //3.前提是代码不报错手动提交前面执行的所有的sql语句
            connection.commit();
            //5.最后需要把事务的手动状态恢复成自动状态
            connection.setAutoCommit(flag);
        } catch (Exception e) {
            e.printStackTrace();
            //4.如果执行了代码出现异常，进行回滚操作
            //回滚：所有的交易数据都恢复到最初的状态
            try {
                connection.rollback();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
        }finally {
            C3P0Util.close(connection,ps,null);
        }
    }
}
```

#### 八、批量更新--不允许执行查询操作

##### 1.批处理的优势：

    批处理：发送到数据库作为一个单元执行的一组更新语句。
    批处理降低了应用程序和数据库之间的网络调用，
    相比单个SQL语句的处理，批处理更为有效
    换句话说：使sql执行的效率更高。
模拟场景：10000条数据一次性全部插入到数据库

传统方式：

 for (int i = 0; i < 10000 ; i++) {
        ps = connection.prepareStatement
                            ("update teacher set t_age=t_age+10 where t_id=3 ");
        ps.executeUpdate();
    }

批处理方式：把10000条数据，分成10个批次，也就是一次装入1000条语句，一次性执行完毕，只需要执行10次就可以。

Statement批处理：

addBatch(String sql);//将sql语句添加到批次中

executeBatch();//批量执行100条数据
clearBatch();

##### 2.PreparedStatement批处理的练习：

```Java
public class Demo05_批处理 {
    public static void main(String[] args) {
        Connection con = C3P0Util.getConnection();
        PreparedStatement p = null;
        String sql="insert into teacher values (null,?,?)";
        try {
            p=con.prepareStatement(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            for(int i=0;i<500;i++){
                p.setString(1,"sun"+i);
                p.setInt(2,i);
                p.addBatch();//将每一个数据添加到缓冲区
                if(i%100==0){
                    p.executeBatch();//批量执行100条数据
                    p.clearBatch();//执行完毕之后清空操作
                }
            }
            //把最后一批进行批处理
            p.executeBatch();//批量执行100条数据
            p.clearBatch();//执行完毕之后清空操作
        } catch (Exception e) {
        }
    }
}
```

##### 3.PreparedStatement和Statement的区别

Statement：

优势：可以执行不同语义的sql

劣势：没有预编译功能，执行效率低，同一语义的sql语句，仍然需要完整的发送执行。

PreparedStatement：

优势：有预编译功能；执行效率高，如果执行同一语义的sql语句，效率更高；

劣势：只能执行同一语义的sql语句

#### 九、两表关联

1.两表之间的关系：一对一的关系

一张表：user表基础信息表

一张表：userInfo表详细信息表

模拟场景：用户通过页面维护了  基础信息和详细信息，需要把数据保存到数据库

基本操作：执行了两条sql语句，前提必须要注意：user的id必须要和userInfo一致！！！

问题：

```
insert into user values(null,"张三")；
insert into userInfo values("需要用到上一条的id")；
```

第一条执行的是插入语句：id是新生成的

第二条想要获取这个新生成的id，是不能直接得到的

一般处理方式：插入到user表生成一个自增id；查询新生成的数据获取id，根据查出的结果得到id；最后在插入userInfo表

解决方案：

注意：

1.connection.prepareStatement(sql1, Statement.RETURN_GENERATED_KEYS);

​    Statement.RETURN_GENERATED_KEYS 返回自增主键

2.获取自增主键的值

​            rs=ps.getGeneratedKeys();
​            rs.next();//往下移动一个，指向第一个数据id
​            int id=rs.getInt(1);

```JAVA 
public class Demo06_自增主键获取 {
    public static void main(String[] args) {
        Connection connection = C3P0Util.getConnection();
        PreparedStatement ps=null;
        String sql1="insert into user values(null,?)";
        ResultSet rs=null;
        try {
            ps=connection.prepareStatement(sql1, Statement.RETURN_GENERATED_KEYS);
            ps.setString(1,"er");
            ps.executeUpdate();
            //获取主键的方法
            rs=ps.getGeneratedKeys();
            rs.next();//往下移动一个，指向第一个数据id
            int id=rs.getInt(1);
            System.out.println(id);
            String sql2="insert into user_Info values(?,?,?,?)";
            ps=connection.prepareStatement(sql2);
            ps.setInt(1,id);
            ps.setString(2,"1233");
            ps.setString(3,"110");
            ps.setString(4,"家");
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

#### 十、分页查询

JDBC实现MySQL分页查询

不同数据库获取部分结果集的SQL是有区别的，在MySQL中的实现方式：
 select * from t limit begin，pageSize;
其中：

begin: 从第几条开始显示
pageSize:每页多少条

在Java程序中，MySQL和Oracle分页的方式，仅限于SQL语句不同。

```Java
public class Demo07_分页查询 {
    public static void main(String[] args) {
        Connection con=C3P0Util.getConnection();
        PreparedStatement p=null;
        ResultSet rs=null;
        try {
            p=con.prepareStatement("select * from user limit ? ,? ");
            p.setInt(1,0);/*开始的位置，不包括0*/
            p.setInt(2,5);/*要包含的个数*/
            rs=p.executeQuery();
            while(rs.next()){
                System.out.println(rs.getInt("id"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            C3P0Util.close(con,p,rs);
        }
    }
}
```

![](https://note.youdao.com/yws/api/personal/file/244C11D328B240D8AF2E822178105A4E?method=download&shareKey=3ded0fa96c576995abe4936968d62169)

![](https://note.youdao.com/yws/api/personal/file/7513B8893E76421B9A69163CC0F3286C?method=download&shareKey=444356ab2319147c98f06cda4cf71512)