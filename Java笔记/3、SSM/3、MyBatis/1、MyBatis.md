[TOC]
# MyBatis

## 一、MyBatis概述

#### 1.MyBatis概述

MyBatis是最近几年非常流行的数据访问层(DAO)框架，能够简单高效的实现对数据层访问。

#### 2.常见的数据层访问方式比较

##### 2.1 JDBC

java原生的关系型数据库访问方式

1. 每次操作数据库都需要获取连接关闭连接，在大量访问数据库时，频繁的开关连接消耗性能。
2. 需要手动编写sql，有学习成本
3. 查询出的结果需要手动进行封装到bean
4. 没有缓存处理机制
5. sql语句写死在程序中，需要修改sql必须修改源文件

##### 2.2 Hibernate

基于面向对象理念设计的DAO层框架，基本理念就是维护对象到表的映射关系，通过操作对象操作表中的数据，从而可以减少甚至杜绝sql的使用。

1. 相对比较沉重，效率不好。
2. 当涉及到比较复杂的查询时Hibernate的操作对象的方式用起来非常麻烦，甚至无法实现，只能用sql操作。
3. 底层需要频繁的拼接sql，产生大量冗余的sql。
4. 基于Entitiy（实体）实现。

##### 2.3 MyBatis

是一种半自动对象-表映射关系的DAO层框架，可以自动的进行对象的封装，但是sql仍然需要自己来写。 

结合了JDBC和Hibernate的优点，可以手写sql灵活实现数据访问，自动封装数据，减少冗余代码。

## 二、MyBatis详解

#### 1.MyBatis的结构

![](https://note.youdao.com/yws/api/personal/file/75872C34D7EB4E5191347B91E4C18482?method=download&shareKey=14820498bf383b37956e6d19baae3cd2)

#### 2.入门案例

1. 创建java项目，并在其中导入相关开发包

2. 创建表，创建bean

   创建表

   ```sql
   create database mydb;
   use mydb;
   create table user (
   	id int primary key auto_increment,
   	name varchar(255),
   	age int
   );
   
   insert into user values (null,'aaa',19),(null,'bbb',29),(null,'ccc',39),(null,'ddd',22),(null,'eee',33);
   ```

   创建bean

   ```java 
   package domain;
   
   public class User {
       private int id;
       private String name;
       private int age;
   
       public User() {
       }
   
       public User(int id, String name, int age) {
           this.id = id;
           this.name = name;
           this.age = age;
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public int getAge() {
           return age;
       }
   
       public void setAge(int age) {
           this.age = age;
       }
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   ", age=" + age +
                   '}';
       }
   }
   
   ```

3. 编写配置文件sqlMapConfig.xml，配置数据源

   DTD:简单易用，功能较弱

   SCHEMA:功能强大，复杂难用

   ![](https://note.youdao.com/yws/api/personal/file/BB7C9E82E3B249FDB565B311E1236AEE?method=download&shareKey=a14a2a49c612b3da528fddae10313f0a)

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
   	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
   	"http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
   	<!-- 配置数据源 -->
   	<environments default="mysqldb">
   		<environment id="mysqldb">
   			<transactionManager type="JDBC"/>
   			<dataSource type="POOLED">
   				<property name="driver" value="com.mysql.jdbc.Driver"/>
   				<property name="url" value="jdbc:mysql:///mydb"/>
   				<property name="username" value="root"/>
   				<property name="password" value="root"/>
   			</dataSource>
   		</environment>
   	</environments>
   </configuration>
   
   ```

4. 编写映射文件，描述bean和表sql的映射关系

   注意：要有唯一的名称空间，不能重复。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
   	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="cn.tedu.UserMapper">
   	<select id="select01" resultType="cn.tedu.domain.User">
   		select * from user;
   	</select>
   
   </mapper>
   ```

5. 编写映射文件，描述bean和表sql的映射关系

   ```xml
   <!-- 配置映射文件 -->
   <mappers>
   	<mapper resource="userMapper.xml"/>
   </mappers>
   ```
   
6. 测试类测试

   ```java 
       public static void main(String[] args) throws IOException {
           //1创建SqlSessionFactory
           //Resources---org.apache.ibatis.io.Resources
           InputStream in= Resources.getResourceAsStream("sqlMapConfig.xml");
           SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(in);
           //2.创建SqlSession
           SqlSession sqlSession = sessionFactory.openSession();
           //3.调用sql得到的结果
           List<User> list = sqlSession.selectList("cn.tedu.UserMapper.select01");
           //4.处理结果
           System.out.println(list);
           //5.关闭资源
           sqlSession.close();
       }
   ```

7. 调用过程

   根据输入流找到sqlMapConfig.xml，加载sqlMapConfig.xml然后解析到<mappers>标签，找到对应的映射文件，名称和id定位到sql语句。

   ![](https://note.youdao.com/yws/api/personal/file/995F13CEFE5246DA87C0F465E61BD5A5?method=download&shareKey=8997ff2aa97518000c6e83e55a45edbd)

#### 3.值的传递

##### 3.1 Map传值

可以通过对象 获取 Map传递值，在配置文件中 通过 #{} 或${}进行应用

```xml
 <select id="select01" resultType="domain.User">
    select * from user where id=#{id};
    </select>
```

测试类

```Java
   public static void main(String[] args) throws IOException {
        //1创建SqlSessionFactory
        //Resources---org.apache.ibatis.io.Resources
        InputStream in=Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory=new SqlSessionFactoryBuilder().build(in);
        //2.创建SqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //3.调用sql得到的结果
        Map<String ,Integer> map=new HashMap<>();
        map.put("id",1);
        User user = sqlSession.selectOne("cn.tedu.UserMapper.select01",map);
        //4.处理结果
        System.out.println(user);
        //5.关闭资源
        sqlSession.close();
    }
```

##### 3.2 对象传值

可以通过对象 获取 Map传递值，在配置文件中 通过 #{} 或 ${}进行应用

在UserMapper.xml中配置：

```xml
 <insert id="insert01">
        insert into user values(null,#{name},#{age});
    </insert>
```

==测试类：增删改需要手动提交==

```Java
 /*通过对象进行值传递*/
    @Test
    public void test02(){
        User user=new User(1,"des",45);
        sqlSession.insert("cn.tedu.UserMapper.insert01",user);
        sqlSession.commit();
    }
```

##### 3.3 单值传值

如果程序中只有一个参数需要传递给sql，则不需要封装到bean或map中，可以直接传入。在sql中可以使用任意名称获取到这个参数，虽然名称可以任意，但通常仍然使用该属性的名称，以便于阅读。

```
<select id="select02" resultType="domain.User">
        select * from user where name=#{name};
    </select>
```

测试类，也可以后边直接写“aaa”

```Java
 @Test
    /*通过的单值传值*/
    public void test03(){
        String name="aaa";
        User user = sqlSession.selectOne("cn.tedu.UserMapper.select02", name);
        System.out.println(user);
    }
```

##### 3.4 #{}和${}区别

1. \#{}会作为方法参数使用，替代sql中的？

   有预编译机制，防止sql注入，效率高

   字符串类型会自动两边拼单引号

2. ${}会原样直接拼接到sql上

   没有预编译机制，不能防止sql注入，效率低

   字符串类型会两边不会自动拼单引号

3. 总结

   通常都使用#{},有预编译机制，可以防止sql注入，效率高

   但如果传递的是 表名 列名 关键字 等必须直接拼接到sql中的内容时，使用${}，防止自动拼接单引号改变sql语义

4. 对比案例01

   在UserMapper.xml中配置：

   ```xml
   <select id="select04" resultType="cn.tedu.domain.User">
   	select * from user order by #{cname} desc;
   </select>
   ```
   
测试类：
   
```java 
   /**
    * 	值传递 - #{} ${}的区别
    * 	如果传递的参数是非字符串，两者等效
    * 	如果传递的参数是一个字符串
    * 		#{} 会将值当作一个字符串传入sql,两边会有单引号包裹
    * 		${} 会将值当作一个字符串直接拼接到sql，两边不会有单引号包裹
    * 	例如：
    * 		select * from user order by #{cname} desc;
    * 		传入 age,则sql变为
    * 		select * from user order by 'age' desc;
    * 		
    * 		select * from user order by ${cname} desc;
    * 		传入 age,则sql变为
    * 		select * from user order by age desc;
    */
   @Test
   public void test04() throws Exception {
   	//1.创建SqlSessionFactory
   	InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
   	SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
   	//2.创建SqlSession
   	SqlSession session = factory.openSession();
   	//3.执行sql
   	Map<String,String> map = new HashMap<>();
   	map.put("cname", "age");
   	List<User> list = session.selectList("cn.tedu.UserMapper.select04",map);
   	//4.处理结果
   	System.out.println(list);
   }
   
   ```
   
5. 对比案例02：

   在UserMapper.xml中配置：

   ```xml
   <select id="sel04" resultType="cn.tedu.domain.User">
   	<!-- select * from user order by #{cname} -->
   	 select * from user order by ${cname} 
   </select>
   ```

   测试类：

   ```java 
   /**
    * MyBatis值传递 - #{}和${}的区别
    */
   @Test
   public void test05() throws Exception {
   	//1.调用sql
   	Map<String,String> map = new HashMap<String, String>();
   	map.put("cname", "age");
   	List<User> list = session.selectList("cn.tedu.mapper.UserMapper.sel04",map);
   	//2.打印结果
   	System.out.println(list);
   }
   
   ```

#### 4.MyBatis实现CRUD(增删改查)

##### 4.1 update修改

update修改也可以使用之前的机制在配置文件中直接编写sql

但是update语句 的 set字句中 拼接哪些字段是根据传入的值决定，此时可以通过MyBatis提供的标签 实现判断==动态拼接==update语句：

```xml
  <update id="update01">
        update user
        <set>
            <if test="name !=null">name=#{name},</if>
            <if test="age!=0">age=#{age},</if>
        </set>
        where id=#{id}
    </update>
```

测试类：

```Java
  @Test
    public void test01(){
        User user=new User(1,"a",15);
        sqlSession.update("UserMapper.update01",user);
        sqlSession.commit();
    }
```

##### 4.2 select查询

select查询也可以使用之前的机制在配置文件中直接编写sql

但是select语句 的 where字句中 拼接哪些查询字段 是根据传入的值决定，此时可以通过MyBatis提供的标签 实现判断 动态拼接select语句：

```xml
   <select id="select01" resultType="domain.User">
        select * from user
        <where>
            <if test="name != null">name=#{name}</if>
            <if test="age!=0">and age=#{age}</if>
            <if test="id!=0">and id=#{id}</if>
        </where>
    </select>
```

测试类：

```java 
   @Test
    public void test02(){
        User user=new User(1,null,0);
        User user1 = sqlSession.selectOne("UserMapper.select01", user);
        System.out.println(user1);
    }
```

##### 4.3 insert操作

insert插入也可以使用之前的机制在配置文件中直接编写sql

但是insert语句 的参数和值的列表 拼接哪些字段 是根据传入的值决定，此时可以通过MyBatis提供的标签 实现判断 动态拼接insert语句：

```xml
<insert id="insert01" >
        insert into user
        <trim prefix="(" suffix=")" suffixOverrides=",">
            id,
            <if test="name!=null">name,</if>
            <if test="age!=0">age,</if>
        </trim>
        values
        <trim prefix="(" suffix=")" suffixOverrides=",">
            null,
            <if test="name !=null">#{name},</if>
            <if test="age !=0">#{age},</if>
        </trim>
    </insert>
```

测试类：

```Java
@Test
    public void test03(){
        User user=new User(6,"nbc",16);
        sqlSession.insert("UserMapper.insert01",user);
        sqlSession.commit();
    }
```

##### 4.4 delete删除

delete删除也可以使用之前的机制在配置文件中直接编写sql

但是delete语句的删除条件拼接哪些字段是根据传入的值决定，此时可以通过MyBatis提供的标签 实现判断 动态拼接delete语句：

没有in时：

```xml
<delete id="delete01">
        delete from user
        <where>
            <if test="name!=null">name=#{name}</if>
            <if test="age!=0">and  age=#{age}</if>
            <if test="id!=0">and id=#{id}</if>
        </where>
    </delete>
```

测试类：

```Java
@Test
    public void test04(){
        User user=new User(0,null,16);
        sqlSession.delete("UserMapper.delete01",user);
        sqlSession.commit();
    }
```

有in时：

```xml
<delete id="delete02">
        delete from user where id in
        <foreach collection="list" open="(" close=")" separator="," item="id">
            #{id}
        </foreach>
    </delete>
```

测试类：

```java 
 @Test
    public void test05(){
        List<Integer> list = Arrays.asList(1, 5);
        sqlSession.delete("UserMapper.delete02",list);
        sqlSession.commit();
    }
```

#### 5.手动映射结果集

MyBatis可以自动将查询结果封装到bean中，前提条件是bean的属性名和查询的结果列名相同， 就会依次对应存储。

如果查询结果的列名和bean的属性名不一致，则需要手动映射结果集

```xml
    <resultMap id="rm01" type="domain.User2">
        <id property="uid" column="id"></id>
        <result property="uname" column="name"></result>
        <result property="uage" column="age"></result>
    </resultMap>
    <select id="select02" resultMap="rm01">
        select * from user;
    </select>
```

测试类：

```
@Test
    public void test01(){
        List<User2> list = sqlSession.selectList("UserMapper.select02");
        System.out.println(list);
    }
```

## 三、多表查询

#### 1.多表设计

1. 一对一

   在任意一方设计外键保存另一张表的主键，维系表和表的关系

2. 一对多

   在多的一方设计外键保存一的一方的主键，维系表和表的关系

3. 多对多

   设计一张第三方关系表，存储两张表的主键的对应关系，将一个多对多拆成两个一对多来存储

![](https://note.youdao.com/yws/api/personal/file/A935E17AD3EA4D97BF99438E09036D59?method=download&shareKey=1bc67bf889bfe38d562db7505e6db2f1)

#### 2.多表查询

1. 笛卡尔积查询

2. 内连接查询

   在笛卡尔积的基础上考虑表之前的关系，过滤出正确的数据,查询到正确的数据

   Select * from dept,emp where dept.id=emp.id;

   内连接查询也可以用如下语法实现，原理同上：

   Select * from dept inner join emp on dept.id=emp.did;

   只查询两边都有的结果。

3. 外连接查询

   左外连接查询

   ​	在内连接的基础上增加左边表有而右边表没有的数据。

   右外连接查询

   ​	在内连接的基础上增加右边表而左边表没有的数据。

   全外连接查询---MySQL不支持full join 关键字，会报错

   ​	union：两个表并在一起，重复的数据去掉。

   ​	通过union来模拟全外连接的查询。

![](https://note.youdao.com/yws/api/personal/file/6FD4C94B7D7B4E8AB5C6F560C379E8EF?method=download&shareKey=c2a3ecfbf110221d4742a1f312781bcd)

#### 3.MyBatis中的一对一查询

创建表和bean：

```mysql
create table room(id int primary key,name varchar(255));
insert into room values (1,'梅花屋'),(2,'兰花屋'),(3,'桃花屋');
create table grade(id int primary key,name varchar(255), rid int);

insert into grade values (999,'向日葵班',2),(888,'玫瑰花班',3),(777,'菊花班',1)
```

```Java
package domain;

public class Grade {
    private int id;
    private String name;
    private Room room;

    public Grade(int id, String name, Room room) {
        this.id = id;
        this.name = name;
        this.room = room;
    }

    public Grade() {
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Room getRoom() {
        return room;
    }

    public void setRoom(Room room) {
        this.room = room;
    }

    @Override
    public String toString() {
        return "Grade{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", room=" + room +
                '}';
    }
}

```

```Java
package domain;

public class Room {
    private int id;
    private String name;
    private Grade grade;

    public Room() {
    }

    public Room(int id, String name, Grade grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Grade getGrade() {
        return grade;
    }

    public void setGrade(Grade grade) {
        this.grade = grade;
    }

    @Override
    public String toString() {
        return "Room{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", grade=" + grade +
                '}';
    }
}

```

在通过MyBatis实现一对一的查询时，需要通过resultMap指定如何将结果集中的列名对应到目标bean中，在一对一的bean中，如果包含了另一个表的对应对象，则可以在resultMap中通过association标签来声明映射方式：

##### 3.1.以Grade视角

配置方式

```xml
    <resultMap id="rm01" type="domain.Grade">
        <id property="id" column="gid"></id>
        <result property="name" column="gname"></result>
        <association property="room" javaType="domain.Room">
            <id property="id" column="rid"></id>
            <result property="name" column="rname"></result>
        </association>
    </resultMap>
    <select id="select01" resultMap="rm01">
    select
        grade.name as gname, grade.id gid,room.id as rid,room.name as rname
    from
        grade inner join room on grade.rid=room.id;
    </select>
```

测试类：

```Java
@Test
    public void test01(){
        List<Grade> list = sqlSession.selectList("o2o.select01");
        System.out.println(list);
    }
```

##### 3.2以Room视角

配置方式

```xml
  <resultMap id="rm02" type="domain.Room">
        <id property="id" column="rid"></id>
        <id property="name" column="rname"></id>
        <association property="grade" javaType="domain.Grade">
            <id property="id" column="gid"></id>
            <result property="name" column="gname"></result>
        </association>
    </resultMap>
    <select id="select02" resultMap="rm02">
    select
        grade.name as gname, grade.id gid,room.id as rid,room.name as rname
    from
        grade inner join room on grade.rid=room.id;
    </select>
```

测试类：

```Java
 @Test
    public void test02(){
        List<Grade> list = sqlSession.selectList("o2o.select02");
        System.out.println(list);
    }
```

#### 4.MyBatis中的一对多查询

创建表和bean

```sql
create table dept(id int primary key,name varchar(255));
insert into dept values (1,'财务部'),(2,'行政部'),(3,'人事部'),(4,'销售部');
create table emp(id int primary key,name varchar(255), deptid int);
insert into emp values (999,'孙悟空',4),(888,'萨达姆',3),(777,'哈利波特',1),(666,'特朗普',2),(555,'鑫三胖',3);
```

```java 
package domain;

import java.util.List;

public class Dept {
    private int id;
    private String name;
    private List<Emp> emps;

    public Dept() {
    }

    public Dept(int id, String name, List<Emp> emps) {
        this.id = id;
        this.name = name;
        this.emps = emps;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List<Emp> getEmps() {
        return emps;
    }

    public void setEmps(List<Emp> emps) {
        this.emps = emps;
    }

    @Override
    public String toString() {
        return "Dept{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", emps=" + emps +
                '}';
    }
}

```

```java 
package domain;

public class Emp {
    private int id;
    private String name;
    private Dept dept;

    public Emp() {
    }

    public Emp(int id, String name, Dept dept) {
        this.id = id;
        this.name = name;
        this.dept = dept;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Dept getDept() {
        return dept;
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }

    @Override
    public String toString() {
        return "Emp{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", dept=" + dept +
                '}';
    }
}

```

##### 4.1.方式一：以部门为角度，在部门bean中保存emp的集合

配置方式

```xml
  <resultMap id="rm01" type="domain.Dept">
        <id property="id" column="did"></id>
        <result property="name" column="dname"></result>
        <collection property="emps" ofType="domain.Emp">
            <id property="id" column="eid"></id>
            <result property="name" column="ename"></result>
        </collection>
    </resultMap>
    <select id="select01" resultMap="rm01">
    select
        dept.id as did,dept.name as dname,emp.name as ename,emp.id as eid
    from
        dept inner join emp on dept.id=emp.deptid;
    </select>
```

测试类：

```Java
@Test
    public void test01(){
        List<Dept> list = sqlSession.selectList("o2m.select01");
        System.out.println(list);
    }
```

##### 4.2.方式二：以员工为角度，在员工bean中存储部门bean

在通过MyBatis实现一对多的查询时，通过resultMap指定如何将结果集中的列名对应到目标bean中，在resultMap中通过association标签来声明映射方式：

配置方式：

```xml
 <resultMap id="rm02" type="domain.Emp">
        <id property="id" column="eid"></id>
        <result property="name" column="ename"></result>
        <association property="dept" javaType="domain.Dept">
            <id property="id" column="did"></id>
            <result property="name" column="dname"></result>
        </association>
    </resultMap>
    <select id="select02" resultMap="rm02">
    select
        dept.id as did,dept.name as dname,emp.name as ename,emp.id as eid
    from
        dept inner join emp on dept.id=emp.deptid;
    </select>
```

测试类：

```将java
 @Test
    public void test02(){
        List<Dept> list = sqlSession.selectList("o2m.select02");
        System.out.println(list);
    }
```

#### 5.MyBatis多表查询

创建表和bean：

```sql
create table stu(id int primary key,name varchar(255));
insert into stu values (1,'小新'),(2,'小白'),(3,'美伢'),(4,'风间');
create table teacher(id int primary key,name varchar(255));
insert into teacher values (999,'孙悟空'),(888,'猪八戒'),(777,'萨达姆'),(666,'哈利波特');
create table stu_teacher (sid int,tid int);
insert into stu_teacher values (1,999),(1,888),(2,999),(2,777),(3,666),(4,888),(4,666);

```

```java 
package domain;

import java.util.List;

public class Stu {
    private int id;
    private String name;
    private List<Teacher> teschers;

    public Stu() {
    }

    public Stu(int id, String name, List<Teacher> teschers) {
        this.id = id;
        this.name = name;
        this.teschers = teschers;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List<Teacher> getTeschers() {
        return teschers;
    }

    public void setTeschers(List<Teacher> teschers) {
        this.teschers = teschers;
    }

    @Override
    public String toString() {
        return "Stu{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", teschers=" + teschers +
                '}';
    }
}

```

```java 
package domain;

import java.util.List;

public class Teacher {
    private int id;
    private String name;
    private List<Stu> stus;

    public Teacher() {
    }

    public Teacher(int id, String name, List<Stu> stus) {
        this.id = id;
        this.name = name;
        this.stus = stus;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List<Stu> getStus() {
        return stus;
    }

    public void setStus(List<Stu> stus) {
        this.stus = stus;
    }

    @Override
    public String toString() {
        return "Teacher{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", stus=" + stus +
                '}';
    }
}

```

##### 5.1方式一：以Teacher为角度，在Teacher bean中存储Stu bean的List

配置方式：

```xml
<resultMap id="rm01" type="domain.Teacher">
        <id property="id" column="tid"></id>
        <result property="name"  column="tname"></result>
        <collection property="stus" ofType="domain.Stu">
            <id property="id" column="sid"></id>
            <result property="name" column="sname"></result>
        </collection>
    </resultMap>
    <select id="select01" resultMap="rm01">
         select 
         stu.id as sid,stu.name as sname,teacher.id as tid,teacher.name as tname 
         from 
         stu inner  join stu_teacher on stu.id=stu_teacher.sid 
         inner  join teacher on teacher.id=stu_teacher.tid;
    </select>
```

测试类：

```Java
 @Test
    public void test01(){
        List<Teacher> list = sqlSession.selectList("m2m.select01");
        System.out.println(list);
    }
```

##### 5.2.以Stu为角度，在Stu bean中存储Teacher bean的List

配置方式：   

```xml
   <resultMap id="rm02" type="domain.Stu">
        <id property="id" column="sid"></id>
        <result property="name" column="sname"></result>
        <collection property="ts" ofType="domain.Teacher">
            <id property="id" column="tid"></id>
            <result property="name" column="tname"></result>
        </collection>
    </resultMap>
    <select id="select02" resultMap="rm02">
         select
         stu.id as sid,stu.name as sname,teacher.id as tid,teacher.name as tname
         from
         stu inner  join stu_teacher on stu.id=stu_teacher.sid
         inner  join teacher on teacher.id=stu_teacher.tid;
    </select>

```

  测试类：

```Java
 @Test
    public void test02(){
        List<Teacher> list = sqlSession.selectList("m2m.select02");
        System.out.println(list);
    }
```

##  四、MyBatis的其他细节

#### 1.别名标签

如果在映射文件中，大量使用类名比较长，可以在sqlMapConfig.xml声明别名，在映射文件中可以使用别名缩短配置，注意此配置要放在最前面

sqlMapConfig.xml中：

```xml
<!--别名标签-->
    <typeAliases>
        <typeAlias type="domain.User" alias="user"></typeAlias>
    </typeAliases>
```

映射文件中：

```xml
<select id="select01" resultType="user">
    select * from user where id=3;
</select>
```

#### 2.sql的复用

如果某段sql语句的片段在映射文件中重复出现，可以将其单独配置为一个引用，从而在需要时直接引用，减少配置量。

```xml
<mapper namespace="UserMap">
    <sql id="sql1">
        select * from user
    </sql>
<select id="select01" resultType="user">
    <include refid="sql1"/> where id=3;
</select>
```

#### 3.MyBatis的缓存机制

缓存机制可以减轻数据库的压力，原理是在第一次查询时，将查询结果缓存起来，之后再查询同样的sql，不是真的去查询数据库，而是直接返回缓存中的结果。

缓存可以降低数据库的压力，但同时可能无法得到最新的结果数据。

1. 数据库缓存的实现:

   通过第三方工具实现缓存：

   Redis内存数据库 - 可以实现缓存

   通过MyBatis提供的缓存机制来实现缓存：

   一级缓存：

   缓存==只在一个事务中有效==，即==同一个事务中先后执行多次同一个查询，只在第一次真正去查库，并将结果缓存==，之后的查询都直接获取缓存中的中数据。如果是不同的事务，则缓存是隔离的。

   二级缓存：

   缓存==在全局有效==，一个事务查询一个sql得到结果，会被缓存起来，之后只要缓存未被清除，则其他事务如果查询同一个sql，得到的将会是之前缓存的结果。二级缓存作用范围大，作用时间长，可能造成的危害也更大，所以在开发中一般很少启用Mybatis的二级缓存。

2. MyBatis的一级缓存

   MyBatis的一级缓存默认就是开启状态，且无法手动关闭。
   
   ```java 
      @Test
       public void test01(){
           User user1 = sqlSession.selectOne("UserMap.select01");
           System.out.println(user1);
           User user2 = sqlSession.selectOne("UserMap.select01");
           System.out.println(user2);
       }
   ```
   
3. MyBatis的二级缓存

   MyBatis的二级缓存默认是关闭的

   ```JAVA 
   public static void main(String[] args) throws IOException {
      
       SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("sqlMapConfig.xml"));
       SqlSession sqlSession1  = sqlSessionFactory.openSession();
       User user1 = sqlSession1.selectOne("UserMap.select01");
       System.out.println(user1);
       sqlSession1.commit();
       SqlSession sqlSession2  = sqlSessionFactory.openSession();
       User user2 = sqlSession1.selectOne("UserMap.select01");
       System.out.println(user1);
       sqlSession1.commit();
   }
   ```

   可以配置选项开启二级缓存：

   在sqlMapConfig.xml配置：（别名标签，二级缓存开启的配置都要放在配置数据源的上边）

   ```xml
    <!--开启二级缓存-->
       <settings>
           <setting name="cacheEnabled" value="true"/>
       </settings>
   ```

   在映射文件中配置：

   ```xml
   <cache/>
   ```

   想要被二级缓存缓存的bean必须实现序列化接口：

   ```
   public class User implements Serializable {
   }
   ```

   测试类：

   测试时不要忘了提交事务，只有在事务提交时，才会将对象存入二级缓存。

   ```Java
     public static void main(String[] args) throws IOException {
   
           SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("sqlMapConfig.xml"));
           SqlSession sqlSession1  = sqlSessionFactory.openSession();
           User user1 = sqlSession1.selectOne("UserMap.select01");
           System.out.println(user1);
           sqlSession1.commit();
           SqlSession sqlSession2  = sqlSessionFactory.openSession();
           User user2 = sqlSession1.selectOne("UserMap.select01");
           System.out.println(user1);
           sqlSession1.commit();
       }
   ```

## 五、==接口的使用==

为了简化MyBatis的使用，MyBatis提供了接口方式自动化生成调用过程的机制，可以大大简化MyBatis的开发

实现过程：

1. 开发映射文件：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="mappers.UserMap">
   
       <select id="select01" resultType="domain.User">
           select * from user;
       </select>
   </mapper>
   ```

2. 开发接口：

   | 接口的全路径名应为映射文件中声明的名称空间          |
   | --------------------------------------------------- |
   | 接口中应该声明和映射文件中sql对应的id相同名称的方法 |
   | 方法接收的参数应该和sql中接收的参数一致             |
   | 方法的返回值应该和sql中声明的返回值类型一致         |
   
   ```Java
   import domain.User;
   import org.apache.ibatis.annotations.Param;
   import java.util.List;   
   /*无参*/  
   public List<User> select01();
   /*
   	* 有参传递方式：可以用注解@Param("min")代表传到#{min}的位置
     * 也可以用map进行传值，此时不需要用注解。
   */
   public List<User> select02(@Param("min") int min,@Param("max") int max);
   ```

3. 开发测试类：

   ```java 
    @Test
   public void test01() throws IOException {
   	SqlSessionFactory sqlSessionFactory=new 	SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("sqlMapConfig.xml"));
   	//第一次事务
     SqlSession sqlSession = sqlSessionFactory.openSession();
     //通过接口获取映射对象
     UserMap usermap=sqlSession.getMapper(UserMap.class);
     //通过映射对象调用方法然后找到对应的sql语句
     List<User> users = usermap.select01();
     System.out.println(users);
     List<User> users1 = usermap.select02(20, 40);
     System.out.println(users1);
   }
   ```

4. 真正的开发中，都是使用这种接口+配置文件方式，实现MyBatis的使用。

实现原理：

![](https://note.youdao.com/yws/api/personal/file/5B69203169B74D0084DD1B3FD5EDA40B?method=download&shareKey=faad58f875ff5941b6af11056a6be4e9)



