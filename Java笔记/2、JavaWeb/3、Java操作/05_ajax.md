[TOC]
# ajax

#### 一、Ajax概述

- asynchronous js and xml:异步的js和xml

- 可以利用js发送请求，而且是局部的发送请求，异步的访问服务器

- 通常服务器给浏览器响应的是一个完整的页面,而在AJAX中, 由于是利用js访问服务器, 再由js接受响应, 局部刷新页面, 所以服务器不用给浏览器响应整个页面了, 而只是数据。

- 服务器响应的数据:

  \> text 纯文本 "用户名已存在!!"

  \> xml 严格区分大小写，需要合理嵌套才能正常使用。

  \> json: js提供的一种数据交互格式, 在js中很受欢迎 

  ajax --> ajaj --> aj

#### 二、同步交互和异步交互

同步： 向服务器发一个请求, 必须等待响应结束, 才能发送第二个请求, 在服务器处理期间, 浏览器不能做其他操作。

​	刷新范围：刷新整个页面。

异步:向服务器发一个请求, 不用等待响应结束, 就可以发送第二个请求, 在服务器处理期间, 浏览器可以做其他操作。

​	刷新范围：可以使用js接收服务器的响应, 再利用js局部刷新页面。

#### 三、ajax应用场景

- 百度的搜索框
- 注册用户时, 校验用户名是否被注册过

#### 四、ajax的优点和缺点

##### 1.优点

​	异步交互, 提高了用户体验!

​	服务器只响应部分数据, 而不是整个页面, 所以降低了服务器的压力!

##### 2.缺点

​	ajax不能应用所有的场景

​	ajax会无端的增加访问服务器的次数, 给服务器带来了压力!!

#### 五、ajax的实现--开发步骤

##### 1.原生态的js脚本开发步骤

- 确定开发思路：

  - 确定js函数的触发时机：用户文本框失去焦点时触发。
  - ajax发送的局部请求，服务器端需要对应一个servlet处理请求。
  - 服务器端返回的结果，需要显示在前端html页面中。

- 开发步骤：

  - 导入ajax-js的脚本【工程下/web/js】

    资源位置：【day13-ServletContext-AM\课前预习\day13\代码\js】

  - 打开【js/ajax.js】了解其中函数的内容

    创建一个操作ajax的对象，各个浏览器都存在内建的该对象

    var xmlHttp-new XMLHttpRequest();

  - 打开【register.jsp】页面。在其中编写js脚本实现ajax

    1. <input type="text" name="username" onblur="ajaxCheckUsername(this)" >

       <span id="uid_msg" >提示信息</span>

    2. 引入ajax脚本

       ```
   <script src="js/ajax.js"></script>
       ```
    
    3. 编写一个函数
    
       ```
       <script>
           			function ajaxCheckUsername(obj){
           				//获取文本框输入的内容value值
           				var value=obj.value;
           				console.log(value);
           				//2.创建一个XMLHttpRequest对象
           				var xr=ajaxFunction();
           				//2.1查看状态码：0表示对象刚刚创建，还没有使用
           				console.log(xr.readyState);
           				//3.使用xr对象，发送一个请求HttpRequest
           				//open方法是打开与服务器的连接
                           //参数1：提交方式；参数2：url路径；参数3：是否为异步
           				xr.open("POST","/ajaxCheckUsername",true);
           				console.log(xr.readyState);//状态码1表示open方法调用了
           				//4.需要把参数添加到request请求中
                           xr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
           				xr.send("username="+value);
           				console.log(xr.readyState)//状态码2表示发送了参数
           				//5.需要等待request请求发送到服务器端，服务器端处理完毕返回结果
           				//添加一个监听器，专门监听服务器端的响应
           				xr.onreadystatechange=function () {
           					console.log(xr.readyState)//状态码3表示开始响应，响应未结束
           					if(xr.readyState==4){//状态码4表示响应结束
           						//判断response对象响应的状态码：200表示成功 404,500表示有错误
           						if(xr.status==200){//响应成功
           							console.log(xr.status);
           						}else if(xr.status ==404){//资源路径错误
           							console.log(xr.status);
           						}else{//服务器错误
           							console.log(xr.status);
           						}
           					}
           	}
           }
       </script>
       ```
    
    4. 在服务器端编写java代码
    
       ```
       package servlet;
           
           import cn.tedu.utils.C3P0Util;
           import cn.tedu.utils.WebUtils;
           
           import javax.servlet.ServletException;
           import javax.servlet.annotation.WebServlet;
           import javax.servlet.http.HttpServlet;
           import javax.servlet.http.HttpServletRequest;
           import javax.servlet.http.HttpServletResponse;
           import javax.xml.transform.Result;
           import java.io.IOException;
           import java.sql.Connection;
           import java.sql.PreparedStatement;
           import java.sql.ResultSet;
           import java.sql.SQLException;
           
           @WebServlet("/ajaxCheckUsername")/*与register.jsp页面中发送的请求一致*/
           public class AjaxCheckUsername extends HttpServlet {
               protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                   //目标：检查用户名是否已经存在
                   //1.设置请求和响应的编码格式
                   request.setCharacterEncoding("utf-8");
                   response.setContentType("text/html;charset=utf-8");
                   //2.获取请求中的参数
                   String username = request.getParameter("username");
                   //3.判断非空校验
                   if(WebUtils.isNull(username)){
                      //因为ajax是局部刷新页面，所以不能使用转发，重定向。
                       response.getWriter().write("用户名不能为空");
                       return ;
                   }
                   //4.判断数据库中是否已经存在该用户
                   Connection connection = C3P0Util.getConnection();
                   PreparedStatement ps =null;
                   ResultSet rs=null;
                   try {
                       ps=connection.prepareStatement("select * from user where username=?");
                       ps.setString(1,username);
                       rs=ps.executeQuery();
                       if(rs.next()) {//用户名已经存在
                           response.getWriter().write("该用户已存在");
                       }else{//用户名可以使用
                           response.getWriter().write("用户名可以使用");
                       }
                   } catch (SQLException e) {
                       e.printStackTrace();
                   }finally{
                       C3P0Util.close(connection,ps,rs);
                   }
               }
           
               protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                   this.doPost(request, response);
               }
           }
       ```
    
    5. 进一步完善register.jsp页面中，js脚本
       目标：获取servlet响应给浏览器的查询结果 
    
       ```
       <script>
              function ajaxCheckUsername(obj){
                 //获取文本框输入的内容value值
                 var value=obj.value;
                 console.log(value);
                 //2.创建一个XMLHttpRequest对象
                 var xr=ajaxFunction();
                 //2.1查看状态码：0表示对象刚刚创建，还没有使用
                 console.log(xr.readyState);
                 //3.使用xr对象，发送一个请求HttpRequest
                 //open方法是打开与服务器的连接
                 xr.open("POST","/ajaxCheckUsername",true);
                 console.log(xr.readyState);//状态码1表示open方法调用了
                 //通知服务器发送的数据是请求参数
                 xr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
                 //4.需要把参数添加到request请求中
                 xr.send("username="+value);
                 console.log(xr.readyState)//状态码2表示发送了参数
                 //5.需要等待request请求发送到服务器端，服务器端处理完毕返回结果
                 //添加一个监听器，专门监听服务器端的响应
                 xr.onreadystatechange=function () {
                    console.log(xr.readyState)//状态码3表示开始响应，响应未结束
                    if(xr.readyState==4){//状态码4表示响应结束
                       //判断response对象响应的状态码：200表示成功 404,500表示有错误
                       if(xr.status==200){//响应成功
                          console.log(xr.status);
                          //获取响应的数据内容
                          var result=xr.responseText;
                          console.log(result );
                          document.getElementById("uid_msg").innerText=result;
                       }else if(xr.status ==404){//资源路径错误
                          console.log(xr.status);
                       }else{//服务器错误
                          console.log(xr.status);
                       }
                    }
                 }
              }
       </script>
       ```

##### 2.框架版jquery的js脚本开发步骤--强有力的封装，实现起来很方便

- 确定开发思路

  - 确定js函数的触发时机：用户文本框失去焦点时触发。
  - ajax发送的局部请求，服务器端需要对应一个servlet处理请求。
  - 服务器端返回的结果，需要显示在前端html页面中。

- 开发步骤

  - 导入jquery的包

    <script src="js/jquery-1.4.2.js"></script>

- ```
  <script>
  			/*使用jquery实现ajax*/
  			$(function () {
  				$("input[name='username']").blur(function () {
  					var value=$("input[name='username']").val();
  					console.log(value);
  					/*//jquery实现ajax方式一：
  					$("#uid_msg").load("/ajaxCheckUsername",{"username":value});
  					/!*jquery实现Ajax方式二：get方式发送请求
  					* 特别注意：get方式的编码格式问题--*!/
                      $.get("/ajaxCheckUsername",{"username":value},function (result) {
                          console.log(result);
                          //把result结果显示到span元素中
                          $("#uid_msg").text(result);
                      });
                      /!*jquery实现ajax第三种：post方式发送请求*!/
                      $.post("/ajaxCheckUsername?username="+value,function (result) {
                          console.log(result);
                          //把result结果显示到span元素中
                          $("#uid_msg").text(result);
                      });*/
                      /*jquery实现ajax第四种：可以自定义参数内容，比如可以指定get还是post*/
                      $.ajax({
                          url:"/ajaxCheckUsername1",
                          data:"username="+value,
                          type:"POST",//请求的方式
                          async:true,//异步请求--可以省略
                         // dataType:json,//可以处理的数据格式--可以省略
                          success:function (result) {//成功之后响应结果
                              console.log(result);
                              //把result结果显示到span元素中
                              $("#uid_msg").text(result);
                          },
                          error:function () {//url资源路径不存在
                              $("#uid_msg").text("服务器404错误")
                          }
                      });
  
  				})
  			});
  		</script>
  ```

##### 3.web应用与ajax

![](https://note.youdao.com/yws/api/personal/file/6E6B9FC2DDA24D8F801B1AB41A405E6D?method=download&shareKey=228c15c16e28cb75427164c9a3e43636)