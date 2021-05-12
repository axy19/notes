[TOC]
# EasyMall

#### 一、EasyMall的搭建

![](https://note.youdao.com/yws/api/personal/file/0F92400D34AD407893EF4247114C2EF8?method=download&shareKey=c07f0cd1031477210655d6ede67b41dc)

#### 二、接受请求时，出现乱码的原因

浏览器的编码格式与服务器的编码格式不一致。

浏览器发送过来的参数时 需要服务器进行解码，然后才能获得数据。

1. 浏览器页面设置了什么格式，那么页面传递的参数就是什么格式。

   页面中是utf-8的格式

   因此输入的字符，比如张--->utf-8的格式   

   utf-8的格式会把中文的一个字转换为三个字节的字节码

2. 设置接受请求的编码格式，防止出现乱码

   post方式

   一下这种方式只能对post请求有效
   这句话一般放在代码的开始位置
   该方法只能解析请求正文的参数，对地址栏的参数无效

   ```
   req.setCharacterEncoding("utf-8");
   ```

   get方式

   ```
username = new String(username.getBytes("ISO-8859-1"), "UTF-8");
   ```

   ![](https://note.youdao.com/yws/api/personal/file/02696C5E6F0E453D80274D9387AEB300?method=download&shareKey=c71c0b6760866a8d833f4388d8af4ac9)

#### 三、注册代码编写步骤

![](https://note.youdao.com/yws/api/personal/file/A4C2C0F946EA47C09C8DD87DBC218B87?method=download&shareKey=97a3a7e14f06a222ea98b1ac717d5215)

##### C3P0Utils工具类

```java
package cn.tedu.utils;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

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

配置文件

```
<c3p0-config>
   <!-- default默认指的是c3p0会直接加载该标签中的内容 -->
   <default-config>
      <property name="driverClass">com.mysql.jdbc.Driver</property>
      <property name="jdbcUrl">jdbc:mysql://localhost:3306/easymall</property>
      <property name="user">root</property>
      <property name="password">123456</property>
   </default-config>
</c3p0-config>
```

##### WebUtils工具类

```java
package cn.tedu.utils;

import java.io.UnsupportedEncodingException;

public class WebUtils {
    /*因为是公共的资源方法，最好设置成静态的方法*/
    private WebUtils(){
        //私有化，别人不能创建对象
    }
    /*1.非空校验*/
    public static boolean isNull(String value){
        return value==null||value.equals("")?true:false;
    }
    /*2.对字符集进行正确的编码，防止乱码出现*/
    public static String encoding (String value,String character){
        try {
            if(value!=null){
                value=new String(value.getBytes("ISO-8859-1"),character);
            }
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return value;
    }
}
```

##### Servlet类

```java
package servlet;

import cn.tedu.utils.C3P0Util;
import cn.tedu.utils.WebUtils;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

@WebServlet("/register")/*页面发过来的请求叫做register*/
public class RegisterServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //0.设置接受请求的编码格式，防止出现乱码
        /*0.1一下这种方式只能对post请求有效
        * 0.2这句话一般放在代码的开始位置
        * 0.3该方法只能解析请求正文的参数，对地址栏的参数无效*/
//        req.setCharacterEncoding("utf-8");
        /*0.4一下这种方式是对get方式做的专门的处理
        * 要求：要使用此方法必须先拿到参数信息
        * 此方法无论get方式还是post方式都有效*/
        //1.获取注册表单的数据
        /*参数表示的是：页面input内的name="这是参数的值"*/
        String username = req.getParameter("username");
        username = WebUtils.encoding(username,"utf-8");
        String password = req.getParameter("password");
        String password2 = req.getParameter("password2");
        String nickname = req.getParameter("nickname");
        String email = req.getParameter("email");
        String valistr = req.getParameter("valistr");
        /*打桩测试，输出获取的信息*/
        System.out.println(username);
        System.out.println(password);
        System.out.println(password2);
        System.out.println(nickname);
        System.out.println(email);
        System.out.println(valistr);
        //2.非空校验
        //创建一个工具类utils的WebUtil类中--以后的公共代码放在其中。
        if (WebUtils.isNull(username)) {
            //如果为空时打印输出提示信息不能为空
            //需要return直接中断代码
            System.out.println("用户名不能为空");
            req.setAttribute("errorMsg1","用户名不能为空");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }
        if (WebUtils.isNull(password)) {
            //如果为空时打印输出提示信息不能为空
            //需要return直接中断代码
            System.out.println("密码不能为空");
            req.setAttribute("errorMsg2","密码不能为空");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }
        //3.密码和确认密码要一致校验
        if (!password.equals(password2)) {
            System.out.println("两次密码输入不一致。");
            req.setAttribute("errorMsg6","两次密码不一致");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }
        if (WebUtils.isNull(nickname)) {
            //如果为空时打印输出提示信息不能为空
            //需要return直接中断代码
            System.out.println("昵称不能为空");
            req.setAttribute("errorMsg3","昵称不能为空");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }
        if (WebUtils.isNull(email)) {
            //如果为空时打印输出提示信息不能为空
            //需要return直接中断代码
            System.out.println("邮箱不能为空");
            req.setAttribute("errorMsg4","邮箱不能为空");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }
        //4.邮箱格式校验:123@qq.com
        String reg = "\\w+@\\w+(\\.\\w+)+";
        if (!email.matches(reg)) {
            System.out.println("邮箱格式不正确。");
            req.setAttribute("errorMsg7","邮箱格式不正确");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }
        if (WebUtils.isNull(valistr)) {
            //如果为空时打印输出提示信息不能为空
            //需要return直接中断代码
            System.out.println("验证码不能为空");
            req.setAttribute("errorMsg5","验证码不能为空");
            req.getRequestDispatcher("/regist.jsp").forward(req,resp);
            return;
        }

        //5.数据库查询是否已经存在该用户
        Connection con = C3P0Util.getConnection();
        PreparedStatement ps = null;
        ResultSet rs = null;
        String sql1 = "select * from user where username=?";
        String sql2="insert into user values(null,?,?,?,?)";
        try {

            ps = con.prepareStatement(sql1);
            ps.setString(1, username);
            rs = ps.executeQuery();
            if (rs.next()){
                //5.1如果存在不执行插入操作
                System.out.println("该用户已存在，注册失败");
                req.setAttribute("errorMsg1","该用户名已存在");
                req.getRequestDispatcher("/regist.jsp").forward(req,resp);
                return ;
            }else{
                //5.2如果不存在执行插入操作
                ps=con.prepareStatement(sql2);
                ps.setString(1,username);
                ps.setString(2,password);
                ps.setString(3,nickname);
                ps.setString(4,email);
                ps.executeUpdate();
                System.out.println("用户注册成功");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            C3P0Util.close(con, ps, rs);
        }
        //6.登录的页面跳转 主界面跳转使用重定向
        //resp.setStatus(302);
        //resp.sendRedirect("/index.jsp");
        //使用定时刷新
        resp.setContentType("text/html;charset=utf-8");
        resp.getWriter().write("<h1><font>注册成功，3秒后转到注册页面</font></h1>");
        resp.setHeader("refresh","3;url=http://www.easymall.com");
    }
}
```

##### regist.jsp

```
<input <%--onblur="ajaxCheckUsername(this)"--%>  type="text" name="username" value="<%=request.getParameter("username")==null?"":request.getParameter("username")%>" />
<span id="uid_msg" style="color: red">
   <%=request.getAttribute("errorMsg1")==null?"":request.getAttribute("errorMsg1")%>
</span>
```

#### 四、EasyMall的执行流程

![](https://note.youdao.com/yws/api/personal/file/06A67D3945C240CAA9BF38024E3E2EF7?method=download&shareKey=cc99fe871b9011a81762959650e27897)

![](https://note.youdao.com/yws/api/personal/file/7D72F3906A904340B3422433C4ABF436?method=download&shareKey=a1b2f8face33d36185550bf9142b0f66)

![](https://note.youdao.com/yws/api/personal/file/5C269347D7AD4F138781FC01951752D4?method=download&shareKey=a25abeac1de109a99dba2293a00f233b)

![](https://note.youdao.com/yws/api/personal/file/841C335D527445D6BD3B33CA95003FF0?method=download&shareKey=36dcf4b3ccb7846d9aba2e2184a7dfe1)

![](https://note.youdao.com/yws/api/personal/file/D4C9B45DB21A4CC98A01ED16B30B2743?method=download&shareKey=39f93155cd940232b049cefb93c72a10)

![](https://note.youdao.com/yws/api/personal/file/17478C9DCD9649D79A03B8630BBA121A?method=download&shareKey=78ba598e4df5af1f2d463b1113d79a12)



![](https://note.youdao.com/yws/api/personal/file/B44B8CB3A63947F48A6DB6751AE6C320?method=download&shareKey=5434a263c010b610fac417d806c3e6a7)

#### 五、easymall的开发模式

##### 1.注册功能的开发思路

前提是：需要先查询数据库中是否存在该用户，如果不存在，把用户的信息插入到数据库，如果存在，把查询的结果“已存在该用户”响应给浏览器

1. 编写一个model模型层：pojo/User.java----对应着数据库的表user表

2. 编写一个DAO层，数据访问层：编写JDBC访问数据库的方法

   1. 查询数据库中是否存在该用户--findByUsername

   2. 把用户信息插入数据库---insertUserInfo

   3. 为什么会写成两个方法，而不是一个方法？

      原因：注册时需要查询用于是否存在，登陆时也需要查询用户是否存在。

3. 编写一个Service业务逻辑层：service/UserService.java

   该类中，主要调用dao层的方法：                                    

4. 编写一个Controller控制层servlet类：servlet/RegistServlet.java

   特别注意：需要调用service层的方法，完成用户的注册代码执行，但是只要是调用service层方法，就有可能出现异常，那么需要对异常进行捕获，会把异常信息提示给浏览器用户。

5. 编写一个View视图层。jsp页面：web/regist.jsp

   需要使用：el表达式和gstl标准标签函数库！！！视具体情况使用。

   外部文件引用时，引用的路径需要写成动态的虚拟路径的形式。

   ​	request.getContextPath();//虚拟路径的名称

   ​	el表达式：${pageContext.request.contextPath}/css/head.css

   获取请求中的参数进行数据的回显：

   ​	${param.username}<---><%=request.getParameter("username")%>

   错误提示信息的显示：

   ​	${errorMsg1}<---><%=request.getAttribute("username")%>

##### 2.登录功能的开发思路

当用户名和密码都输入正确时，才会进行正常登入，否则提示用户名或者密码不正确。

1. 编写Model层：pojo/User.java类，在注册时已经创建好了。

2. 编写一个DAO层：dao/UserDao.java类，在注册时已经创建好了。

   但是在登录时，需要根据用户名和密码一起查询数据库，确定登陆信息。

3. 编写service业务逻辑层：service/UserService.java

   但是在登录时需要添加特定的业务逻辑。

    	先查询数据库是否能够查到登录的用户信息。

   ​	 如果有，就获取登录的user对象并返回给servlet层

   ​	 如果没有抛出异常提示信息：用户名或者密码不正确。

4. 编写一个View视图层：web/login.jsp 

   使用el表达式，标准标签函数库。

##### 3.记住用户名的功能

当用户名第一次输入用户名和密码时，点击登录，并且点击【记住用户名】，那么在LoginServlet.java中，会产生一个cookie，记录了当前的用户名。

当用户再次进入登录页面时，login.jsp页面会自动判断cookie中是否有用户名，如果cookie中有用户名，那么回显到用户名文本框。

乱码问题："${cookie.username.value}" 

解决方案:需要进行utf-8的转码

方案一：<%URLDecoder.decode()%>

方案二：jQuery中提供了一个转码的方法、

```
${function(){//当页面加载完毕之后
	$("input[name='username']").value()
}}
```

##### 4.30天内自动登录的功能

需要先使用cookie把用户名和密码，保存到浏览器缓存中，需要在使用filter过滤器，拦截index主页面的请求，然后从cookie中获取用户名和密码，直接放行即可。

开发步骤：

1. 先在用户第一次登录时，生成一个cookie，cookie记录了用户名和密码。

   位置：在LoginServlet.java中创建一个cookie

2. 当用户再次访问主界面时，由filter过滤器拦截，从cookie中拿数据。

   位置：在filter包中，创建一个类：AutoLoginFilter.java

3. index.jsp主界面显示当前登陆用户的信息

   注意：user对象是存储在session中的，是一个会话中的共享数据

   jstl完成登陆的用户名的显示。

   ​	1.导包：jstl.jar、standard.jar,位置web/WEB-INF/lib

   ​	2.需要在index.jsp页面中，添加@taglib的指令

   ```
   <@ taglib prefix="c" uri=">"
   ```

   ​	3.编写jstl代码编写即可。


