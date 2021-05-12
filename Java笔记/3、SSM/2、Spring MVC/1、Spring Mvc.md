[TOC]
# Spring MVC

## 一、Spring MVC概述

#### 1.Spring MVC概述

SpringMVC是一个WEB层、控制层框架,主要用来负责与客户端交互,业务逻辑的调用.

SpringMVC是Spring家族的一大组件.Spring整合SpringMVC可以做到无缝集成.

特点 简单易用性能佳。

#### 2.为什么有了Servlet还要学Spring MVC

1. Servlet的开发配置相对麻烦，servlet特别多的时候web.xml文件将会非常臃肿
2. 每个Servlet都只能处理一个功能，如果需要多个功能就需要开发多个Servlet，项目中存在大量Servlet显得臃肿。
3. 获取请求参数 进行类型转换 封装数据到bean的过程比较繁琐。
4. 其他开发中不方便的地方，例如，乱码问题..数据格式处理..表单校验..

## 二、Spring MVC详解

1.SpringMVC的组件

* 前端控制器（DispatcherServlet）--唯一的Servlet

  本质上是一个Servlet，相当于一个中转站，所有的访问都会走到这个Servlet中，再根据配置进行中转到相应的Handler中进行处理,获取到数据和视图后，在使用相应视图做出响应。

* 处理器映射器(HandlerMapping)

  本质上就是一段映射关系，将访问路径和对应的Handler存储为映射关系，在需要时供前端控制器查阅。

* 处理器适配器(HandlerAdapter)

  本质上是一个适配器，可以根据要求找到对应的Handler来运行。前端控制器通过处理器映射器找到对应的Handler信息之后，将请求响应和对应的Handler信息交由处理器适配器处理，处理器适配器找到真正handler执行后，将结果即model和view返回给前端控制器

* 视图解析器(ViewResolver)

  本质上也是一种映射关系，可以将视图名称映射到真正的视图地址。前端控制器调用处理器适配完成后得到model和view，将view信息传给视图解析器得到真正的view。

* 视图(View)

  本质上就是将handler处理器中返回的model数据嵌入到视图解析器解析后得到的jsp页面中，向客户端做出响应

* 图解

  ![](https://note.youdao.com/yws/api/personal/file/9A771418A11C455A94CA4B1837A999FC?method=download&shareKey=e34c48d766f25427cbdb93b637e31818)

## 三、Spring MVC入门案例

#### 1.配置文件方式

1. 创建web项目

2. 导入SpringMVC相关开发包

   ![](https://note.youdao.com/yws/api/personal/file/9C83FC700403498BAA05FEF54E7B82BA?method=download&shareKey=ea906e3a16117abbcfe64073c69c744c)

3. 配置前端控制器

   本质上是一个servlet，在当前web项目中配置该servlet

   ```xml
    <!--配置前端控制器-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       </servlet>
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>*.action</url-pattern>
       </servlet-mapping>
   ```

4. 生成SpringMVC的核心配置文件

   SpringMVC默认会自动在web应用的WEB-INF目录下去寻找[前端控制器ServletName]-servlet.xml作为当前SpringMVC的核心配置文件。

   创建这个文件，这个文件本身其实就是Spring的配置文件，所以导入Spring相关的约束信息，包括 beans、context、mvc

   ```xml
   xmlns="http://www.springframework.org/schema/beans"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/aop
          http://www.springframework.org/schema/aop/spring-aop.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          http://www.springframework.org/schema/mvc/spring-mvc.xsd
   ```

5. 创建处理器，编写代码

   想要开发一个处理器，写一个类实现Controller接口，在其中的handlerRequest中编写代码处理请求，并将处理好的数据和目标视图封装到ModelAndView中返回

   ```
   public class FirstController implements Controller {
       @Override
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception{
           ModelAndView mac=new ModelAndView();
           mac.addObject("k1","v1");
           mac.addObject("k2","v2");
           mac.addObject("k3","v3");
           mac.setViewName("first");
           return mac;
       }
   }
   ```

6. 配置处理器映射器中的路径和处理器的映射关系

   ```
   <!--配置处理器映射器-->
       <bean name="first.action" class="controller.FirstController"></bean>
   ```

7. 配置视图解析器

   ```
   <!--配置视图解析器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"></property>
           <property name="suffix" value=".jsp"></property>
       </bean>
   ```

8. 开发视图

   ```jsp
   <%--
     Created by IntelliJ IDEA.
     User: tedu
     Date: 2020/1/3
     Time: 10:01
     To change this template use File | Settings | File Templates.
   --%>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
       first.jsp....
       ${k1}
       ${k2}
       ${k3}
   </body>
   </html>
   
   ```

9. 发布应用，通过浏览器访问

10. 整体流程

    ![](https://note.youdao.com/yws/api/personal/file/7D20DF7CAD4B4F2D9C5A73B3FED10D74?method=download&shareKey=0fd7c19f513a6c8405a2a23e0b9e13ae)

#### 2.修改SpringMVC核心配置文件的位置

SpringMVC默认在和web.xml相同的位置即WEB-INF目录下寻找核心配置文件,文件名默认[前端控制器Servlet-name]-servlet.xml如果需要，可以通过配置，手动指定核心配置文件的位置，和文件的名称：

```xml
<init-param>
     <param-name>contextConfigLocation</param-name>
     <param-value>classpath:springmvc.xml</param-value>
 </init-param>
```

#### 3.SpringMVC的注解方式配置

SpringMVC支持使用注解方式配置，比配置文件方式更加的灵活易用，是SpringMVC使用的主流模式。

1. 在配置文件中开启SpringMVC的注解模式

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <!--配置前端控制器-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc.xml</param-value>
           </init-param>
       </servlet>
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>*.action</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:contetx="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          http://www.springframework.org/schema/mvc/spring-mvc.xsd
   ">
   
       <contetx:component-scan base-package="controller"></contetx:component-scan>//开启包扫描
       <mvc:annotation-driven/>//开启注解方式mvc
       <!--配置视图解析器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"></property>
           <property name="suffix" value=".jsp"></property>
       </bean>
   </beans>
   ```

2. 使用注解开发Controller

   ```Java
   @Controller
   public class FirstController {
       @RequestMapping("test01.action")
       public String  test01(Model model){
           model.addAttribute("k1","v1");
           model.addAttribute("k2","v2");
           model.addAttribute("k3","v3");
           return "test01";
       }
   }
   ```

3. 发布应用，通过浏览器进行访问

4. 整体流程

   ![](https://note.youdao.com/yws/api/personal/file/AFFD0CA448504763AEBDE561DEA5FB75?method=download&shareKey=9a99928a1068c2a03198b74bee6002a2)

5. .SpringMVC注解方式工作原理

   i.当服务器启动时,先会加载web.xml,之后通过引入核心配置文件加载springmvcservlet.xml.就会解析该xml配置文件

   ii. 当解析到包扫描时，扫描指定的包，并将含有@Controller注解的类解析为处理器

   iii. 如果配置开启注解方式MVC，就会解析Spring-MVC注解,解析@RequestMapping(value="/hello.action")，将指定的地址和当前方法的映射关系进行保存

   iv.当用户发出请求访问一个地址时，SpringMVC寻找该地址映射关系，如果存在，则找到相应处理器相应方法执行，如果找不到，则报404。

## 四、SpringMVC细节

#### 1.@RequestMapping

1. 基本使用

   通过注解方式实现路径到处理器方法的映射。

   可以用在类或方法上。

   用在方法上表示将该方法变为一个处理器，且和指定路径做映射。

   用在类上则配置的路径会作为这个类中所有处理器的路径的父路径使用。

   ```Java
     /**
        * @RequestMapping的基本使用
        *      用在方法上 - 将当前方法转为一个控制器方法，绑定到指定路径
        *      用在类上 - 作为当前类中所有控制器方法路径的父路径
        * http://localhost/SpringMVCDay01_03_RequestMapping/my01/test01.action
        */
       @RequestMapping("/test01.action")
       public void test01(){
           System.out.println("test01...");
       }
   ```

2. 注解属性

   i. String [] value() default {};---可以配置多个值，将多个路径绑定到当前方法。

   ​	指定要将当前处理器绑定到哪个访问路径上。

   ​	可以配置多个路径。

   ​	路径中也可以使用*号作为通配符匹配部分路径。

   ```Java
      /**
        * @RequestMapping的value属性
        *      绑定路径到控制器方法
        *      可以配置多个值，将多个路径绑定到当前方法
        *      可以在路径中使用*号作为通配符
        * http://localhost/SpringMVCDay01_03_RequestMapping/my01/test02.action
        * http://localhost/SpringMVCDay01_03_RequestMapping/my01/test02x.action
        * http://localhost/SpringMVCDay01_03_RequestMapping/my01/test02zabcdefg.action
        */
       @RequestMapping(value={"/test02.action","/test02x.action","/test02z*.action"})
    public void test02(){
           System.out.println("test02...");
    }
   ```

   ii. RequestMethod[] method() default {};	

   ​	---限定允许/不允许那种请求方式的请求访问此方法，如果不做限制所有请求方式都可以访问此控制器方法。如果配置method属性限定接受那些指定方式的请求。
   
   ​	指定当前处理器处理哪种提交方式提交的请求。
   
   ```java
   /**
        *   @RequestMapping的method属性
        *   限定只允许哪种请求方式的请求访问此方法
        *   如果不做配置，所有请求方式都可以访问此控制器方法
        *   可以通过配置method属性限定哪此控制器方法只接受哪些中方式的请求
        *   http://localhost/SpringMVCDay01_03_RequestMapping/test03.jsp
    */
   @RequestMapping(value="/test03.action",method=RequestMethod.GET)
   public void test03(){
   	System.out.println("test03...");
   }
   ```
   
   iii.String[] params() default {};
   
   要求请求参数必须符合指定要求的才能访问此控制器方法。
   
      	 格式1：只指定名称，要求必须具有改名称的请求参数
   
      	 格式2：以"名称=值"或"名称!=值"的方式指定必须具有某个请求参数，且值必须等于或不等于 	 给定值
   
   ​		格式3:以"!名称"的方式指定必须不包含指定名称的请求参数
   
   ```java 
   /**
           * @RequestMapping的params属性
           *  要求请求参数必须符合指定要求才能访问此控制器方法
           *  格式1:只指定名称，要求必须具有该名称的请求参数
           *  格式2:以"名称=值"或"名称!=值"的方式指定必须具有某个请求参数，且值必须等于或不等于给定值
           *  格式3:以"!名称"的方式指定必须不包含指定名称的请求参数
           *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test04.action
           *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test04.action?uname=zs
           *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test04.action?uname=zs&gender=male
           *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test04.action?uname=zs&gender=male&country=cn
           *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test04.action?uname=zs&gender=male&country=us
           *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test04.action?uname=zs&gender=male&country=us&age=99
   */
   @RequestMapping(value="/test04.action",params={"uname","gender=male","country!=cn","!age"})
   public void test04(){
   	System.out.println("test04...");
   }
   ```
   
   iv.String[] headers() default {};
   
   ​		通过headers属性指定只处理请求头符合指定要求的请求
   
      ​	 格式1:只指定名称，要求必须具有该名称的请求头
   
      ​	 格式2:以"名称=值"或"名称!=值"的方式指定必须具有某个请求头，且值必须等于或不等于给定值
   
      ​	 格式3:以"!名称"的方式指定必须不包含指定名称的请求头
   
   ```Java
   /**
       * @RequestMapping的headers属性
       *  要求请求头必须符合指定要求才能访问此控制器方法
       *  格式1:只指定头名称，要求必须具有该名称的请求头
       *  格式2:以"头名称=值"或"名称!=值"的方式指定必须具有某个请求头，且值必须等于或不等于给定值
       *  格式3:以"!头名称"的方式指定必须不包含指定名称的请求头
       *  http://localhost/SpringMVCDay01_03_RequestMapping/my01/test05.action
   */
      @RequestMapping(value="/test05.action",headers={"Cookie","!Refresh","Connection=keep-alive","Host!=localhost"})
      public void test05(){
          System.out.println("test05..");
   }
   ```

#### 2.获取Request Response对象

在SpringMVC的注解开发中，可以选择性的接收Request和Response对象来使用，在传入的参数直接获取一个HttpServletRequest即可。想要啥就写啥。

#### 3.获取请求参数

##### 3.1 通过request对象获取

```Java
 /**
     * 获取请求参数 - 通过request对象获取请求参数
     * http://localhost/SpringMVCDay01_04_Parameter/my01/test01.action?uname=zs&uage=19
     */
    @RequestMapping("/test01.action")
    public void test01(HttpServletRequest request){
        String  uname = request.getParameter("uname");
        int uage = Integer.parseInt(request.getParameter("uage"));
        System.out.println(uname);
        System.out.println(uage);
    }
```

##### 3.2 直接接收请求参数

可以在Controller方法中直接接收请求参数相同名称的方法形参，可以直接得到请求参数的值

```Java

    /**
     * 获取请求参数 - 直接获取
     *      要求方法参数名必须和请求参数名对应
     * http://localhost/SpringMVCDay01_04_Parameter/my01/test02.action?uname=zs&uage=19
     */
    @RequestMapping("/test02.action")
    public void test02(String uname,int uage){
        System.out.println(uname);
        System.out.println(uage);
    }
```

##### 3.3 请求参数中的名称和属性名不同的处理@RequestParam

可以通过@RequestParam来修饰Controller方法中用来接收请求参数的形参，有如下属性可以配置：

| value        | 参数名字，即入参的请求参数名字，如value=“delId”表示请求的参数区中的名字为delId的参数的值将传入 |
| ------------ | ------------------------------------------------------------ |
| required     | 是否必须，默认是true，表示请求中一定要有相应的参数，否则将报400错误码； |
| defaultValue | 默认值，表示如果请求中没有同名参数时的默认值                 |

value来指定 将那个请求参数赋值给当前形参

将required声明为true，则请求参数中必须有该属性，如果没有客户端将受到400

defaultValue可以设定当前形参的默认值

```Java
   /**
     * 获取请求参数 - 直接获取 - 方法参数名和请求参数名不一致
     *      可以通过@RequestParam手工指定方法参数到请求参数的映射关系
     * http://localhost/SpringMVCDay01_04_Parameter/my01/test03.action?uname=zs&uage=19
     */
    @RequestMapping("/test03.action")
    public void test03(@RequestParam("uname") String name,@RequestParam("uage") int age){
        System.out.println(name);
        System.out.println(age);
    }

```

##### 3.4 自动封装请求参数信息到bean

SpringMVC框架可以自动将请求参数封装到bean中，要求bean中必须提供属性的setXxx方法，且bean的属性名和请求参数中请求参数的名字必须一致，才可以自动设置。

```Java
package domain;

public class User {
    private String name;
    private int age;

    public User() {
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
                "name='" + name + '\'' +
                ", age=" + age 
                '}';
    }
}
```

```Java
    /**
     * 获取请求参数 - 自动封装数据到bean
     *  要求请求参数名和bean的属性名对应
     *  http://localhost/SpringMVCDay01_04_Parameter/my01/test04.action?uname=zs&uage=19&uaddr=bj
     */
    @RequestMapping("/test04.action")
    public void test04(User user){
        System.out.println(user);
    }

```

##### 3.5 处理复杂类型

如果自动封装的bean中存在复杂类型，只要该复杂类型的属性同样具有setXxx方法，则可以在请求参数中包含[bean中复杂类型].[属性]的方式为该复杂类型的参数复制，从而实现自动封装bean的过程中处理其中的复杂类型

```Java
package domain;

public class User2 {
    private int age;
    private String name;
    private Dog dog;

    public User2() {
    }

    public User2(int age, String name, Dog dog) {
        this.age = age;
        this.name = name;
        this.dog = dog;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "User2{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", dog=" + dog +
                '}';
    }

    public void setName(String name) {
        this.name = name;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }
}

```

```Java
package cn.tedu.domain;
public class Dog {
    private String name;
    private int age;
    public Dog() {
    }
    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
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
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```Java
  /**
     * 获取请求参数 - 自动封装数据到bean - 处理复杂类型
     *      可以通过点号设定自定义bean类型中带有复杂类型的数据
     * http://localhost/SpringMVCDay01_04_Parameter/my01/test05.action?uname=zs&uage=19&uaddr=bj&dog.name=wc&dog.age=5
     */
    @RequestMapping("/test05.action")
    public void test05(User2 user2){
        System.out.println(user2);
    }
```

##### 3.6 请求参数中存在多个同名参数

如果请求参数中存在多个同名值

如果用String接收，此时直接获取，会得到一个用逗号分隔的字符

```Java
 @RequestMapping("test6.action")
    public void test6(String name ,int age){
        System.out.println(name+"\t"+age);
    }
```

如果用String[]数组接收，则直接接收到一个数组

```Java
    /**
     * 获取请求参数 - 处理多个同名请求参数
     *      如果用String接收，则传入多个值用逗号连接后的字符串
     *      如果用Stirng数组接收，则传入多个值组成的数组
     *      http://localhost/SpringMVCDay01_04_Parameter/my01/test06.action?like=zq&like=lq&like=ppq
     */
    @RequestMapping("/test06.action")
    //public void test06(String like){
    public void test06(String [] like){
        System.out.println(Arrays.asList(like));
    }
```

##### 3.7 请求参数中的中文乱码

SpringMVC提供了过滤器用来解决全站乱码

 配置文件：(配置在web.xml文件中)

```xml
    <!--配置SpringMVC乱码解决过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

页面代码编写：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>first.jsp</title>
</head>
<body>
<form action="test7.action" method="post">
    name:<input type="text" name="name" />
    age:<input type="text" name="age" />
    <input type="submit">
</form>
</body>
</html>
```

测试类：

```Java

    /**
     * 获取请求参数 - 中文乱码处理
     *      springmvc内置了乱码解决过滤器，简单配置后可以用来解决请求参数乱码
     *      但这个过滤器只能解决POST提交乱码，对GET提交请求参数乱码无效
     * http://localhost/SpringMVCDay01_04_Parameter/my01test07.jsp
     */
    @RequestMapping("/test07.action")
    public void test07(String username,String addr) throws UnsupportedEncodingException {
        System.out.println(username);
        System.out.println(addr);
    }

```

这种方式只能解决POST提交的乱码，对GET方式提交的乱码无效！此时只能手动进行编解码解决GET方式请求参数乱码问题

```
name=new String(name.getBytes("ISO8859-1"),"UTF-8");
```

也可以直接修改Tomcat中连接器的配置来使tomcat默认采用指定编码处理请求参数，但这种方式不建议大家使用，因为生产环境下不一定允许修改此项

![](https://note.youdao.com/yws/api/personal/file/9AADCDFD7AE742C39022FB213CE3D277?method=download&shareKey=74ce9a78d1ef3f4fdc2e5590a42af9be)

##### 3.8 日期数据的处理

在SpringMVC中解析页面提交的请求参数时，默认按照yyyy/MM/dd的格式解析数据的,并不符合Data类默认处理机制，所以无法自动获取封装日期到Data。此时可以配置适配器自己来指定格式

页面代码：

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>my01test08.jsp</title>
</head>
<body>
    <form action="${pageContext.request.contextPath}/my01/test08.action" method="post">
        用户名:<input type="text" name="username"/>
        出生日期:<input type="date" name="birthday"/>
        <input type="submit"/>
    </form>
</body>
</html>
```

测试类：

```Java
   /**
     * 获取请求参数 - 日期数据处理
     * http://localhost/SpringMVCDay01_04_Parameter/my01test08.jsp
     */
    @InitBinder
    public void myDateInitBinder(ServletRequestDataBinder binder){
        binder.registerCustomEditor(Date.class,new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"),true));
    }
    @RequestMapping("/test08.action")
    public void test08(String username,Date birthday){
        System.out.println(username);
        System.out.println(birthday);
    }
```

##### 3.9 SpringMVC文件上传

i. 准备文件上传表单

文件上传表单必须满足如下三个条件

a) 表单==必须是Post提交==

b) 文件上传项==必须有name属性==

c) 表单==必须是enctype="multipart/form-data"==

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>test9</title>
</head>
<body>
<form action="${pageContext.request.contextPath}/test9.action" method="post" enctype="multipart/form-data">
    <input type="file" name="fx">
    <input type="submit">
</form>
</body>
</html>
```

ii. 在配置文件springmvc中配置文件上传工具

```xml
 <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">

    </bean>
```

![](https://note.youdao.com/yws/api/personal/file/B47D7C54FC1C4AACA1362B12199C4D14?method=download&shareKey=3be3ad8a7f83d3bde077a79b6e8c2e20)

iii. 在Controller中实现文件上传

```Java
  /**
     * 获取请求参数 - 实现文件上传
     *      开发文件上传表单
     *      开发文件上传代码
     * http://localhost/SpringMVCDay01_04_Parameter/my01test09.jsp
     */
    @RequestMapping("/test09.action")
    public void test09(MultipartFile fx) throws IOException {
        fx.transferTo(new File("C://"+fx.getOriginalFilename()));
    }
```

##### 3.10 路径动态数据的获取(RESTFul风格的请求参数处理)

i. RESTFul风格的请求：

​	普通get请求：

```
Url:localhost/XXXX/addUser.action?name=tom&age=18
```

​	RESTFul风格的请求：

```
Url:localhost/XXXX/tom/18/addUser.action
```

ii. SpringMVC对RESTFul风格的请求的处理：

@PathVariable获取请求路径中的参数

```java 
   /**
     * 获取请求参数 - 获取路径中的数据(RESTFUL风格编程支持)
     * http://localhost/SpringMVCDay01_04_Parameter/my01/zs/bj/test10.action
     */
    @RequestMapping("/{uname}/{uaddr}/test10.action")
    public void test10(@PathVariable("uname") String uname,@PathVariable("uaddr") String uaddr){
        System.out.println(uname);
        System.out.println(uaddr);
    }
```

#### 4.SpringMVC中的重定向和转发的实现

##### 4.1 请求转发、重定向、定时刷新的区别

请求重定向、请求转发、定时刷新都是web开发中资源跳转的方式。

```
转发：
	请求转发是服务器内部的跳转
	地址栏不发生变化
	只有一个请求响应
	可以通过request域传递数据
重定向：
	请求重定向是浏览器自动发起对跳转目标的请求
	地址栏会发生变化
	两次请求响应
	无法通过request域传递对象
定时刷新:
	定时刷新是浏览器自动发起对跳转目标的请求
	地址栏会发生变化
	两次请求响应
	无法通过request域传递对象
	可以在资源跳转期间提示额外信息
```

##### 4.2 SpringMVC中实现转发

i. 在SpringMVC中仍然可以使用传统方式实现转发和重定向

```Java
/**
 * 请求转发 - 传统方式
 */
@RequestMapping("/test01.action")
public void test01(HttpServletRequest request, HttpServletResponse response) 
	throws ServletException, IOException {
    request.getRequestDispatcher("/index.jsp").forward(request,response);
}
```

ii. 在SpringMVC中也提供了快捷方法实现转发和重定向

​	只要在返回视图时，使用如下方式指定即可：

```Java
/**
 * 请求转发 - springMVC方式
 */
@RequestMapping("/test02.action")
public String test02(){
    return "forward:/index.jsp";

}
```

##### 4.3 SpringMVC中实现请求重定向

i. 传统方式实现请求重定向

```Java
/**
 * 请求重定向 - 传统方式
 */
@RequestMapping("/test01.action")
public void test01(HttpServletRequest request,HttpServletResponse response) throws IOException {
    response.sendRedirect(request.getContextPath()+"/index.jsp");
}

```

ii. SpringMVC方式实现请求重定向

可以通过返回redirect:/xxxx.xxx格式的字符串表明要重定向到指定地址

通过这种方式实现请求重定向时不用在路径前写应用名，SpringMVC会自动拼接应用名

```Java
/**
 * 请求重定向 - SpringMVC方式
 */
@RequestMapping("/test02.action")
public String test02(){
    return "redirect:/index.jsp";
}

```

##### 4.4 SpringMVC中的定时刷新

SpringMVC中没有提供实现定时刷新的便捷方式，只能用传统方式实现定时刷新

```Java
/**
 * 定时刷新 - 传统方式
 */
@RequestMapping("/test01.action")
public void test01(HttpServletRequest request,HttpServletResponse response) throws IOException {
    response.setContentType("text/html;charset=utf-8");
    response.getWriter().write("注册成功，3秒后回到主页..");
    response.setHeader("refresh","3;url="+request.getContextPath()+"/index.jsp");
}

```

#### 5.SpringMVC中域的使用

##### 5.1 request域的使用

1. 传统方式向request域中写入数据

   ```Java
   /**
    * 向request域写入数据
    * 方式一：直接通过request对象写入
    */
   @RequestMapping("/test01.action")
   public String test01(HttpServletRequest request) {
   	request.setAttribute("attr1",System.currentTimeMillis());
   	return "my02test01";
   }
   
   ```

2. 向model中写入数据，默认就是写入request域

   ```Java
   /**
    * 向request域写入数据
    * 方式二：向model对象中写入的数据默认进入request域
    */
   @RequestMapping("/test02.action")
   public String test02(Model model) {
   	model.addAttribute("attr1",System.currentTimeMillis());
   	return "my02test02";
   }
   
   ```

##### 5.2 session域的使用

1. 仍然可以使用传统方式使用session

   ```Java
   /**
    * 向session域中写入数据
    * 方式一：直接通过session对象写入
    */
   @RequestMapping("/test03")
   public String test03(HttpSession session) {
   	session.setAttribute("attr1", System.currentTimeMillis());
   	return "my02test03";
   }
   
   ```

2. 通过model+@SessionAttributes实现将数据写入session

   ```Java
   @Controller
   @RequestMapping("/my02")
   @SessionAttributes("attr1x")
   public class MyController02 {
   	/**
   	 * 向session域中写入数据
   	 * 方式二：通过model+@SessionAttributes实现将数据写入session
   	 */
   	@RequestMapping("/test04")
   	public String test04(Model model) {
   		model.addAttribute("attr1x",System.currentTimeMillis());
   		return "my02test04";
   	}
   }
   
   ```

##### 5.3 ServletContext域的使用

==只能通过传统方式写入==

```Java
/**
 * 向ServletContext域中写入数据
 * @return 
 */
@RequestMapping("/test05.action")
public String test05(HttpServletRequest request) {
	ServletContext context = request.getServletContext();
	context.setAttribute("attr1", System.currentTimeMillis());
	return "my02test05";

}
```


#### 6.@ModelAttribute

##### 6.1 使用在方法上

则被修饰的方法将会在当前类的任意handler方法执行之前执行，该方法返回的返回值会自动存入model供后续使用

```Java
 @ModelAttribute("k1")
    public String myfunc01(){
        System.out.println("myfunc01..");
        return "abc";
    } 
/**
     * @ModelAttribute 用法1
     *      用在普通方法上，则此方法会在这个控制器类的所有控制器方法执行之前执行
     *      将返回值按照指定键预存入Model中，控制器方法得到的Model中提前就有了这个键值对
     * http://localhost/SpringMVCDay01_05_Scope/my04/test01.action
     */
    @RequestMapping("/test01.action")
    public void test01(Model model){
        Map<String,Object> map = model.asMap();
        System.out.println(map);
    }
```

##### 6.2 使用在方法参数之前

则会从model中获取属性值赋值到被修饰的方法参数上

```Java
  /**
     * @ModelAttribute 用法2
     *      用在控制器方法的参数上，则在控制器方法执行时，会自动从Model中获取指定键的值赋值给该参数
     * http://localhost/SpringMVCDay01_05_Scope/my04/test02.action
     */
    @RequestMapping("/test02.action")
    public void test02(@ModelAttribute("k1") String str){
        System.out.println(str);
    }
```

#### 7.异常处理

##### 7.1 为当前Controller配置错误处理

```Java
package cn.tedu.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/my01")
public class MyController01 {

    /**
     * 当前控制器类内部的异常处理机制 - 只对当前控制器类内部的控制方法有效
     */
    @ExceptionHandler
    public String My01ExceptionHander(Exception e){
        System.out.println("#####发生异常#####"+e.getMessage());
        return "my01err";
    }

    /**
     * 异常处理
     * http://localhost/SpringMVCDay02_02_Exception/my01/test01.action
     */
    @RequestMapping("/test01.action")
    public void test01(){
        int i = 1/0;
    }
}
```

##### 7.2 注解方式配置全局的错误处理

```Java
package cn.tedu.controller;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

/**
 * 全局错误处理器
 */
//@ControllerAdvice
public class MyGlobalExceptionHandler {
    @ExceptionHandler
    public String myExceptionHandler(Exception e){
        System.out.println("#####全局发生异常#####"+e.getMessage());
        return "err";
    }
}
```

##### 7.3 配置文件方式配置全局错误处理

在SpringMVC配置文件中配置

```xml
    <!--配置文件方式全局错误处理-->
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="exceptionMappings">
            <props>
                <prop key="java.lang.NullPointerException">null_err</prop>
                <prop key="java.io.IOException">io_err</prop>
                <prop key="java.lang.Throwable">g_err</prop>
            </props>
        </property>
    </bean>
```

#### 8. 实现返回一段数据 - @ReponseBody 

##### 8.1 返回字符串数据

1. 方式一：通过response返回

   ```java 
   /**
    * 	向客户端直接返回数据 - 字符串数据
    *	方式一：直接通过response对象输出
    * 	http://localhost/SpringMVCDay02_02_Details3/my01/test01.action 
    */
   @RequestMapping("/test01.action")
   public void test01(HttpServletResponse resp) throws Exception {
   	resp.setContentType("text/html;charset=utf-8");
   	resp.getWriter().write("abcde");
   	resp.getWriter().write("中国");
   }
   
   ```

2. 方式二：直接获取PrintWriter返回

   ```java 
   /**
    * 	向客户端直接返回数据 - 字符串
    *	方式二：直接获取PrintWriter对象输出
    * 	http://localhost/SpringMVCDay02_02_Details3/my01/test02.action
    */
   @RequestMapping("/test02.action")
   public void test02(PrintWriter writer) throws Exception {
   	writer.write("abcde");
   }
   
   ```

3. 方式三：通过@ResponseBody返回

   ```java 
   /**
    * 	向客户端直接返回数据 - 字符串
    * 	方式二：通过@ResponseBody将返回值直接写入响应
    *        其中可以通过@RequestMapping的produces属性控制输出时的类型编码
    * 	http://localhost/SpringMVCDay02_02_Details3/my01/test03.action
    */
   @ResponseBody
   @RequestMapping(value="/test03.action",produces="text/html;charset=utf-8")
   public String test03() {
   	return "abcd中国";
   
   }
   ```

#####  8.2 实现自动发送json格式数据 - AJAX

1. 方式一：手动拼接json

   ```java 
   /**
    * 	向客户端直接返回数据 - json
    * 	http://localhost/SpringMVCDay02_02_Details3/my01/test04.action
    */
   @ResponseBody
   @RequestMapping(value="/test04.action")
   public String test04() throws Exception {
   	User user = new User(99,"zs",Arrays.asList("bj","sh","gz"));
   	String str = "";
   	for(String addr : user.getAddrs()) {
   		str += "'"+addr+"',";
   	}
   	str = str.substring(0, str.length()-1);
   	String json = "{id:'"+user.getId()+"',name:'"+user.getName()+"',addrs:["+str+"]}";
   	return json;
   
   }
   ```

2. 方式二：通过配置@ResponseBody利用内置的jackson将对象处理为json返回 

   ```java 
   /**
    * 	向客户端直接返回数据 - json
    * 	http://localhost/SpringMVCDay02_02_Details3/my01/test05.action
    */
   @ResponseBody
   @RequestMapping(value="/test05.action",produces="application/json;charset=utf-8")
   public User test05() throws Exception {
   	User user = new User(99,"张三",Arrays.asList("bj","sh","gz"));
   	return user;
   }
   
   ```

#### 9.处理器方法支持的参数类型和返回值类型总结

##### 9.1 支持的方法参数类型

i. HttpServletRequest

​	代表当前请求的对象代表当前响应的对象

ii. HttpServletResponse

   代表当前会话的对象

iii. HttpSession

​	代表当前会话的对象

iv. WebRequest

​	SpringMVC提供的对象，相当于是request和session的合体，可以操作这两个域中的属性。

v. InputStream OutputStream Reader Write

​	代表request中获取的输入流和response中获取的输出流

vi. 通过@PathVariable @RequestParam声明的方法参数

​	@PathVariable可以将请求路径的指定部分获取赋值给指定方法参数

​	@RequestParam可以将指定请求参数赋值给指定方法参数 

​	如果不写此注解，则默认会将同名的请求参数赋值给方法参数

vii. 通过@CookieValue和@RequestHeader声明的方法参数

​	@CookieValue可以将请求中的指定名称的cookie赋值给指定方法参数

​	@RequestHeader可以将请求参数中的指定名称的头赋值给指定方法参数

viii. Model和ModelMap和java.util.Map 

 	向这些Model ModelMap Map中存入属性，相当于向模型中存入数据

ix. Bean类

​	SpringMVC自动将请求参数封装到bean

x. MultipartFile

​	实现文件上传功能时，接收上传的文件对象

xi. Errors BindingResul

​	实现数据验证的参数

##### 9.2 支持的返回值类型

i. ModelAndView

​	可以返回一个ModelAndView对象，在其中封装Model和View信息

ii. View

​	可以直接返回一个代表视图的View对象

iii. 字符串

​	直接返回视图的名称

iv. void

​	如果返回值类型是void，则会自动返回和当前处理器路径名相同的视图名

v. 方法被@ResponseBody修饰

​	当方法被@ResponseBody修饰时，默认将返回的对象转为json写入输出

vi.除以上之外返回的任何内容都会被当做模型中的数据来处理，而返回的视图名等同于当前处理器路径名

