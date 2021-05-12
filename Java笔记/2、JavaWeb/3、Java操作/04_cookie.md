[TOC]
# cookie+session

#### 一、会话技术

##### 1.会话技术的应用范畴

​	不仅仅是 java web存在，其他的编程语言也存在，只是写法不同而已。

##### 2.会话的概念

​	一次会话中多次请求和多次响应的一个过程叫做会话。

​	一次会话：打开浏览器，访问了某一个网站，直到你手动关闭浏览器或者网站服务器关闭时，会话才结束。

![](https://note.youdao.com/yws/api/personal/file/EE210CD174E746B5931A16B359A3E7C2?method=download&shareKey=3ecb597f1f225cda9846885dcd506d3b)

##### 3.会话的功能，为什么会有会话技术存在？

​	一次会话中，多次请求和响应过程中，产生了大量的数据，会话技术是让这些数据，在不同的请求过程中，实现数据的共享。

​	之前学习的request域对象，servletcontent域对象不合适。

##### 4.cookie

​	客户端（浏览器）的会话技术。

​	安全性比较低，一般存储不重要的东西，比如记住用户名。

​	负责的共享数据是不需要安全加密的数据。

##### 5.session

​	服务器端的会话技术。		

​	安全性较高，存储重要的东西，比如登录的用户信息，验证码。

​	负责的共享数据是：用户的信息，相关的加密的数据

![](https://note.youdao.com/yws/api/personal/file/EF86D08780534F858FABC3B45CC40FA0?method=download&shareKey=ca1d41cd158b30cf013bf9abd771c422)

#### 二、cookie--不是域对象

==cookie是在浏览器端存储但是数据是在服务器端做处理！！==

##### 1.快速入门

​	创建一个cookie对象（服务器端的代码书写）

​	cookie对象设置一些共享数据（服务器端的代码书写）

​	浏览器负责把cookie保存起来（response响应给浏览器，浏览器才能保存）

​	获取cookie中的信息（服务器端的代码书写）

```Java
package cookie;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/cookie01")
public class Cookie01 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        //1.创建一个cookie对象，并设置数据
        //参数是key=value的键值对
        Cookie c=new Cookie("msg","hello");
        //2.把cookie响应给浏览器端,
        // 其中响应过程是服务器与浏览器自动完成的，不需要程序员干预
        response.addCookie(c);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

```Java
package cookie;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/cookie02")
public class Cookie02 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        //获取浏览器端的cookie数据
        //通过request请求获取cookie
        Cookie[] cookies = request.getCookies();
        
        if(cookies!=null&&cookies.length>0){
            for (Cookie c:cookies) {
                String name = c.getName();//获取cookie中的key
                String value = c.getValue();//获取cookie中的value值
                System.out.println(name+","+value);
            }
        }
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

##### 2.cookie的原理 

基于Set-cookie与cookie对象实现的

​	2.1 response.addCookie(Cookie对象)

​		response响应头（HTTP协议）：Set-cookie:msg="hello"

​	2.2 浏览器接收到响应头：Set-cookie

​		意味着：浏览器知道set-cookie的意思：是要把cookie存起来

​		存储的位置是：谷歌浏览器的浏览器缓存中

​	2.3 浏览器发送了另外一个请求:/cookie02

​		request请求头（HTTP协议）：Cookie :msg="hello"

​		意味着：浏览器发送请求时，自动从cookie缓存中，拿到cookie的数据，然后封装到request请求中，发送给服务器端。

​	2.4 服务器端获取数据：request.getCookies();

​		通过request请求获取请求中的cookie参数

​	2.5 因此，cookie数据实现了不同请求之间的数据共享。

![](https://note.youdao.com/yws/api/personal/file/E55BCBCC34B5458AA7CF9B77C4FCF2E6?method=download&shareKey=27bd9409c3b5fa5586d43435c8e7c1bf)

##### 3.Cookie中的细节问题

###### 	3.1 浏览器关闭后，再次打开，直接获取cookie，能否获取到？

​		不能，因为浏览器关闭，本次会话结束，浏览器缓存中的cookie会默认清空，所以再次打开浏览器/cookie，直接而从浏览器中拿不到数据。

###### 	3.2 一次使用response发送多个cookie对象给浏览器？

​		可以，

​		方案：

 		 //1.创建一个cookie对象，并设置数据
        //参数是key=value的键值对
        Cookie c=new Cookie("msg","hello");
        Cookie c1=new Cookie("msg1","hello1");
        Cookie c2=new Cookie("msg2","hello2");
        Cookie c3=new Cookie("msg3","hello3");
        Cookie c4=new Cookie("msg4","hello4");
        //2.把cookie响应给浏览器端,
        // 其中响应过程是服务器与浏览器自动完成的，不需要程序员干预
        response.addCookie(c);
        response.addCookie(c1);
        response.addCookie(c2);
        response.addCookie(c3);
        response.addCookie(c4);

###### 	3.3 cookie可以在浏览器中保存多长时间？

​		默认情况下，浏览器关闭，cookie被销毁；cookie持久化技术可以让cookie的生存时间变长，比如时间设置为24个小时，那么cookie就能存在24个小时，期间无论浏览器是否关闭，cookie一直存在。

​		解决方案：cookie.setMaxAge(int seconds);//seconds 秒数

​		c.setMaxAge(60*2);//2分钟
​		c1.setMaxAge(60*60);//1小时
​		c2.setMaxAge(60*60*24);//1天

​		int  seconds:正数：把数据持久化到硬盘中，存活多长时间

​							负数：默认-1，表示不使用缓存存储cookie

​                             0：表示直接删除一个cookie

​		模拟删除cookie的场景：

​		1.先添加一个cookie，并响应给浏览器，也就是说cookie经过第一次请求访问，得到响应结果

​		2.浏览器发送另一个请求，获取第一步中存储的cookie并进行cookie的修改，cookie.setMaxAge(0);属性改变之后，cookie对象发生变化，需要重新响应给浏览器 

​        respone.addCookie(cookie)

​		3.重新发送请求，获取所有的cookie发现，第二步的cookie没有了

###### 	3.4 cookie对象中，设置键值对时，value能够为中文？

​		可以支持中文。

​		支持版本的问题：tomcat8之前，不包括8，直接使用中文会报错（500错误）。

​		                              tomcat8之后，包括8，可以直接使用中文。

​		模拟500错误：创建类Cookie05.java使用中文进行键值对赋值

​		HTTP Status 500 - Control character in cookie value or attribute.

​		出现的原因：cookie对象的value值默认只支持英文，对中文不识别。编码格式的问题。

​		解决方案：

​		1.对中文先进行编码：URLEncoder.encode(value,charset);

```java 
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        //1.创建一个cookie对象
        Cookie c=new Cookie("username","安冉");
        //1.1获取cookie中的value值
        String value = c.getValue();
        System.out.println(value);
        //1.2对cookie的value值进行编码
        String newvalue = URLEncoder.encode(value, "utf-8");
        System.out.println("编码之后"+newvalue);
        //1.3把newvalue赋值给cookie
        c.setValue(newvalue);
        //2.直接响应给浏览器
        response.addCookie(c);
    }
```

​	2.对cookie中的value值，获取之后进行解码操作

```Java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    request.setCharacterEncoding("utf-8");
    response.setContentType("text/html;charset=utf-8");
    //获取浏览器端的cookie数据
    //通过request请求获取cookie
    Cookie[] cookies = request.getCookies();

    if(cookies!=null&&cookies.length>0){
        for (Cookie c:cookies) {
            String name = c.getName();//获取cookie中的key
            String value = c.getValue();//获取cookie中的value值
            System.out.println("解码之前："+value);
            value = URLDecoder.decode(value, "utf-8");
            System.out.println(name+","+value);
        }
    }
}
解码之前：%E5%AE%89%E5%86%89
username,安冉
```

###### 	3.5 Cookie的范围有多大？？

​		1.一个tomcat服务器中，有两个web工程，比如：day14-01,day14-02，day14-01中生成的cookie，被存在于浏览器端，那么day14-02中能否能够获取cookie？？

​       答案：==默认情况下，多个web应用之间不能共享数据==

​      解决方案：1.找到cookie05先打印输出cookie.getPath()；重新设置cookie的路径cookie.setPath("/");

​                     2.复制cookie02到day14-02中cookie包中

```
System.out.println(c.getPath());
c.setPath("/");//表示同一个域名下所有的web应用都可以访问
```

​	2.cookie可以跨服务器访问吗？

​    	可以

   	解决方案：1.前提条件，一级域名必须一致： .baidu.com

​						  2.在原有的cookie对象上添加下边的方法

   							c.setDomain(".baidu");//可以进行跨服务器访问

##### 4.总结

​	Cookie的特点和作用：

​	特点：

​		1.Cookie的数据是存在客户端浏览器中

​		2.浏览器对单个cookie的大小有限制4kb，浏览器对同一个域名下能够最大存放20个cookie

​	作用：

​		1.Cookie存储少量的，一般的，非隐私的数据

​		2.在不登录的情况下，完成服务器对客户端的身份识别。

​			模拟：谷歌浏览器打开京东存在购物车的信息，ie浏览器不存在购物车信息。

##### 5.案例

​	使用Cookie完成登录页面中的记住用户名的操作。

​	准备工作：

​       1.找到登录页面的资源【day14-cookie-AM\资源\页面】

​       2.把这两个页面复制到【Easymall/web】

​       3.先进性【project structure】-->【source&resource】切换下，重启tomcat

​	项目分析：

​		1.【记住用户名】

​	项目开发步骤：

![](https://note.youdao.com/yws/api/personal/file/17AC4719B56548BCA946D43DBF16A733?method=download&shareKey=6ed0f6e0185331190a75c768500283e7)

​		1.服务器端代码创建一个servlet，Loginservlet.java

```java
package servlet;

import cn.tedu.utils.C3P0Util;
import cn.tedu.utils.WebUtils;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.net.URLEncoder;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        //1.获取页面中的form表单的数据 username password remname
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        String remname = request.getParameter("remname");
        System.out.println(username+password+remname);
        //2.判断remname是否勾选。是否为null
        if(WebUtils.isNull(remname)){
            //2.1如果为null，表示不记住用户名，
            // 删除原有的cookie，或者如果没有不需要生成
            Cookie c=new Cookie("username","");
            c.setMaxAge(0);
            response.addCookie(c);
        }else{
            //2.2如果不为null，表示记住用户名
            // 原来有cookie，需要把username重新赋值给cookie
            //如果原来没有cookie，需要生成一个新的cookie，响应给浏览器
            Cookie c=new Cookie("username","");
            //进行utf-8的编码,防止中文时出现错误
            String username1=URLEncoder.encode(username,"utf-8");
            c.setValue(username1);
            c.setMaxAge(60*60*24*30);//存活一个月
            c.setPath("/");
            response.addCookie(c);
        }
        //3.C3P0连接池：查询数据库是否存在该用户啊
        Connection connection = C3P0Util.getConnection();
        PreparedStatement ps=null;
        ResultSet rs=null;
        try {
            ps=connection.prepareStatement("select * from user where username=? and password=?");
            ps.setString(1,username);
            ps.setString(2,password);
            rs=ps.executeQuery();
            if(rs.next()){
                //3.1如果存在:表示登陆成功，重定向到主界面【index.jsp】
                response.sendRedirect("/index.jsp");
            }else{
                //3.2如果不存在，表示登录失败，请求转发到login.jsp，给出提示信息即可
                request.setAttribute("errorMsg","用户名或者密码不正确");
                request.getRequestDispatcher("/login.jsp").forward(request,response);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            C3P0Util.close(connection,ps,rs);
        }
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

​		2.客户端代码编写，编写login.jsp页面

​			2.1 在login.jsp中，编写一部份Java代码

​				主要目的：从浏览器的cookie中获取username

   	     2.2 那username的值回显到用户名文本框

​			2.3根据username是否为null，回显【记住用户名】的复选框

```java
<%
   //1.获取所有的cookies信息
   Cookie[] cookies = request.getCookies();
   String username="";//全局变量用于回显
   //2.判断其中是否有username的cookie
   if(cookies!=null&&cookies.length>0){
      for (Cookie c:cookies){
         if("username".equals(c.getName())){
            String value=c.getValue();
            value=URLDecoder.decode(value,"utf-8");
            username=value;
            System.out.println("cookie中的名字："+username);
            break;
         }
      }
   }
%>
```

```
<td colspan="2">
   <input type="checkbox" 
   <%="".equals(username)?"":"checked='checked'" %>
   name="remname" value="true"/>记住用户名
   <input type="checkbox" name="autologin" value="true"/>30天内自动登录
</td>
```

#### 三、session--是域对象

##### 	1.session的概念

​		域对象：request对象，servletContext对象,session对象

​		域对象：tomcat服务器接受请求时，会自动创建一些对象：servlet HttpServletRequest,HttpServletResponse,他们都能共享数据

共同特征：全都不是程序员自己创建的，都是由tomcat服务器创建的。

​		session是一个域对象

​		seesion是服务器端数据存储技术，实现了一次会话中，多次请求多次响应之间的数据共享

​		session存储的数据相对安全，不会泄露相关的信息。

##### 	2.session作用域

​		域的范围：request(请求转发中)<session（一次会话中）<ServletContext（整个web应用中）

​	在一次会话中多次请求，多次响应，在整个会话过程中，session一直存在于服务器端，存放的数据更加的隐私

##### 3.session的应用场景

​	一般用于记录登录用户的相关信息，比如：用户id，或者是user对象

##### ==4.session的基本操作==

​		与request对象相似：

​		session.setAttribute(key,value);

   	session.getAttribute(key);

​		session.getAttributeNames();

​		session.removeAttribute();

​	   sesssion.invalidate();//清空session

##### 5.总结

1. session是服务器端技术，他的操作以及生成都是后端代码。
2. session的存储也是存储在服务器端的。
3. request.getSession();是与request对象相关，与response响应无关。

##### ==6.原理：查看底层api源码==

​	session对象是基于JSESSIONID的cookie工作的。

​	第一次请求时：生成的session对象的id，会响应到cookie中，

​    Set-Cookie:JSESSIONID=4561215141;

​	第n次请求时，通过JSESSIONID去匹配获取session对象

​	那么这多次请求中，因为Jsession是相同的，所有的session的数据实现了共享。

![](https://note.youdao.com/yws/api/personal/file/88C0C25C73724DD3A6C079FB775DB1FB?method=download&shareKey=e91e1d235a9fd1c51afa777b39b8ff95)

##### ==7.细节==

1. 当客户端关闭后，服务器不关闭，两次获取的session是否为同一个？

   默认情况下不是，因为：每一次关闭浏览器。jsessionid会被清除。所以每一次重新打开浏览器访问的是不同的session对象，因为JSESSIONID是不同的。

   如果需要相同，可以持久化操作：

   Cookie cookie=new Cookie("JSESSIONID",session.getId());

   cookie.setMaxAge(60*60);

   response.addCookie(cookie);

2. 当浏览器不关闭，服务器关闭时，服务器重启后，访问的是同一个session吗？

   答案：默认情况下不是。因为：session对象在服务器关闭后，会被从内存中清理掉，下次的session对象是一个新的session对象

   session如何保证：服务器关闭前后，都是同一个session呢？？

   解决方案：tomcat已经给我们解决了。

   技术支持：

   对象的序列化 ：把内存中的对象存储到硬盘中，相当于把内存中的对象保存起来。

   对象的反序列化：当下次使用这个对象的时候，可以把硬盘中的文件，解析到内存，那么我们的内存中，这个对象又存在了。

   演示过程：tomcat的序列化和反序列化

   1.找到项目，然后再找到它的output输出路径，编译之后的项目【D:\javaWeb\javaWebCode\output\production】

   2.把项目打包成war包:day15-session.war

   3.把这个war包，放入到tomcat服务器中（不使用idea）

   4.使用idea进行session的序列化和反序列化。

     问题：idea关闭tomcat时，会把session进行序列化

    但是，当idea重新启动tomcat时，会自动把序列化之后的文件删除，所以使用idea演示，浏览器不关闭，服务器关闭时，这是场景并不能看到反序列化的过程，因为，序列化文件被删除了。

3. session不进行持久化工作时，session在内存的失效时间？

   默认情况是30分钟，在【tomcat7/conf/web.xml】

4. 如果session失效了会发生什么问题？

   比如：登录了一个网站，session存储量用户的登录信息。如果用户在半小时中，不进行任何操作（挂机），过一段时间回来，会提示：请重新登录。

##### 8.综合案例：注册页面的【验证码】

 准备工作：需要资源util工具类【VerifyCode】

```
VerifyCode中的方法：
drawImage:生成一张验证码图片 ，
        该图片最终是要显示到页面上的，给用户+看到
getCode() ：验证码中的图片的数字，一定是正确的验证码
            该数字是专门用于 对比用户输入的 验证码
            该数字是存储于 session 对象中，一次会话中生效
regist.jsp页面：验证码的<img />
    <img src="img/regist/yzm.jpg" width="" height="" alt="" />
    img/regist/yzm.jpg ： 是一个写死的静态的图片
需要改造：当点击图片时，图片更换一张验证码，需要发送一个请求
         当页面刷新时，图片更换一张验证码，需要发送一个请求
创建一个Servlet类：cn.tedu.servlet/ValidateCodeServlet.java
    缓存【控制浏览器不适用缓存】
    接受【更换验证码的请求】
    获取【一张验证码图片】
    获取【验证码的正确code，存放到session中】
    响应【输出流输出图片】
打开RegistServlet.java类：需要验证用户输入的验证码是否正确
```

#### 四、session和cookie的区别

1. session是存储在服务器端，cookie是存储在浏览器端。
2. session没有数据大小的限制，cookie是有大小限制。
3. session的数据保存是安全的，cookie的数据保存是相对不安全的。





 