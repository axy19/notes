[TOC]
# jsp+el表达式+jstl标准标签函数库

#### 一、jsp

##### ####1. jsp基本概念

##### 1.课程目标

​	能够使用jsp的标签技术,完成一个网站商城主界面的开发！！！

##### 2.课程大纲

​	jsp原理

​	jsp的原生态脚本技术：原理性的内容

​	jsp的标签技术：el表达式，jstl标准标签库

##### 3.jsp的入门

###### 1.概念

​	JSP：java server page，服务器端的java代码的页面

​		底层其实是一段java代码，是一段servlet代码！！

​	基本结构：jsp的脚本+html+css+js/jquery

###### 2.JSP的演变过程：

- 早期：只有servlet，没有jsp时

  业务场景：商城主界面，浏览器发送一个主界面的请求，服务器端的servlet给浏览器一个响应，响应的内容是：html+js+css+数据！！这样一个完整的页面。

  缺点：只有servlet的时候，响应的页面java代码与html标签强耦合，不利于程序员的开发与运维工作。因为，如果要修改html页面，那么就会动到原有的java代码。

- JSP：html+css+js+jquery+java代码

  优点：是实现了java代码与html页面的解耦（耦合度大大降低），方便程序员开发和测试，jsp具有自己的脚本语言，正是这种脚本语言才能让java代码与html代码分离。

  缺点：jsp当数据量太庞大时，因为底层是java代码，所以会给服务器造成很大的压力。

###### 3.jsp原理

1. jsp页面。最终加载到tomcat服务器中进行运行时，会编译成两个文件：

   【regist_jsp.java】java文件

   【regist_jsp.class】字节码文件，最终jsp执行的是.class的字节码文件

2. jsp页面底层是一个servlet代码，其中存在常见的对象：request对象，response对象

3. 底层是servlet程序，其中tomcat服务器会自动编译和生成jsp的java文件

###### 4.jsp的编译过程？jsp编译之后会被编译进那个目录

​	IDEA配置tomcat之后，会产生一个idea与tomcat的文件夹：

​	文件夹路径：

​	tomcat的【work】目录：tomcat真正执行的代码的一个目录。最终jsp编译之后的目录：

 【\work\Catalina\localhost\day15_session_war_exploded\org\apache\jsp】

​	编译之后生成两个文件：

​	比如：index.jsp页面，那么编译之后的文件名称是：【index_jsp.java】【index_jsp.class】

###### 5.jsp什么时候会被编译？

​	jsp是在tomcat启动之后就产生编译还是在浏览器发送请求访问jsp页面时才产生编译？？

​	答案：在浏览器访问jsp页面时才会产生编译。

#### 二、jsp代码编写

##### 1.基本语法

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>jsp基本语法</title>
</head>
<body>
    <h1>1.JSP模板元素</h1>
    <spjinan>JSP页面中所有的html元素都是会被编译进入.java文件 </spjinan><br />
    <span>JSP页面中所有的CSS+JS+数据，同样会编译进入.java文件</span><br />
    <span>JSP页面中所有的模板元素，最后会被编译进入servlet.java文件</span><br />
    <hr/>
    <h1>2.JSP的脚本表达式</h1>
    <p>定义一个变量：<%int i=0;%></p>
    <p>常量：<%=123%></p>
    <p>变量：<%= i %></p>
    <p>表达式：<%=1+1%></p>
    <p>关系运算：<%= true&&false %></p>
    <p>脚本表达式：会把执行的运算结果通过out.print(值)打印到浏览器中</p>
    <hr />
    <h1>3.JSP的脚本片段</h1>
    <p>
        <%
            for(i=0;i<10;i++){
                System.out.println(i);
        %>
            <span style="color: #CA141D"><%= i%></span>
            <img src="../img/head/logo.jpg" /><br />
        <%
            }
        %>
    </p>
    <p>JSP的脚本片段：可以进行截断，截断之后，中间可以书写HTML标签以及其他的任何样式。</p>
    <hr />
    <h1>4.JSP的脚本声明</h1>
    <p>"< %"用于声明局部变量，在service方法中有效 <% int j=0; %></p>
    <p>“< %!”是用于声明成员变量，整个类中有效<%! int k=0; %></p>
    <p>定义成员方法：<%! public static void method(){} %></p>
    <p>定义静态代码块： <%! static{}%></p>
    <hr />

    <h1>5.JSP的注释</h1>
    <p>第一种：jsp的注释，只是给jsp程序员看的，浏览器中没有，
        Java文件也没有，页面源代码中也没有<%--这是一个被注释的信息--%></p>
    <p>第二种：纯Java注释，能够被Java文件解析到<% //获取request对象中的参数 %></p>
    <p>第三种：HTML注释能被Java文件解析到，也能在浏览器源码中查看。
        <%--这是HTML页面的注释--%>
        <%-- <%= k%> --%>
    </p>
    <p></p>
</body>
</html>
```

```
<%@ page contentType="text/html;charset=UTF-8" errorPage="errorPage.jsp" buffer="16kb" session="true" import="java.util.Properties" language="java" %>
<html>
<head>
    <title>JSP的page指令</title>
</head>
<body>
    <h1>1.page指令的基本语法</h1>
    <p>"< %@ page"  "%>"  这就是page指令 </p>
    <p> page指令：可以定义[属性=值]的键值对 </p>
    <p> page指令：能够定义[属性=值]的键值对共有12种 </p>
    <p> [ language="java" ] ：表示页面中可以书写java代码 </p>
    <p> [ extends="package.class" ]  ：表示jsp页面可以继承一个父类 </p>
    <p> [ import="{package.class | package.*}, ..." ] :导入java代码需要使用到的包 </p>
    <p> [ session="true | false" ] :表示当前页面是否可以使用session对象
            默认情况下使用的是true
        <%= request.getSession().getAttribute("key") %>
        <%= session.getAttribute("key") %>
    </p>
    <p>
        [ buffer="none | 8kb | sizekb" ] : 表示的是响应流的缓冲区
        out.write()方法输出内容时，设置的缓冲区大小
    </p>
    <p>
        [ autoFlush="true | false" ]：表示自动刷新缓冲区
        默认情况下，是true，不需要程序干预缓冲区的刷新
    </p>
    <p>
        [ isThreadSafe="true | false" ]：表示当前的线程时单线程的
        默认为false
    </p>
    <p>
        <% int i = 1/0; %>
        [ errorPage="relative_url" ] ：表示当前页面报错时，给这页面配置一个500的错误页面
        [ isErrorPage="true | false" ] ：表示错误页面中可以使用exception对象捕获异常
    </p>
    <p>
        [ contentType="mimeType [ ;charset=characterSet ]" | "text/html ; charset=ISO-8859-1" ]
    </p>
    <p>
        [ pageEncoding="characterSet | UTF-8" ]：表示服务器在加载页面时，会使用pageEncoding进行编码
    </p>
    <p>
        [ isELIgnored="true | false" ]：表示是否忽略 el表达式
    </p>
</body>
</html>

```

##### 2.jsp脚本--指令

###### 2.1 page指令

```
<%@ page 共12个属性设置 
	language="java"
	contentType="text/html;charset=utf-8"
	pageEncoding="utf-8"
	session="true/false"
	errorPage="错误页面的连接"
    isErrorPage="true/false" 是否为错误页面
    import="包路径.类路径" 导包
%>
```

###### 2.2 include指令

指的是当前页面包含其他的某一个页面

 <%@ include file="其他页面的路径"   %>
                 这种include不太常用，了解意义即可！！！

```
<%@ include file="../index.jsp" %>
```

###### 2.3 taglib指令

指的是引入一个第三方的 lib库！！！

主要使用在 jstl标准标签库！！！
         <%@ taglib prefix="前缀" uri="标准函数的命名地址"  %>

```
<%@ taglib prefix="c" uri="地址，需要配合相应的jar包即可" %>
```

#### 三、jsp页面的九大隐式对象

​	隐式对象：不是程序员new出来的对象，是我们jsp页面编译生产.java文件时，自动生成的。

​	举例：<% request.getSession.getAttribute(); %>

为什么存在隐式对象？

​	因为让程序员写起来更加方便，因为jsp的底层是一段servlet代码，servlet中的request，response都是自动创建的，所以jsp的隐式对象也是自动创建的。

##### 1.九大隐式对象

```
request（域对象）
response
session（域对象）
application（指的是servletContext对象 域对象）
out
page(页面) 对应的类型：  java.lang.Object.page=this;
config(配置文件对象)
exception（异常）
pageContext（当前页面对象 域对象）
```

```
<p>
    page对象：final java.lang.Object page = this;不常用 <br/>
    【<%=page.getClass()%>】
</p>
<p>
    config对象：final javax.servlet.ServletConfig config;不常用<br/>
    <%--servletConfig对应的配置文件是web.xml中的servlet标签
     <servlet>
        <init-param>
            <param-name>
            <param-value>
        <init-param>
     </servlet>
    --%>
    【<%=config.getClass()%>】
</p>
<p>
    application对象：常用final javax.servlet.ServletContext application;<br/>
    【域对象：<%=application.getClass()%>】
</p>
<p>
    response对象：常用final javax.servlet.http.HttpServletResponse response<br/>
    【<%=response.getClass()%>】
</p>
<p>
    request对象：常用javax.servlet.http.HttpServletRequest request <br/>
    【域对象：<%=request.getRequestURI()%>】

</p>
<p>
    session对象：常用 javax.servlet.http.HttpSession session <br/>
    【域对象：<%=session.getId()%>】
</p>
<p>
    out对象：javax.servlet.jsp.JspWriter out  ; <br/>
    【输出流对象：<%=out.getClass()%>】
</p>
<p>
    exception对象：java.lang.Throwable exception = org.apache.jasper.runtime.JspRuntimeLibrary.getThrowable<br/>
   <%-- <%int i = 1/0;%>--%>
    需要配合其他异常页面完成
    <%--<%=exception%>--%>
</p>
<p>
    pageContext对象：javax.servlet.jsp.PageContext pageContext<br/>
    【域对象：可以获取以上八种隐式对象】<br />
    <%--【域的范围：pageContext <request <session <application】--%>
    <%pageContext.setAttribute("testkey","这是本页面");%>
    【<%=pageContext.getAttribute("testkey")%>】
</p>
```

##### 2.pageContext：入口对象

```
<h1>pageContext对象：作为入口对象获取其他八种隐式对象：</h1>
<p>
    <br/>
    作为入口对象获取其他八种隐式对象：<br/>
    pageContext.getException()=【<%=pageContext.getException()%>】<br/>
    pageContext.getPage()=【<%=pageContext.getPage()%>】<br/>
    pageContext.getRequest()=【<%=pageContext.getRequest()%>】<br/>
    pageContext.getResponse()=【<%=pageContext.getResponse()%>】<br/>
    pageContext.getServletConfig()=【<%=pageContext.getServletConfig()%>】<br/>
    pageContext.getServletContext()=【<%=pageContext.getServletContext()%>】<br/>
    pageContext.getOut()=【<%=pageContext.getOut()%>】<br/>
    pageContext.getSession()=【<%=pageContext.getSession()%>】
</p>
<hr>
<h2>pagecontext对象：作为入口对象,四大域对象属性</h2>
<p>
    request域对象常量PageContext.REQUEST_SCOPE：【<%=pageContext.REQUEST_SCOPE%>】<br/>
    session域对象常量PageContext.SESSION_SCOPE：【<%=PageContext.SESSION_SCOPE%>】<br/>
    application域对象常量PageContext.APPLICATION_SCOPE：【<%=PageContext.APPLICATION_SCOPE%>】<br/>
    page域对象常量PageContext.PAGE_SCOPE：【<%=PageContext.PAGE_SCOPE%>】
</p>
<hr>
<h2>pagecontext对象：操作所有域中属性的方法</h2>
<p>
    <h3>1.pagecontext对象：获取attribute属性键值对</h3>
    <%
        //设置session的属性键值对 key : session-value
        //【】
        pageContext.setAttribute("key","session-value",PageContext.SESSION_SCOPE);
        session.setAttribute("key","session-value");
        pageContext.getSession().setAttribute("key","session-value");
        //设置application的属性键值对 key : application-value
        //【】
        pageContext.setAttribute("key","application-value",PageContext.APPLICATION_SCOPE);
        //设置request的属性键值对 key : request-value
        //【】
        pageContext.setAttribute("key","request-value",PageContext.REQUEST_SCOPE);
        //设置pageContext的属性键值对 key : pageContext-value
        //【】
        pageContext.setAttribute("key","Context-value");

    %>
    /*
        SESSION_SCOPE/APPLICATION_SCOPE/REQUEST_SCOPE/PAGE_SCOPE
    */
    session-key:【<%=pageContext.getAttribute("key",PageContext.SESSION_SCOPE)%>】<br>
    application-key:【<%=pageContext.getAttribute("key",PageContext.APPLICATION_SCOPE)%>】<br>
    request-key:【<%=pageContext.getAttribute("key",PageContext.REQUEST_SCOPE)%>】<br>
    pagecontext-key:【<%=pageContext.getAttribute("key")%>】
    <h3>2.pagecontext对象：查找四大域对象的属性键值对</h3>
    /*
        删除方法：pageContext.removeAttribute
        查找方法：pageContext.findAttribute
    */
    顺序查找1--pageContext：【<%=pageContext.getAttribute("key")%>】<br>
    <%--如果不指定scope他将删除四大域对象的key--%>
    删除pageContext中的key：【<%pageContext.removeAttribute("key",PageContext.PAGE_SCOPE);%>】<br>
    <%--getAttribute()，前提：remove不能删除所有的域，不然无法获取。
    如果不指定scope，他不能自动的去其它域获取key,value--%>
    <%--findAttribute方法，前提：remove不能删除所有的域，不然无法获取
     是自动的逐一搜索四大域对象。从小到大的顺序查找。--%>
    顺序查找2--Request：【<%=pageContext.findAttribute("key")%>】<br>
    删除Request中的key：【<%request.removeAttribute("key");%>】<br>
    顺序查找3--session：【<%=pageContext.findAttribute("key")%>】<br>
    删除session中的key：【<%session.removeAttribute("key");%>】<br>
    顺序查找4--application：【<%=pageContext.findAttribute("key")%>】<br>
    删除application中的key：【<%application.removeAttribute("key");%>】<br>
    如果都找不到：<%=pageContext.findAttribute("key")%><br>
    <h3>3.pageContext对象：作为域对象使用：默认查找顺序即可</h3>
    <%request.setAttribute("key1", "value1");%>
    【<%=pageContext.findAttribute("key1")%>】
    <h3>4.pagecontext对象：跳转到其他资源 forward</h3>
    <%--【<%pageContext.forward("jsp04.jsp");%>】--%>
</p>
```

#### 四、jsp原生态标签

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>JSP标签：由sun公司提供，属于jsp规范中的内容，不需要引入第三方标签库，我们介绍其中的三个。</h1>
    <%--原生标签的基本格式：<jsp：标签名></jsp:标签名>--%>
    <p>
        jsp:include:【<jsp:include page="jsp03.jsp"></jsp:include>】
        等价于：request.getRequestDispatcher("/_head.jsp").include(request, response);

    </p>
    <p>
        <%--页面在进行跳转时，可以携带param参数进行跳转--%>
        jsp:forward:【<jsp:forward page="jsp03.jsp">】
            jsp:param:【<jsp:param name="username" value="123"/>】
                      【<jsp:param name="name" value="xsz"/>】
        【</jsp:forward>】
    </p>
</body>
</html>
```

#### 五、EL表达式

##### 1.EL

###### 1.1 概述

```
<p>
    EL 全名为Expression Language，用来替代"< % ="  "%>"脚本表达式 <br>
    EL具有获取数据、执行运算、获取常用开发对象、调用java方法这四方面的功能
</p>
```

###### 1.2 pojo

```java
/*
 * pojo指的是一个简单的javaBean
 * javaBean指的是一个java类中具有以下基本的结构：
 *       1.成员变量
 *       2.构造方法
 *       3.getter和setter
 *       4.toString()
 * 通常：在pojo包中存在的javaBean,我们称之为对象模型（类）。
 * */
```

##### 2.获取常量

书写方式${}取代<%=%>

```
<p>
    <%--\${}相当于EL表达式的注释}--%>
    \${"张三"}:【${"张三"}】<br/>
    \${123}:【${123}】<br/>
    \${true}:【${true}】
</p>
```

##### 3.获取变量

###### 3.1 在四大作用域中获取变量

```
<p>
    <h2>2.1 在四大作用域查找属性</h2>
    <%
        session.setAttribute("key1", "session-value");
        application.setAttribute("key2", "application-value");
        request.setAttribute("key3", "request-value");
        pageContext.setAttribute("key4", "pagecontext-value");
    %>
    \${key1}:【${key1}】<br/>
    \${key2}:【${key2}】<br/>
    \${key3}:【${key3}】<br/>
    \${key4}:【${key4}】
</p>
```

###### 3.2 获取指定域中的属性

```
<p>
    <h2>2.2 获取指定域中的属性</h2>
    <%
        session.setAttribute("hello1", "session-value");
        application.setAttribute("hello1", "application-value");
        request.setAttribute("hello1", "request-value");
        pageContext.setAttribute("hello1", "pagecontext-value");
    %>
    \${hello1}:【${hello1}】<br/>
    \${requestScope.hello1}:【${requestScope.hello1}】<br/>
    \${sessionScope.hello1}:【${sessionScope.hello1}】<br/>
    \${applicationScope.hello1}:【${applicationScope.hello1}】
</p>
```

###### 3.3 获取数组中的数据

```
<p>
    <h2>2.3 获取数组中的数据</h2>
    <%
        String[] strings = {"一","二","三","四"};
        pageContext.setAttribute("arr", strings);
    %>
    <%--<%=strings[7]%> 原生态写法，数组越界会产生异常
       当前就会输出[]    --%>
    \${arr[0]}:【${arr[0]}】<br/>
    <%--EL表达式：遇到null或者数组越界时，会自动处理结果，不会出现异常。--%>
    \${arr[7]}:【${arr[7]}】
</p>
```

###### 3.4 获取集合中的数据

```
<p>
    <h2>2.4 获取集合中的数据</h2>
    <%
        List<String> lists = new ArrayList<String>();
        lists.add("刘备");
        lists.add("关于");
        lists.add("张飞");
        lists.add("曹操");
        pageContext.setAttribute("lists", lists);
    %>
<%--会自动进行null处理，不会出现异常--%>
    \${lists[0]}:【${lists[0]}】<br/>
    \${lists[3]}:【${lists[3]}】<br/>
</p>
```

###### 3.5 获取map中的数据

```
<p>
    <h2>2.5 获取map中的数据</h2>
    <%
        Map<String,String> map=new HashMap<String,String>();
        map.put("name", "刘备");
        map.put("age", "30");
        map.put("wife", "糜夫人");
        map.put("wife.another", "孙尚香");
        pageContext.setAttribute("map", map);
        pageContext.setAttribute("wife", "name");
    %>
    \${map.name}:【${map.name}】<br/>
    \${map.age}:【${map.age}】<br/>
    \${map.wife}:【${map.wife}】<br/>
    <%--\${map.wife.another}:${map.wife.another}<br/>--%>
    \${map["wife.another"]}:【${map["wife.another"]}】<br/>
</p>
```

###### 3.6 获取javabean的属性

```
<p>
    <h2>2.6 获取javabean属性</h2>
    <%
        Person p1=new Person();
        p1.setName("张三丰");
        p1.setAge(120);
        p1.setAddr("武当山");
        pageContext.setAttribute("p1", p1);
        Person p2=new Person();
        p2.setName("张无忌");
        p2.setAge(12);
        p2.setAddr("明教");
        pageContext.setAttribute("p2", p2);
    %>
    <%--此处的p1指的是setAttribute()当中的key，并不是变量名p1
    前提是，必须要把前边设置的数据进行共享,才能获取到对应的值
    --%>
    \${p1}:【${p1}】<br>
    \${p1.name}:【${p1.name}】<br>
    \${p1.addr}:【${p1.addr}】<br>
    \${p1.age}:【${p1.age}】<br>
</p>
```

##### 4.获取数据细节

###### 4.1 关系运算符：lt(小于)  gt(大于) ne eq

```
<p>
    <h3>3.1关系运算：lt  gt  ne eq </h3>
    <%--可以直接用大于小于的符号但是不建议--%>
    1&lt;2=【${1 lt 2}】<br>
    1&gt;2=【${1 gt 2}】<br>
    <%
        pageContext.setAttribute("p1", new Person());
        pageContext.setAttribute("p2", new Person());
    %>
<%--eq相当于equals()--%>
    p1==p2? 【${p1 eq p2}】<br>
    p1!=p2? 【${p1 ne p2}】
    <hr>
</p>
```

###### 4.2 empty运算符

```
<h3>3.2empty运算符</h3>
<%
    // 判断对象是否为null
    //Person p3=null;
    Person p3=new Person();
    pageContext.setAttribute("p3", p3);
    // 判断字符串是否为空串
    pageContext.setAttribute("str", "");
    // 判断数组是否没有初始化
    String[] array=new String[10];
    pageContext.setAttribute("array",array);
    // 判断集合中没有添加任何元素
    List<String> list=new ArrayList(20);
    // 判断作用域中是否有元素
    // pageScope/requestScope/sessionScope/
    // applicationScope
    // 以上4个对象代表的是四大作用域的集合
    pageContext.setAttribute("list", list);
    // application.setAttribute("123", 123);
    // application.removeAttribute("123");
    // session.setAttribute("111", 111);
    session.removeAttribute("111");
%>
判断对象是否为null：【${empty p3}】<br>
判断字符串是否为空串：【${empty str}】<br>
判断数组是否没有初始化：【${empty array}】<br>
判断集合中没有添加任何元素：【${empty lists}】<br>
<%--session是指这个对象是不是为空
    sessionScope是指当前作用域中是不是空--%>
判断作用域中是否有元素：【${empty sessionScope}】<br>
</p>
```

###### 4.3 三目运算符

```
<p>
    <h3>三目运算符 布尔表达式?表达式1:表达式2</h3>
    1>2? 1 : 2【${1 gt 2 ? 1:2}】
    <hr>
</p>
```

###### 4.4 总结

```
<h1>4.获取数据细节</h1>
<p>
    <h3>.和[]区别：使用点的地方都可以用中括号，
    如果属性名是数字或包含特殊符号（.-）就必须使用中括号。
    如果要动态取值要用[]来做，.不可以动态取值。
    例子：\${map["first.name"]}</h3>
    <h3>EL只能获取不能设置</h3>
    <h3>EL只能获取不能遍历</h3>
</p>
```

##### 5.进行简单运算

##### 6.获取web开发常用对象

###### 6.1 常用对象

 El中预先定义了11个内置对象，不需要提前存入域中个，可以直接在el使用
 pageScope,requestScope,sessionScope,applicationScope
            --- 获取jsp中的域对象中的数据
param,paramValues   -- 接收参数.
             --- 相当于request.getParameter()    request.getParameterValues()
header,headerValues   -- 获取请求头信息
            --- 相当于request.getHeader(name)
initParam   -- 获取全局初始化参数
            --- 相当于    this.getServletContext().getInitParameter(name)
cookie  -- WEB开发中cookie
            --- 相当于request.getCookies()---cookie.getName()---cookie.getValue()
pageContext  ---WEB开发中的pageContext.
            --- pageContext获得其他八大对象

###### 6.2 4大作用域上的map集合的4个对象

```
<p>
    <h3>5.1.代表了4大作用域上的map集合的4个对象</h3>
        <!-- 4大作用域对象 pageContext,request,session,application -->
        <!-- pageScope/requestScope/sessionScope/++++++ -->
        <!-- 上面这4个对象本身就是map集合，分别代表了4大作用域对象身上的map集合 -->
        <%
            pageContext.setAttribute("fruit", "apple");
            pageContext.setAttribute("fruit", "orange", PageContext.REQUEST_SCOPE);
            pageContext.setAttribute("fruit", "banana", PageContext.SESSION_SCOPE);
            pageContext.setAttribute("fruit", "watermelon", PageContext.APPLICATION_SCOPE);
            pageContext.setAttribute("name", "张三");
        %>
        pageContext:【${pageScope.fruit}】<br>
        request:【${requestScope.fruit}】<br>
        session:【${sessionScope.fruit}】<br>
        application:【${applicationScope.fruit}】<br>
        name:【${pageScope.name}】<br>
</p>
```

###### 6.3 请求参数组成的map

```
<p>
    <h3>5.2.请求参数组成的map</h3>
    <h3>代表了请求参数的map集合的2个对象 param paramValues</h3>
     name=<%=request.getParameter("name") %><br>
    <%--原生态写法没有处理对象为null时，抛出的异常--%>
    <%--like=<%=request.getParameterValues("like")[0] %><br>
    like=<%=request.getParameterValues("like")[1] %><br>
    like=<%=request.getParameterValues("like")[2] %><br>--%>
    name=【${param.name}】<br>
    like=【${paramValues.like[0]}】<br>
    like=【${paramValues.like[1]}】<br>
    like=【${paramValues.like[2]}】<br>
<hr>
</p>
```

###### 6.4 请求头组成的map

```
<p>
    <h3>5.3.请求头组成的map</h3>
    <h3>代表了请求头数据的map集合的2个对象 header headerValues</h3>
    host=【${header.host}】<br>
    cookie=【${headerValues.cookie}】<br>
    <hr>
    <hr>
</p>
```

###### 6.5 web应用初始化参数组成的map

```
<p>
    <h3>5.4.web应用初始化参数组成的map</h3>
    <h3>代表了所有Cookie的map集合: cookie.JSESSIONID.</h3>
    <%
        Cookie[] cs=request.getCookies();
        Cookie c1=cs[0];
        String name=c1.getName();
        String value=c1.getValue();
    %>
    cookie-name=【${cookie.JSESSIONID.name}】
    cookie-value=【${cookie.JSESSIONID.value}】
    <hr>
</p>
```

###### 6.6 web.xml中配置的全局变量:initParam

配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>hellokey</param-name>
        <param-value>hellovalue</param-value>
    </context-param>
    <context-param>
        <param-name>job</param-name>
        <param-value>work</param-value>
    </context-param>

</web-app>
```

jsp脚本：需要重启猫才能将配置进去，然后获取到。

```
<p>
    <h3>5.5.web.xml中配置的全局变量:initParam</h3>
    <h3>代表了web.xml中配置的初始化参数</h3>
    hellovalue=【${initParam.hellokey}】<br>
    job=【${initParam.job}】<br>
</p>
```

#### 六、jstl

##### 1.概念

- jstl:提供给java web 开发人员一个标准标签函数库，
- 作用：替代了jsp的原生脚本<%可以写任意的java代码语句%>
- jstl是第三方公司开发的一个jar包，使用时必须先导入jar包，该jar包就是封装好的taglib函数库

##### 2.jstl的项目搭建

​	jstl使用时必须先导入jar包，该jar包就是封装好的taglib函数库

​		包的资源【day16/课后资料/两个jar包】

​		jsp页面的taglib:

```
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

##### 3.jstl的基本使用

* 默认的前缀是：c

  <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

* 程序员可以自定义前缀名称，比如hello

  <%@taglib prefix="hello" uri="http://java.sun.com/jsp/jstl/core" %>

* 特别注意：使用默认的前缀即可，如果是程序员自定义会导致代码的可阅读性较差。

##### 4.jstl的标签划分

​	共有12个标签：每个标签代表者java中常用的语法结构，每个标签都能够替代<%%>的形式。

###### 4.1 c:out标签

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>c:out标签库 输出</h1>
    <h3>1. 输出常量</h3>
    123:【<c:out value="123"></c:out>】<br>
    abc:【<c:out value="abc"></c:out>】<br>
    true:【<c:out value="true"></c:out>】<br>
    <h3>2. 输出变量</h3>
    <%
        int i=10;
        pageContext.setAttribute("j", 20);
    %>
    <%--int i=10时，只是定义一个局部变量
      正常情况下，引用变量，el表达式主要是获取域对象中的值。--%>
    i=【<c:out value="<%=i%>"></c:out>】<br>
    j=【<c:out value="${j}"></c:out>】<br>
    <h3>3. 转义输出：escapeXml="false"</h3>
    <%--超链接的基本形式：<a href="http://www.baidu.com">百度一下</a>--%>
    字符串输出：【<c:out value="<a href='http://www.baidu.com'>百度一下</a>"></c:out>】<br>
    超链接：【<c:out value="<a href='http://www.baidu.co'>百度一下</a>" escapeXml="false"></c:out>】<br>
    <h3>4. 输出默认值：default</h3>
    <%
        pageContext.setAttribute("a",333);
        pageContext.setAttribute("b",666);
    %>
    <%--获取不到aaa的时候给他一个默认值444
        获取到aaa时就会输出aaa对应的值。--%>
    a=【<c:out value="${a}" default="444"></c:out>】
    b=${empty b?"12345":b}
</body>
</html>
```

###### 4.2 c:set标签

set标签中：共有五个属性

var：变量，指的是setattribute的key

scope：域对象的范围

property：对象中属性的名称，map中的key

value：对象中属性的值，map中的value

target：表示目标对象。

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>c:set标签 赋值</h1>
    <h3>向四大作用域中增加域属性 page/request/session/application</h3>
    <%--
        var  value scope
        value:绑定的键值对的值
        var：绑定的键值对的key
        scope：指的是四大域对象的范围。pagecontext,request,session,application,
    --%>
    <c:set value="安心" var="username" scope="request"></c:set>
    <c:set var="name" value="林黛玉" scope="request"></c:set>
    name=【${name}】<br>
    <hr>
    <h3>向map中增加、修改键值对</h3>
    <%
        Map<String,String> map=new HashMap<String,String>();
        map.put("name", "刘备");
        map.put("wife", "甘夫人");
        pageContext.setAttribute("map", map);
    %>
    <!-- 修改键值对的值 -->
    <%--
        property：指的是map中的key
        value:map中的key对应的value值
        target:需要操作的map对象
    --%>
    【<c:set property="name" value="张三" target="${map}"></c:set>】
    name=${map.name}<br/>
    wife=${map["wife"]}<br>
    <!-- 向map集合中添加键值对 -->
    【<c:set property="son" value="张1" target="${map}"></c:set>】
    son=${map["son"]}<br>

    <hr>
    <h3>修改javabean的属性</h3>
    <%
        Person p1=new Person("林黛玉",18,"大观园");
        pageContext.setAttribute("p1", p1);
    %>
    <%--
        property
        value
        target
    --%>
    【<c:set property="name" value="jjjj" target="${p1}"></c:set> 】
    p1=${p1}
    <hr>
    <br><br><br><br><br>
</body>
</html>
```

###### 4.3 c:remove标签

删除域对象中的attribute键值对属性

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>c:remove标签 删除作用域中的值</h1>
    <h3>1. 删除指定作用域中的值</h3>
    <c:set var="city" value="北京" scope="page"></c:set>
    <c:set var="city" value="太原" scope="request"></c:set>
    <c:set var="city" value="石家庄" scope="session"></c:set>
    <c:set var="city" value="青岛" scope="application"></c:set>
    city=${city}<br>
    删除page：【<c:remove var="city" scope="page"></c:remove> 】
    city=${city}<br>
    删除request：【<c:remove var="city" scope="request"></c:remove> 】
    city=${city}<br>
    删除session：【<c:remove var="city" scope="session"></c:remove> 】
    city=${city}<br>
    删除application：【<c:remove var="city" scope="application"></c:remove>】
    city=${city}<br>
    <hr>
    <h3>2. 如果不指定scope，则删除4大作用域中所有该name的值</h3>
    <c:set var="city" value="北京" scope="page"></c:set>
    <c:set var="city" value="太原" scope="request"></c:set>
    <c:set var="city" value="石家庄" scope="session"></c:set>
    <c:set var="city" value="青岛" scope="application"></c:set>
    city=${city}<br>
    不指定scope，删除所有：【<c:remove var="city"></c:remove>】
    city=${city}
    <hr>
    <br><br><br><br><br>
</body>
</html>
```

###### 4.4 c:catch标签

catch标签：用于捕获异常，能够自动识别异常的类型，可以打印输出。该标签不常用。

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>c:catch标签 捕获异常</h1>
    <h3>1. 捕获异常</h3>
    <%
        try{
            int i=10/0;
        }catch(ArithmeticException e){
            pageContext.setAttribute("e", e);
        }
    %>
    <%--e1指的是异常的名字，已经封装好了--%>
    <c:catch var="e1">
        <%
            int j=10/0;
        %>
    </c:catch>
    e1=${e1}
    e=${e}<br>

    <c:catch var="e1" >
        <%
            Person p1=null;
            p1.getName();
        %>
    </c:catch>
    e1=${e1}<br>
    <hr>
    <br><br><br><br><br>
</body>
</html>
```

###### ==4.5 c:if标签，执行判断==

```
特别注意：不能出现额外的空格，而别是在if语句的test中判断。
```

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>c:if标签 执行判断</h1>
    <c:set var="user" value="admin" scope="session"></c:set>
    <%--<c:remove var="user"/>--%>
    <%--模拟一个场景，登录之后，显示登录的名字--%>
    <%--如果session中存在user，显示欢迎回来
        如果session中不存在user显示登录。--%>
    <c:if test="${not empty sessionScope.user}" scope="session" var="flag" >
        flag=${flag}<br/>
        <c:if test="${flag}">
            欢迎：【${sessionScope.user}】回来
        </c:if>
    </c:if>
    <c:if test="${empty sessionScope.user}" scope="session" var="flag">
        flag=${flag}<br/>
        <c:if test="${flag}">
            登录
        </c:if>
    </c:if>
    <hr>
    <br><br><br><br><br>
</body>
</html>
```

###### ==4.6 c:choose标签--对应swith case==

```
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>c:choose c:when c:otherwise标签 模拟switch-case</h1>
    <%
        pageContext.setAttribute("day", 12);
    %>
    <c:set var="day" value="12" scope="page"></c:set>
    <c:choose><%--相当于switch--%>
        <c:when test="${day==1}"><%--相当于每一个case--%>
            <span style="color: bisque">星期一</span>
        </c:when>
        <c:otherwise><%--相当于default--%>
            <span style="color: red">非法日期</span>
        </c:otherwise>
    </c:choose>
    <hr>
    <br><br><br><br><br>
</body>
</html>
```

###### ==4.7 c:foreach标签--实现循环==

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>c:forEach标签 实现循环</h1>
<h3>增强for循环</h3>
    <%
        List<String> list=new ArrayList<String>();
        list.add("宋江");
        list.add("吴用");
        list.add("卢俊义");
        list.add("武松");
        pageContext.setAttribute("list", list);
    %>
<%--items:指的是要遍历的集合名
    var：代表变量名。--%>
    <c:forEach items="${list}" var="str">
        name=${str}<br/>
    </c:forEach>
    <hr>
    <h3>普通for循环</h3>
    <%--
    varStatus:变量名是程序员自定义的，
    代表当前循环的状态：index（索引从0开始），
    count（循环的次数从1开始），first（循环的第一个参数）
    --%>
    <c:forEach begin="0" end="10" step="1" varStatus="s">
        <span>hello jstl</span> index=${s.index};count=${s.count}; first=${s.first}<br/>
    </c:forEach>
    <hr>
    <h3>遍历10到100的偶数，如果数字所在的位置是3的倍数，
        显示成红色</h3>
    <c:forEach begin="10" end="100" var="temp" varStatus="vs">
        <%--特别注意： 不能出现额外的空格，而别是在 if语句的test判断中  --%>
    <c:if test="${temp%2==0}" var="flag">
        <c:if test="${vs.count%3==0}">
            <span style="color: red">${vs.count}  ${temp}</span><br/>
        </c:if>
        <c:if test="${vs.count%3!=0}">
            <span style="color: springgreen">${vs.count}  ${temp}</span><br/>
        </c:if>
    </c:if>
    </c:forEach>
    <br><br><br><br><br>
</body>
</html>
```

###### ==4.8 c:forTokens标签--实现拆分字符串并遍历==

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--
  Created by IntelliJ IDEA.
  User: tarena
  Date: 2019/12/23
  Time: 1:04
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>c:forTokens标签 实现拆分字符串并遍历</h1>
    <%
        String str="1,admin,123,超级管理员,1@163.com";
        pageContext.setAttribute("str", str);
        String[] split = str.split("");
        for(String str1: split){
            System.out.println(str1);
        }
    %>
  <%--特别注意：Java代码中的str.split("")表示把每一个字符都拆分出来
  标签中 delims=“”表示不做任何拆分--%>
<c:forTokens items="${str}" delims="," var="ss">
    结果：${ss}<br/>
</c:forTokens>
    <br><br><br><br><br>
</body>
</html>
```

