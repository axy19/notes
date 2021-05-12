[TOC]

# Servlet

#### 一、servlet概述

##### 1. servlet是什么？--接口

servlet是由sun公司提供的一套接口规范，是专门应用于javaweb网站的开发技术。

servlet=server+applet  服务器+应用程序

web应用服务器实现了servlet这一套接口规范，tomcat实现了servlet的接口。

![](https://note.youdao.com/yws/api/personal/file/4BFEAE6707694A65A7AEB3EAF858077E?method=download&shareKey=a1d13311b16e70cbbd9ba5f1d3fab387)

##### 2.servlet的主要作用

处理客户端（浏览器）发送过来的http请求，响应给客户端（浏览器）一个请求结果。

举例：百度



#### 二、servlet的API

##### 1. servlet的API在哪里？

1. 如果要使用serverlet必须要先导入他的jar包

   tomcat-->lib库---》servlet-api.jar【配置猫的时候直接导入lib即可】

##### 2. 编写一个servlet程序：入门案例

1. 在src目录下创建一个package包：servlet，然后创建一个类：Servlet01_servlet接口

2. 需要先实现servlet接口

3. servlet程序如果要进行代码的执行，需要浏览器发送一个请求过来才可以。

   举例：servlet相当于餐厅服务员，只有当有客户用餐时，服务员才会执行起来。

4. 需要在web.xml中配置一个文件：servlet的映射关系

   举例：就想当以客户点餐时，需要一个菜单  ，服务员看到 才知道客户要什么。

5. servlet调用过程

   ![](https://note.youdao.com/yws/api/personal/file/E779597F3B4843E09D8A34FF35F3E048?method=download&shareKey=7f2d728e23246903bc527e73162284b3)

   ![](https://note.youdao.com/yws/api/personal/file/1D1596B8CA6E443A94111B4CBF9F99B8?method=download&shareKey=9e4a4244da13030b114d6cbb21c0897b)

6. servlet的生命周期：

   - 当tomcat服务器启动时，加载配置文件web.xml，只有当web.xml文件都配置正确时，才会启动成功。
   - 当http请求发送到web.xml解析时，如果找到了一个class文件路径，tomcat会自动创建一个class对象。
   - servlet活动：在请求处理期间是一直存在的。
   - servlet销毁：在tomcat关闭之前，执行销毁方法，最终对象会被jvm虚拟机回收。


```java
public class Serverlet01_serverlet接口 implements Servlet {

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("这是一个初始化方法，先执行init()方法");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("这是service服务方法，程序员编写的业务逻辑()处理请求的代码就在这里写");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("help...me...");
    }
}
```

配置文件

```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--serverlet的映射关系：浏览器请求与servlet代码的映射关系-->
    <servlet>
        <!--servlet-name是程序员自定义的名字:相当于菜名-->
        <servlet-name>hello</servlet-name>
        <servlet-class>serverlet.Serverlet01_serverlet接口</servlet-class>
    </servlet>
    <servlet-mapping>
        <!--必须与servlet标签的name对应-->
        <servlet-name>hello</servlet-name>
        <!--浏览器发送请求的资源路径:相当于客户点餐-->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

#### 三、GenericServlet的继承关系--抽象类

1. servlet是一个接口，内部只有方法签名，没有方法体

   如果要使用servlet就必须实现servlet接口，有以下方法：

   ```
   public void init(ServletConfig servletConfig) throws ServletException {}
   public ServletConfig getServletConfig() {
       return null;
   }
   public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
   }
   public String getServletInfo() {
       return null;
   }
   public void destroy() {}
   ```

2. servlet的一个实现类：GenericServlet类

3. 浏览器发送http请求的方式：7种

   常用的有：get/post

   不常用的有:put ,delete,tarce,head,options

   问题是：GenericServlet.service()是专门接受请求的，那么它能接受多少种请求？

   答案：能接受以上7种所有的请求。

   接受请求后，不同的请求方式，比如get和post两种，那么service方法中的处理逻辑要分开执行，get方式的请求执行get的代码逻辑，post方式的请求执行post的代码逻辑。（这种书写方式很繁琐，需要简化处理）

![](https://note.youdao.com/yws/api/personal/file/3B0C8F5A50A54D039B33475F406B29A1?method=download&shareKey=5479f8f3822808c79283f11ffd72a9f7)

```Java
public class Servlet02_GenericServlet extends GenericServlet {
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        //主要的业务逻辑代码
        /*实现的功能如下：在浏览器中显示当前的日期时间*/
        Date date=new Date();
        //如何把date日期返回给浏览器。
        System.out.println(date.toString());
        //参数一：ServletRequest是接收浏览器的请求

        //参数二：ServletResponse是响应给浏览器的结果
        // servletResponse.getWriter()，只是通过response打开一个writer流
         servletResponse.getWriter().write(date.toString());
         //根据不同的请求会执行不同的代码逻辑
        //1.获取浏览器的请求方式
        HttpServletRequest rep= (HttpServletRequest) servletRequest;
        String method=rep.getMethod();//得到http的请求方式
        System.out.println("请求方式："+method);
        if("GET".equals(method)){
            System.out.println("get方式的处理逻辑");
        }else if("POST".equals(method)){
            System.out.println("post方式的处理逻辑");
        }
    }
}
```

配置文件

```xml
<servlet>
    <servlet-name>date</servlet-name>
    <servlet-class>serverlet.Servlet02_GenericServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>date</servlet-name>
    <url-pattern>/date</url-pattern>
</servlet-mapping>
```

#### 四、HTTPServlet的继承关系---抽象类

1. ==HttpServlet的诞生是专门为了HTTP请求而服务的。==

   public abstract class HttpServlet extends GenericServlet

2. HttpServlet的方法详解

   protected  void doDelete(HttpServletRequest req, HttpServletResponse resp)
                 Called by the server (via the service method) to allow a servlet to handle a DELETE request.
                 由服务器（通过服务方法）调用，以允许servlet处理删除请求。
       protected  void doGet(HttpServletRequest req, HttpServletResponse resp)
                 Called by the server (via the service method) to allow a servlet to handle a GET request.
                 服务器（通过服务方法）允许servlet处理GET请求。
       protected  void doHead(HttpServletRequest req, HttpServletResponse resp)
                 Receives an HTTP HEAD request from the protected service method and handles the request.
                 从受保护的服务方法接收HTTP HEAD请求并处理该请求。
       protected  void doOptions(HttpServletRequest req, HttpServletResponse resp)
                 Called by the server (via the service method) to allow a servlet to handle a OPTIONS request.
                 由服务器（通过服务方法）调用，以允许servlet处理选项请求。
       protected  void doPost(HttpServletRequest req, HttpServletResponse resp)
                 Called by the server (via the service method) to allow a servlet to handle a POST request.
                 由服务器（通过服务方法）调用，以允许servlet处理POST请求。
       protected  void doPut(HttpServletRequest req, HttpServletResponse resp)
                 Called by the server (via the service method) to allow a servlet to handle a PUT request.
                 由服务器（通过服务方法）调用，以允许servlet处理PUT请求。
       protected  void doTrace(HttpServletRequest req, HttpServletResponse resp)
                 Called by the server (via the service method) to allow a servlet to handle a TRACE request.
                 由服务器（通过服务方法）调用，以允许servlet处理跟踪请求。
       protected  long getLastModified(HttpServletRequest req)
                 Returns the time the HttpServletRequest object was last modified, in milliseconds since midnight January 1, 1970 GMT.
                 返回HttpServletRequest对象上次修改的时间，以自1970年1月1日格林尼治标准时间午夜起的毫秒为单位。
       protected  void service(HttpServletRequest req, HttpServletResponse resp)
                 Receives standard HTTP requests from the public service method and dispatches them to the doXXX methods defined in this class.
                  接收来自公共服务方法的标准HTTP请求，并将它们分派给该类中定义的doXXX方法。
       void service(ServletRequest req, ServletResponse res)
                 Dispatches client requests to the protected service method.
                  将客户端请求分派到受保护的服务方法。

3. HttpServlet的方法：service方法

4. 模拟一个场景：用户的一个注册提交的场景。

   代码的编写步骤：

   - 修改param.html中form表单中的action路径：是一个请求的url

     /项目名称/--->/web虚拟路径的名称/请求的名称

     action=“/day10_web_war_exploded/demo03”

   - 配置web.xml，完成url路径与servlet类的映射关系

   - 编写后台的servlet的代码

     > 找到doGet和都Post

#### 五、http协议

![](https://note.youdao.com/yws/api/personal/file/F8DA90331F4141C7BC298AD3E9266C38?method=download&shareKey=ab8e85a9f0e3111490e7e990469f43d5)

#### 六、Request---域对象

##### 1. Request的继承结构

###### 1.1 ServletRequest:接口

定义：将客户端请求信息提供给某个servlet对象，servlet容器（tomcat服务器中的组件）创建ServletRequest对象，并将该对象作为参数传递给servlet的service方法。

这个ServletRequest接受的请求不仅仅是HTTP协议的请求，还可能是其他类型的请求。

特别注意：javaweb课程，包括以后工作都是用的HTTP协议

###### 1.2 HttpServletRequest：接口（以后写代码用）只接受http协议

ServletRequest接口

 				|

​				 |HttpServletRequest：接口（对ServletRequest的扩展，提供了更多的方法去操作http协议）

​								|HttpServletRequestWrapper(实现类)

​                                |tomcat服务器会自动创建一个对象：HttpServletRequest类型的对象

​								|HttpServletRequest   req = new  HttpServletRequestWrapper();

##### 2.HttpServletRequest的常用方法

###### 2.1 获取客户端相关信息

```java
/*("/request01") 代表的是 浏览器发送过来的请求信息  */
@WebServlet("/request01") /*完全代替了web.xml的配置文件映射关系*/
public class Request01_获取客户机相关信息 extends HttpServlet {
                protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    this.doGet(request,response);
                }
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                //编写自己的业务逻辑
                /**
                 * getRequestURL方法 -- 返回客户端发出请求完整URL
                 *    结果：http://localhost:80/day10_servlet_war_exploded/request01
                 * getRequestURI方法 -- 返回请求行中的资源名部分
                 *    结果： /request01
                 * getQueryString方法 -- 返回请求行中的参数部分
                 *     结果： username=...&password=...
                 * getRemoteAddr方法 -- 返回发出请求的客户机的IP地址
                 *     结果：127.0.0.1:80
                 * getMethod方法 -- 返回客户机的请求方式
                 *     结果：GET/POST
                 * getContextPath方法 -- 获得当前web应用的虚拟目录名称
                 *      结果：/day10_servlet_war_exploded
                 */
                String url = request.getRequestURL().toString();
                System.out.println("完整请求路径"+url);
                String requestURI = request.getRequestURI();
                System.out.println("资源路径："+requestURI);
                String queryString = request.getQueryString();
                System.out.println("请求行中参数："+queryString);
                String remoteAddr = request.getRemoteAddr();
                System.out.println("客户机的IP地址："+remoteAddr);
                String contextPath = request.getContextPath();
                System.out.println("虚拟目录名称："+contextPath);

            }
        }
```

###### 2.2 获取请求头信息

```java
@WebServlet("/request02")
public class Request02_获取请求头信息 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求头中的信息
        /*
        * getHeader(name):name 表示字符串类型的key，返回值是字符串类型value
        * getHeaders(String name)方法 --- Enumeration<String>有的name会对应多个值
			  * getHeaderNames方法 --- Enumeration<String>
			  * getIntHeader(name)方法  --- int
			  * getDateHeader(name)方法 --- long(日期对应毫秒)
        * */
        String host = req.getHeader("Host");
        System.out.println(host);
        String accept = req.getHeader("Accept");
        System.out.println(accept);
        /*getHeaders(String name)*/

        /*getHeaderNames()--返回一个所有的请求头信息，枚举类型的集合*/
        Enumeration<String> headerNames = req.getHeaderNames();
        while(headerNames.hasMoreElements()){//判断集合中是否存在元素
            //获取元素对象，返回值是请求头的key
            String headerName=headerNames.nextElement();
            String value = req.getHeader(headerName);
            System.out.println("key"+headerName+"value"+value);
        }
        /*Upgrade-Insecure-Requests：value值是一个int类型*/
        int intHeader = req.getIntHeader("Upgrade-Insecure-Requests");
        System.out.println(intHeader);
    }
}
```

###### 2.2 获取请求参数

```java
@WebServlet("/request03")
public class Request03_获取请求中的参数 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置请求对象req的编码格式位utf-8
        req.setCharacterEncoding("utf-8");
        //设置相应对象resp的编码格式位utf-8
        resp.setContentType("text/html;charset=utf-8");
        //获取请求中的参数信息：key=value,获取结果为value
        /*模拟一个场景：注册信息的获取
         getParameter(String name) --- String通过name获得值
         getParameterNames()  --- Enumeration<String> 获得所有name
        */
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        System.out.println(username);
        System.out.println(password);
        if("tony".equals(username)){//如果注册用户名是tony
            //提示注册信息已经存在
            resp.getWriter().write("注册失败，tony已经存在");
        }else{
            resp.getWriter().write("注册成功");
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req, resp);
    }
}
```

###### 2.3 实现请求转发

请求重定向: 302+location
请求转发: ==实现资源的跳转, 服务器内部的跳转. 一次请求 一次响应 地址栏不会发生变化==
RequestDispatcher dis = request.getRequestDispatcher("xxxxx");//调度器
dis.forward(request, response);//实现请求转发

1.在请求转发之前, 如果response缓冲区写入了数据但是还没有打给浏览器, 在请求转发时这些数据将会被清空
2.在请求转发之前, 如果response缓冲区写入了数据并且打给了浏览器, 请求转发失败抛出异常!
3.请求转发就像方法的调用, 在转发代码之后的代码将会在转发结束后继续执行
4.不能多次转发, 但是可以多重转发

![](https://note.youdao.com/yws/api/personal/file/1EA2B9C7123748B6AEC6C7D7859B5C32?method=download&shareKey=4ac1c602367a1d858c26ce852ca6179a)

```java
package demo;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/request01")
public class Request01 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //模拟场景
        System.out.println("浏览器发送的请求暂时处理不了");
        System.out.println("需要交给另外一个servlet处理");
        //1.创建一个调度器：作用是把request请求指向了另一个servlet
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("/request02");
        //2.把request对象和response对象一并发送给request02
        /*主要作用再转发过程中分别保证请求对象和相应对象是相同的*/
        System.out.println("request01....start");
        requestDispatcher.forward(req,resp);
        System.out.println("request02...end");
    }
}
```

```java
package demo;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/request02")
public class Request02 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("接受到了request01转发过来的请求");
        System.out.println("我需要做出响应");
        //向浏览器发送一个响应
        resp.getWriter().write("。。。。￥10000");
    }
}
```

###### 2.4 实现请求包含

所谓的请求包含指的是服务器内部实现资源合并的效果
如果浏览器请求ServletA, 在A的内部可以通过
request.getRequestDispatcher("B的虚拟路径").include(request, response);将ServletB包含进来, 这时将由A和B共同处理该请求, B处理的结果将会并入A处理的结果, 一起响应给浏览器

###### 2.5 作为域对象使用

域对象: 一个对象具有可以被看见的范围, 利用这个对象身上的map就可以实现资源的共享, 像这样的对象就称之为域对象
			setAttribute(String name, Object valObj);
			getAttribute(String name);
			removeAttribute(String name);
			getAttributeNames();

生命周期：一次请求开始，到一次请求结束

作用范围：在整个请求链上都可以看见

主要功能：在转发时带数据到目的地

#### 七、Response

代表http响应的对象
	ServletResponse< -- HttpServletResponse(继承关系)

##### 1.继承结构 (!掌握)

ServletResponse -- 通用的响应接口, 定义了响应对象应该具有的功能
			|继承
			|--HttpServletResponse 在ServletResponse的基础上, 添加了很多和Http协议相关的方法

![](https://note.youdao.com/yws/api/personal/file/D841E8F665514DE88F38A4469EB7DCEF?method=download&shareKey=fc8b92c33a08a34c860d6eedef87d856)

![](https://note.youdao.com/yws/api/personal/file/62A36A9B7BA2426B866F4031C186C99B?method=download&shareKey=bc2507ebcf060ae9cea2f0e736526bff)

![](https://note.youdao.com/yws/api/personal/file/C2F3A911A1D84F98A0EC0A3AD1A1D7B0?method=download&shareKey=206b8f5cd65a9c5d193a33fdb357aba1)

##### 2.Response上的重要方法

一个状态行
				HTTP/1.1 200 OK
				HTTP/1.1: 当前所遵循的协议版本
				200: 响应码 -- 一个3位的数字, 范围为100~600, 表示服务器处理请求的结果
					200~299 表示服务器正确的处理了请求
					300~399 表示服务器正确的处理了本次请求, 但是如果想要继续执行, 还需要更多的额外信息
					400~499 表示客户端的请求有问题
					500~599 表示服务器端发生了问题

​                    ==200 表示服务器处理成功==
​				   302 表示请求重定向
​				   304/307 通知浏览器使用缓存资源
​				   ==404 表示客户端请求的资源路径找不到==
​				  ==500 表示后端Java代码，服务器端处理请求出错==

​            OK: 描述字符

若干响应头:
				xxx
				...
				xxx

(一个空行)
		实体内容: xxxxx
		设置状态码的方法
	      void setStatus(int sc)

 		void setStatus(int sc, String sm)  

设置响应头的方法
			void setHeader(String name, String value) 
			void setDateHeader(String name, long date)
			void setIntHeader(String name, int value) 

​           void addHeader(String name, String value) 
​			void addDateHeader(String name, long date)
​			void addIntHeader(String name, int value)
​			set方法是如有没有则添加,如果有则更新
​			add方法是不论有没有都添加新的
​		设置响应内容的方法
​			ServletOutputStream getOutputStream() 
​			PrintWriter getWriter() 

##### 3.Response的功能 (!掌握)

###### 3.1 向客户端发送数据 (!!!重点, 特别是乱码问题)

getOutputStream() 

getWriter() 

字节流发送数据的中文乱码问题

服务器端指定了用utf-8来发送数据, 浏览器在接受数据时, 如果不指定将使用默认的平台码GBK, 编解码不一致导致乱码. 

解决方案: 
				response.setHeader("Content-Type", "text/html;charset=utf-8");// 通知浏览器使用utf-8打开服务器发送过去的数据
			response.getOutputStream().write("中国".getBytes("utf-8"));// 指定编码为utf-8

字符流发送数据的中文乱码问题
利用字符流发送数据, 底层还是要转成字节. 服务器端如果不手动指定, 服务器默认会使用iso8859-1码表, 由于里面没有中文汉字, 所以服务器端发送给客户端就是一堆乱码, 客户端不管使用什么码表都无法转成正常的字符

服务器会根据getCharacterEncoding()方法返回的编码来发送数据, 如果没有指定, 该方法默认返回iso8859-1

解决方案:
第一步, 需要指定服务器发送数据使用utf-8
response.setCharacterEncoding("utf-8"); //通知服务器使用utf-8来发送响应实体中数据
第二步: 需要指定浏览器在接收数据时也使用同一个编码来打开数据
response.setHeader("Content-Type","text/html;charset=utf-8");
等价于<==>			response.setContentType("text/html;charset=utf-8");

在通知浏览器使用什么编码接受服务器发送的数据时, 服务器很智能, 会使用相同的编码来发送数据, 所以指定服务器以什么编码发送数据的代码可以省略不写

总结: 不管是字符流还是字节流, 解决乱码问题, 可以用一行代码搞定:response.setContentType("text/html;charset=xxx");
getOutputStream()和getWriter() 这两个方法是互斥的, 在一次请求当中调用了其中的一个, 就不能再调用另一个!!!
在调用完getOutputStream()或getWriter()方法之后, 不需要手动去关闭流, 服务器会自动帮我们去关闭!!!	
这个两个方法获取到的流并不是指向客户端的流, 而是指向response缓冲区的流, 通过流将数据写入response缓冲区, service方法执行结束, 请求回到服务器, 由服务器将数据组织成响应消息打给浏览器!!	

```Java
package demo;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/response02")
public class Response02  extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.定义一个字符串
        String str="我爱你中国";
        //2.使用响应流输出内容到浏览器页面中
        //2.1使用字节流响应
        //如果在获取字节流时没有设置编码格式那么会默认使用平台的编码--gbk
        //平台：不同操作系统的默认字符集：gbk
        //浏览器默认的字符集gbk
        //所以这一句话不会出现乱码
        //resp.getOutputStream().write(str.getBytes());
        //2.2字节流设置utf-8的格式
        //浏览器默认字符集jbk
        //响应流设置的字符集utf-8，编码格式不匹配会出现乱码
        //resp.getOutputStream().write(str.getBytes("utf-8"));
        //2.3字节流设置utf-8
        //设置HTTP协议的响应头，告诉浏览器使用什么类型的编码格式进行解析
        //resp.setHeader("Content-Type","text/html;charset=utf-8");
        //服务器端的代码使用utf-8进行字节流获取
        //resp.getOutputStream().write(str.getBytes("utf-8"));
        //2.4使用字符流响应
        //底层原理：字符流的底层依然是字节流
        //如果进行字符流输出时，没有设置编码格式会默认使用tomcat服务器默认的字符集iso-8859-1
        //getWriter方法返回的时printWriter字符流
        //resp.getWriter().write(str);
        //2.5字符流设置响应实体的内容编码为utf-8
        //服务器端的字符流时utf-8格式的，但是浏览器的默认字符集jbk
        //所以该结果一定会出现乱码
        //resp.setCharacterEncoding("utf-8");
        //resp.getWriter().write(str);
        //2.6字符流完整的使用方法
        //服务器端的字符流编码格式
        resp.setCharacterEncoding("utf-8");//可以不写，因为idea本身使用utf-8编码格式
        //通知浏览器使用utf-8的格式进行解析
        //和下边一句等价resp.setHeader("Content-Type","text/html;charset=utf-8");
        resp.setContentType("text/html;charset=utf-8");
        resp.getWriter().write(str);
    }
}
		
```

###### 3.2 实现重定向 (!!重点)

重定向的原理就是302+location, 通过设置状态码302和location响应头就可以实现重定向的效果
			response.setStatus(302);
			response.setHeader("Location", "/Day09/index.jsp");
			这两行代码等价于
			response.sendRedirect("资源路径（前边要加/）/其他的网址");

==特点：==

两次请求，两次响应；

浏览器的地址栏的位置发生了改变；

两个请求对象不同，两个响应对象不同；

重定向不仅可以定位到一个静态的页面，也可以定位到一个/response01的请求

重定向可以在不同的web资源中进行页面的跳转

![](https://note.youdao.com/yws/api/personal/file/C8D61ECB787D4B0CAD9002BD3A648AC9?method=download&shareKey=2de9e5ba0611ba019296f919e820e5d5)

```Java
package demo;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/response01")
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.response对象中的状态码302重定向
        resp.setStatus(302);
        //重定向到url
        resp.sendRedirect("http://www.tmooc.cn");
    }
}
```

###### 3.3 实现定时刷新 (!掌握)

![](https://note.youdao.com/yws/api/personal/file/2A08CA8C1AFE431F9BE424134BD80DF5?method=download&shareKey=75be39e0486599f5e7c8095c46ba9c89)

定时刷新是通过Refresh响应头, 可以实现在多少秒之后跳转到另外一个资源
response.setHeader("Refresh", "3;url=/Day09/index.jsp"); 

应用场景一般是在注册成功或者登录成功后使用。

```Java
package demo;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
@WebServlet("/response03")
public class Response03 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.设置请求的编码格式
        req.setCharacterEncoding("utf-8");
        //2.设置响应的编码格式
        //服务器端的编码
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");//通知浏览器使用utf-8解析
        //3.使用字符流响应给浏览器一段HTML代码
        resp.getWriter().write("<h1><font color='red'>注册成功，3秒后跳转到主界面</font></h1>");
        //4.使用refresh设置响应头的刷新时间
        resp.setHeader("refresh", "3;url=http://www.easymall.com");
    }
}
```

###### 3.4 控制浏览器使用缓存

![](https://note.youdao.com/yws/api/personal/file/6F2F9E59F5EF45999066B0EC39265B46?method=download&shareKey=98798c6445260fde8ae58718b664e997)

由于不同的浏览器的缓存行为可能是不同的, 我们可以在服务器中通过设置响应头来控制浏览器的缓存行为!!
控制浏览器不要缓存: ---验证码
  			HTTP1.0	setDateHeader("Expires", -1);
  			HTTP1.1	setHeader("Cache-control", "no-cache");

```Java
package demo;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Date;

@WebServlet("/response04")
public class Response04 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.创建一个日期对象
        Date d=new Date();
        //2.Response对象控制浏览器不使用缓存---会实时刷新
        //参数-1表示时间毫秒数，-1是不使用缓存
        resp.setDateHeader("Expires",-1);
        resp.setHeader("pragma","no-cache");
        resp.setHeader("Cache-control","no-cache");
        //3.把日期输出到浏览器中显示
        resp.getWriter().write(d.toLocaleString());

    }
}
```

控制浏览器使用缓存:--自动免密登陆 
HTTP1.0：

setDateHeader("Expires", System.currentTimeMillis()+1000*60*60*24【1970年开始的毫秒数】);
HTTP1.1：

setHeader("Cache-control", "max-age=60");

```Java
package demo;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Date;

@WebServlet("/response05")
public class Response05 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Date d=new Date();
        //控制浏览器使用缓存
        response.setDateHeader("Expires",System.currentTimeMillis()+1000*60*60*24);
        response.setHeader("Cache-control","max-age=5");
        response.getWriter().write(d.toLocaleString());
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

###### 3.5 总结: (!掌握)

请求转发/请求重定向/定时刷新都可以实现资源的跳转, 区别是什么呢? 
请求转发:
1.一次请求,一次响应 request对象是同一个
2.地址栏不会发生变化
3.只能用于服务器内部的资源跳转, 并且只能是同一应用中的不同资源上进行跳转, 不可用在不同应用和不同服务器中的资源跳转
请求重定向:
1.两次请求,两次响应 request对象不是同一个
2.地址栏会发生变化
3.可以用于服务器内部的资源跳转, 也可以用于不同应用和不同服务器之间的资源跳转
定时刷新:
1.两次请求,两次响应 request对象不是同一个，地址栏会发生变化。
2.可以用于服务器内部的资源跳转, 也可以用于不同应用和不同服务器之间的资源跳转
3.和重定向不同的是, 定时刷新可以在刷新到新的地址之间设置一个时间, 在间隔的这段时间内可以输出文本到浏览器并维系一段时间

那什么时候用哪种方式进行资源的跳转呢?

如果是同一服务器中的同一应用内部的资源跳转；如果需要利用request域在跳转的资源之间传输数据, 只能用请求转发；如果不想让地址栏发生变化, 只能用请求转发；如果需要地址栏发生变化, 只能用重定向或定时刷新；如果没有什么特殊需求, 三种方式都可以, 但是推荐使用转发, 可以减少请求次数降低服务器的压力；如果只是想更新刷新操作, 最好使用重定向或定时刷新, 使用请求转发, 在刷新时会把刚才的操作再做一遍, 可能会导致一些问题, 比如表单重复提交或重复支付订单等；如果是不同服务器或不同应用内部的资源跳转, 只能用重定向或这定时刷新；重定向和定时刷新的主要区别在于: 重定向会立即跳转, 而定时刷新可以设置一个时间间隔, 在指定时间后再进行跳转；如果在跳转之前需要输出提示信息(如: 注册成功, xx秒后跳转到xxx)只能用定时刷新, 否则两种方式都可以。

#### 八、ServletConfig

代表当前Servlet在web.xml文件中配置信息的对象

![](https://note.youdao.com/yws/api/personal/file/810ED9C9DEC44CF0807B7BD9D8A299B0?method=download&shareKey=a34470daf7b0aba3f2fac05c9c0276f8)

##### 1.获取ServletConfig对象

​		ServletConfig scf = this.getServletConfig();

##### 2.ServletConfig

###### 2.1获取当前Servlet的初始化参数

在web.xml中的<Servlet>标签的内部可以为当前Servlet配置零个或多个初始化参数,（<init-param>标签中的内容） 这些参数相当于我们为Servlet配置了一些初始化信息, 可以通过ServletConfig对象(在当前Servlet内部可以获取该对象)来获取这些信息。

###### 2.2获取的方法

getInitParameter(String name);
getInitParameterNames();

###### 2.3 获取ServletContext对象

this.getServletConfig().getServletContext();

##### 3.局限性

只在当前的servlet对象中有效，对其他的servlet无效

```java 
package demo;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

public class ServletConfig01 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //1.获取一个ServletConfig对象
        // 该对象对应着web.xml中<servlet>中的配置信息，
        // 该对象只是对当前的servlet对象来说跟其他的servlet对象没有关系
        ServletConfig servletConfig = this.getServletConfig();
        //2.使用api中的方法，获取初始化的配置信息key，value
        //2.1该方法参数为key返回值为value结果只有一个value
        String encoding = servletConfig.getInitParameter("encoding");
        System.out.println(encoding);
        //2.2 该方法返回当前servlet中所有的配置信息，key:value的键值对信息
        Enumeration<String> initParameterNames = servletConfig.getInitParameterNames();
        while(initParameterNames.hasMoreElements()){
            String key=initParameterNames.nextElement();
            String value=servletConfig.getInitParameter(key);
            System.out.println("key:"+key+",value:"+value);
        }

    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--对所有的servlet都有效-->
    <servlet>
        <servlet-name>config01</servlet-name>
        <servlet-class>demo.ServletConfig01</servlet-class>
        <!--init-param是当前servlet的初始化配置信息-->
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <init-param>
            <param-name>key1</param-name>
            <param-value>value1</param-value>
        </init-param>
        <init-param>
            <param-name>key2</param-name>
            <param-value>value2</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>config01</servlet-name>
        <url-pattern>/config01</url-pattern>
    </servlet-mapping>
</web-app>
```

使用注解配置初始化信息

```Java
//initParams对应着配置文件所有的init-param
//@WebInitParam对应着其中一个<init-param>
//name对应着<param-name>标签
//value对应着<param-value>标签
@WebServlet(value="config02",initParams = {
        @WebInitParam(name="encoding",value="utf-8"),
        @WebInitParam(name="key3",value="value3")
})
```

设计思想：

解耦思想：代码与配置信息分离，代码的可重用性非常高。目的：让程序员写的代码可以适用于任何的场景。比如：配置信息改变了，代码依然可以使用。

解耦的案例：jdbc的编写=配置文件+代码编写

#### 九、ServletContext

代表当前web应用的对象
在web应用被加载后, 服务器会立即创建出代表当前web应用的ServletContext对象, 创建后该对象会一直驻留在内存中唯一的代表当前web应用, 直到服务器关闭或者web应用移出容器时为止, 随着web应用的销毁, ServletContext对象也跟着销毁!

##### 1.ServletContext功能

获取web应用的初始化参数
在当前Servlet中配置的参数信息, 只能在当前Servlet中通过ServletConfig对象来获取, 在其他的Servlet中无法获取!
如果有一段初始化参数, 希望不是属于某一个Servlet对象, 而是整个web应用都可以使用, 可以将这些参数配置到web.xml的根目录下, 配置在根目录下的这些信息是属于整个web应用的, 可以通过代表整个web应用的ServletContext来获取。
			<context-param>
				<param-name>scparam1</param-name>
				<param-value>scvalue1</param-value>
			</context-param>
获取参数方法:
				getInitParameter();
				getInitParameterNames();

##### 2.作为域对象来使用

​			ServletContext对象是一个域对象, 利用这个对象上的map就可以在整个web应用内实现资源的共享.
获取方法:
​				setAttribute();
​				getAttribute();
​				removeAttribute();
​				getAttributeNames();
​			生命周期:
​				和WEB应用的命一般长
​			作用范围:
​				在整个WEB应用内都可以被看见
​			主要功能:
​				在整个WEB应用内实现资源的共享
​			ServletContext在作为作用域时,也被称为Application作用域

![](https://note.youdao.com/yws/api/personal/file/D37D6F93CF3448EFAA3D431A1E7835E7?method=download&shareKey=213bd57174759982415f7d33a5a1153b)

```Java
package demo;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

@WebServlet("/context01")
public class ServletContext01 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取一个servletContext对象
        //1.1直接获取
        ServletContext servletContext = this.getServletContext();
        //1.2间接获取
        ServletContext servletContext1 = this.getServletConfig().getServletContext();
        //得出一个结论ServletContext对象在web应用活动期间，内存中只有一个
        System.out.println(servletContext==servletContext1);
        //2.servletcontext对象获取一个配置信息---》web.xml文件中配置
        //改配置信息是全局的所有的servlet对象都能获取到
        String encoding = servletContext.getInitParameter("encoding");
        System.out.println(encoding);

        Enumeration<String> initParameterNames = servletContext.getInitParameterNames();
        while(initParameterNames.hasMoreElements()){
            String s = initParameterNames.nextElement();
            String initParameter = servletContext.getInitParameter(s);
            System.out.println(s+initParameter);
        }
        //3.servletcontext对象配置，删除，获取 共享的信息。key=value
        //3.1设置一个attribute键值对的属性
        servletContext.setAttribute("success","设置成功");
        servletContext.setAttribute("success1","设置成功1");
        servletContext.setAttribute("success2","设置成功2");
        //3.2删除某一个attribute键值对的属性
        servletContext.removeAttribute("success2");
        //3.3获取其中一个attribute键值对的属性
        String success = (String )servletContext.getAttribute("success");
        String success1 = (String )servletContext.getAttribute("success1");
        //3.4获取所有的attribute键值对的属性
        Enumeration<String> attributeNames = servletContext.getAttributeNames();
        while(attributeNames.hasMoreElements()){
            String s=attributeNames.nextElement();
            //因为该对象是全局的对象，默认会加载更多的attribute属性，
            // 如果强制转换会报500的错误，提示强制转换失败。
            Object  attribute = servletContext.getAttribute(s);
            System.out.println("name:"+s+"attribute:"+attribute);
        }


    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }
}

```

##### 3.获取web资源

在web开发中, 如果要在程序中获取web应用的资源文件, 需要写一个路径时:
1) 如果写一个相对路径 File file  = new File("conf.properties"); 这时会到程序启动的目录下去寻找这个资源, 对于web应用来说, web应用是在容器中运行的, 这时候就会到tomcat/bin目录下去找这个文件, 找不到!!

(2) 如果写一个绝对路径, 则会到程序启动目录的硬盘根目录去找这个资源文件, 也找不到!!

(3) 写一个盘符开始的硬盘路径, 可以解决这个问题, 但是这种写法一旦换一个发布环境, 路径很可能是错误的. 这种写法也不可取!!

可以通过ServletContext提供的方法来解决这个路径问题: 
sc.getRealPath("xxx"); 传入一个相对于web应用根目录的资源文件的路径, 这个方法会在传入的路径的前面动态的拼接上当前web应用根目录的硬盘路径, 从而拼接出当前资源文件的硬盘路径, 由于web应用的根目录的硬盘路径是动态获取的. 没有写死, 即使换一个发布环境, 也能获取到正确的路径

WEB应用在开发和部署时使用的是不同的路径,开发是在workspace里面,部署是在WEB服务器上,在开发时很可能不知道未来应用部署的绝对路径.在读取一些资源时,需要指定该资源的绝对路径,就需要通过方法来动态获取当前应用所在的绝对路径.解决方案就是通过调用ServletContext对象的getRealPath(path)方法,其中path指定的是相对于Web应用的路径,该方法会返回该资源的绝对路径.

当没有ServletContext对象可以使用时, 可以通过类加载器来加载资源, 类加载器提供了方法来加载资源, 但是要求传入的文件路径必须是相对于类加载器加载类的路径
				getResource(xxx)
				getResourceAsStream(xxx)

```Java
package demo;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
@WebServlet("/context02")
public class ServletContext02 extends HttpServlet {
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取web资源：指的是web应用中所有的文件都是web资源。
        //1.方式一：设置一个相对路径---相对于tomcat/bin而言。
        File file = new File("test.xml");
        //对于web应用
        System.out.println("该文件的绝对路径：" + file.getAbsolutePath());
        //方式二：file设置一个绝对路径
        File file1=new File("D:\\c3p0-config.xml");
        System.out.println("磁盘目录如下："+file1.getAbsolutePath());
        //方式三：类加载器加载web资源
        //获取类加载器
        ClassLoader classLoader = ServletContext02.class.getClassLoader();
        //通过类加载器获得c3p0-config.xml的配置文件
        //如果当前的应用程序是一个web应用，那么会去WEB-INF/classes文件中去找。
    //如果当前程序是Java程序，那么class path是jdk/bin
    //如果当前程序是一个web应用，那么class path是WEB-INF/classes
        String path = classLoader.getResource("c3p0-config.xml").getPath();
        System.out.println("web应用中的资源路径"+path);
        //方式四：servletcontext对象获取资源路径
        ServletContext servletContext = this.getServletContext();
        //该方法需要写入的路径是从/WEB-INF开始写
        String realPath = servletContext.getRealPath("/WEB-INF/classes/c3p0-config.xml");
        System.out.println(realPath);
        //方式五：获取的是当前应用的虚拟路径，web应用的路径:application context ：url资源路径（自己设置的资源路径）
        String contextPath = servletContext.getContextPath();
        String contextPath1 = request.getContextPath();
        //如果资源路径配置了缺省的路径/,那么输出的内容为空
        System.out.println(contextPath);
        System.out.println(contextPath1);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
输出结果：
该文件的绝对路径：D:\software\tomcat7\bin\test.xml
磁盘目录如下：D:\c3p0-config.xml
web应用中的资源路径/D:/software/www.easymall.com/ROOT/WEB-INF/classes/c3p0-config.xml
D:\software\www.easymall.com\ROOT\WEB-INF\classes\c3p0-config.xml
/ttt
/ttt
```

作用：加载全局性的初始化信息，比如web.xml中配置了<context-param></context-param>



#### 十、域对象

![](https://note.youdao.com/yws/api/personal/file/376E483E0705460C973985C8DC5959B1?method=download&shareKey=aa8905d7fb6f10318d874dcdcf41928a)

四大域对象：

request

ServletContext

session

pagecontext
