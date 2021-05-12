[TOC]
# DHTML

#### DHTML概述

- JS到底是如何操作HTML的？？？

  答案：根据DHTML的对象使用js操作HTML页面。

- DHTML ： Dynamic  HTML 动态的HTML页面的概念。

- DHTML ：不是一门语言，更不是一门技术，而是一个HTML与CSS与JS的结合的技术集。是一种设计层面的概念是一种艺术。

- DHTML特点：

  - 是将HTML的各个标签元素，以及CSS的各个属性 转换为 JS可以操作的对象
  - DHTML 中的各个对象，都可以被JS去操作，那么当找到其中一个节点时，该节点的树形结构的层级关系也就清晰了。
  - DHTML提供了大量的API，方便js调用任意的方法去操作页面

- DHTML由以下两部分组成：

  - 整个浏览器都会被解析成一个对象：BOM(Browser Object Model)浏览器对象模型

  ![](https://note.youdao.com/yws/api/personal/file/57CEB2E578EF482082FB86FC8FF914E3?method=download&shareKey=ee17d3b0adb373a034d460b9971e8fa2)

  - 整个浏览器对应的HTML ：DOM(document  object  model)  文档对象模型

     <HTML>

    ​     <HEAD></head>

    ​     <BODY></BODY>

    </HTML>

    ![](https://note.youdao.com/yws/api/personal/file/1457C8D345DE43339DD60D1FF89B4847?method=download&shareKey=20ea87edbad50fe7cea8a14915949c66)

  - 总结：BOM包含了DOM

    ![](https://note.youdao.com/yws/api/personal/file/664AF56985784815A2F0884F7E34C83D?method=download&shareKey=b7c4b3b816f136623ad039f274450ee8)

#### BOM模型

了解Bom常用的事件和方法

##### web浏览器窗口对象:window

其中包含的方法：

* onblur:失去焦点
* 
* :获得焦点
* !!!onload:当前浏览器页面装载完成后触发
* !!!alert
* !!!confirm
* !!!prompt
* !!!close (//仅限ie浏览器，chrom和firefox需要解决浏览器禁止js关闭非js创建页面的问题)
* !!!setInterval
* !!!setTimeout

其中包含的对象：

* location：href 获取或设置地址栏上的地址。通过此属性js可以控制浏览器访问一个新的地址。
* screen
* history：length  back()  forward()  go()
* navigator
* document

```HTML 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>DHTML-BOM01-窗口对象</title>
    <script>
        /*1.BOM对象模型中，顶级的对象：window对象
        *   window对象指的是浏览器的窗口！！*/
        //1.1window.onload指的是页面加载完毕后执行的代码，onload表示触发的事件
        window.onload=function(){
            //向div中输出一句话
            document.getElementById("d1").innerText="123";
        };
        //1.2window.onblur指的是  鼠标移出页面，window对象失去焦点时触发
        window.onblur=function(){
            document.getElementById("d1").innerText="页面失去焦点了";
        };
        //1.3window.onfocus指的是  鼠标进入页面，焦点在当前window窗口中
        window.onfocus=function () {
            document.getElementById("d1").innerText="页面获取焦点";
        }
        //1.4window的prompt的弹出对话框，带有提示信息和输入框--方法
        //返回值是输入的字符串
        var password=window.prompt("请输入您的密码：");
        alert(password);
        if(password=="123"){
           alert('登陆成功');
        }else{
            alert("登录失败");
        }
        //1.5window的confirm确认对话框，带有提示信息和确认/取消按钮
        //返回值是true/false
        var check=window.confirm("确定要删除吗？");
        if(check){//true
            alert("删除成功");
        }else{//false
            alert("取消删除");
        }
    </script>
</head>
<body>
    <div id="d1"></div>
</body>
</html>
```

##### window对象的子对象

```HTML 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>bom02wihdow子对象</title>
    <script>
        /*1.history历史对象*/
        function demo1() {
            //前进的方法：forward
            // window.history.forward();
            //前进方法：go
            window.history.go(1);//1表示前进
        }
        function demo2() {
            //后退的方法：back
            // window.history.back();
            //后退的方法go()
            window.history.go(-1);//-1表示后退
        }
        /*2.location对象：地址栏，包含当前url的信息*/
        console.log(window.location.href);
        console.log(window.location.hostname)
        /*3.navigator对象：包含浏览器的一些基本信息*/
        /*获取浏览器运行的平台和版本*/
        console.log(window.navigator.appVersion);
        /*4.screen屏幕对象：包含屏幕和渲染能力的对象*/
        console.log(window.screen.width);
        console.log(window.screen.height);

    </script>
</head>
<body>
    <input type="button" onclick="demo1()" value="前进按钮" />
    <input type="button" onclick="demo2()" value="后退按钮" />
    <a href="http://www.tmooc.cn" >教育网站</a>
</body>
</html>
```

#### DOM模型

#####  如何获取HTML中的某一个节点信息

document可以通过三种方式获取 DOM中的节点：

1.  通过节点标签的id属性获取
2. 通过节点标签的name属性获取
3. 通过节点标签的'标签名'获取

| getElementById("id")            | 根据id获取一个元素                       |
| ------------------------------- | ---------------------------------------- |
| getElementsByName("name")       | 根据name获取一组元素                     |
| getElementsByTagName("tagname") | 根据元素名称获取一组元素                 |
| innerHTML()                     | 设置或获取位于对象起始和结束标签内的HTML |
| innerText()                     | 设置或获取位于对象起始或结束标签内的文本 |

```html
<!DOCTYPE HTML>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
   <title>如何通过document获取数据</title>
   <meta http-equiv="Content-type" content="text/html; charset=UTF-8" />
   <script type="text/javascript">
        /*document获取数据的方式：3种（id,name,元素名）*/
        function demo1() {
            //通过id获取数据，id表示标签的唯一标示符
            //1.获取input对象
            var username=document.getElementById('username');
            console.log(username);//对象
            console.log(username.value)//输入的值
        }
        function demo2() {
            //通过name获取数据：name表示标签中的name属性
            //返回结果是数组
            var pwds=document.getElementsByName('password');
            console.log(pwds);
            //获取pwds数组中的第一个对象:
            var p1=pwds[0];
            console.log(p1);
            //获取p1对象中的value值
            console.log(p1.value);
        }
        function demo3(){
            /*通过标签的名字，也就是标签名称去获取所有的统一名称的元素对象*/
            var inputs=document.getElementsByTagName("input");
            console.log(inputs);
            //使用数组的下标获取value值
            for(var i=0;i<inputs.length;i++){
                console.log(inputs[i].value);
            }
        }
        function demo4() {
            //1.先获取段落p对象
            var p=document.getElementById("pid");
            console.log(p);//打印p段落元素对象
            //2.获取内部的文本 ：innerText
            console.log(p.innerText);
            //3.获取内部的html标签：innerHTML
            console.log(p.innerHTML);
            //4.动态的修改内部html标签
            p.innerHTML="<font color='#00ff7f' >嘿嘿嘿</font>";
        }
    </script>

   
</head>
<body>

   用户名称：<input type="text" name="username" id="username"/><br />
    用户密码：<input type="password" name="password" id="password" /><br />
    用户密码2：<input type="password" name="password" id="password2" /><br />
    
    <hr />
    <input type="button" value="通过ID获取节点的值" onclick="demo1()"/>
    <input type="button" value="通过NAME获取节点的值"  onclick="demo2()" />
    <input type="button" value="通过TAG获取节点的值" onclick="demo3()" />        
    
    <hr  />
    <p id="pid"><font color="red">获取P标签中的文字</font></p>
   <input type="button" value="获取P中文字" onclick="demo4()" />
   
</body>
</html>
```

##### 节点的增删改克隆

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

<title>节点的增删改查</title>
<!--加入样式表-->
<style type="text/css">
div {
   border:#0099FF 1px solid;
   height:60px;
   width:120px;
   margin:20px 0px 20px 20px;
   padding:10px 0px 0px 20px;
   }
   
#div_1{
   background-color:#00FFFF;
   }
   
#div_2{
   background-color:#FF3399;
   }
   
#div_3{
   background-color:#0000FF;
   }
   
#div_4{
   background-color:#FFFF66;
   }
</style>

<script type="text/javascript">
    function addNode(){
        /*创建一个节点，把新节点添加到页面中*/
        //1.创建一个新的div节点:creatElement("元素名称")
        var newdiv=document.createElement('div');
        //添加样式
        newdiv.style.backgroundColor="springgreen";
        newdiv.innerText="div新区域";
        //2.获取父节点对象：body对象,返回的是一个数组，数组的第0个元素就是body
        var body=document.getElementsByTagName("body")[0];
       /* //3.将newdiv插入到body中，排序往后添加
        body.appendChild(newdiv);*/
       //4.1先获取div_2的节点，在该节点前边插入
        var div2=document.getElementById("div_2");
        //4.2确定新节点依然是body的孩子节点
        body.insertBefore(newdiv,div2);
    }
    function deleteNode() {
        //1.获取父节点对象：body
        var body=document.getElementsByTagName('body')[0];
        //2.获取子节点对象：div_2
        var div2=document.getElementById("div_2");
        //3.父节点删除其中一个子节点
        body.removeChild(div2);
    }
    function updateNode() {
        //1.获取父节点对象：body
        var body=document.getElementsByTagName("body")[0];
        //2.获取被替换的节点对象div_3
        var div3=document.getElementById("div_3");
        //3.生成一个新节点
        var newdiv=document.createElement("div");
        //4.把div3替换成新节点
        body.replaceChild(newdiv,div3);
    }
    function copyNode() {
        //1.先获取div的对象
        var div4=document.getElementById("div_4");
        //2.在获取body对象
        var body=document.getElementsByTagName("body")[0];
        //3.克隆div对象
        //不写参数默认为false等同于写false，表示克隆样式，不能克隆内容
        //写了参数是true表示完全克隆包含内容。
        var newdiv=div4.cloneNode(true);
        //4.追加克隆的div对象
        body.appendChild(newdiv);
    }
</script>

</head>
<body>
    <div id="div_1">
      
    </div>
    
    <div id="div_2">
    div区域2
    </div>
    
    <div id="div_3">
        div区域3
    </div>
    
    <div id="div_4">
    div区域4
    </div>
    
    <hr />
    <input type="button" value="创建并添加节点" onclick="addNode()" />
    <input type="button" value="删除节点" onclick="deleteNode()" />
    <input type="button" value="替换节点" onclick="updateNode()" />
    <input type="button" value="克隆节点" onclick="copyNode()" />

</body>
</html>
```

##### 节点的样式调整（css的样式）

1.节点对象.style.css的属性

2.节点对象.className=css的样式选择器的名字

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>新闻广告</title>
<style type="text/css">
<!-- 伪元素选择器 -->
a:link,a:visited {
   color:#FF9900;
   text-decoration:none;
   font-size:15px;
   }
   
a:hover {
   color:#0099FF;
   }
   
.newsstyle {
   border:#0099FF 1px solid;
   font-size:16px;
   width:400px;
   }
   
/*
预先定一些选择器
*/
.max {
   border:#0099FF 1px solid;
   font-size:20px;
   color:#FF0000;
   background-color:#CCFFFF;
   width:400px;
   }
   
.min {
   border:#0099FF 1px solid;
   font-size:12px;
   color:#0000FF;
   background-color:#FFFFFF;
   width:400px;
   }
</style>
<script type="text/javascript">
    function resize(obj) {
        //1.打印一下obj是什么
        console.log(obj);
        //2.obj代表的是 css样式
        document.getElementById("newstext").className=obj;
    }
</script>
</head>
<body>
    <h2>这是一个大新闻.</h2>
    <a href="javascript:void(0)" onclick="resize('min')">小字体</a> 
    <a href="javascript:void(0)" onclick="resize('newsstyle')">中字体</a> 
    <a href="javascript:void(0)" onclick="resize('max')">大字体</a> 
    <hr />    
    <div id="newstext" class="newsstyle">
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    演示接口.很多内容.怎么办呢?等等<br />
    </div>
</body>
</html>
```

##### 好友列表（节点之间的关系）

父子关系:子节点对象.parentNode

兄弟关系：节点对象.nextSlibling//后边的空行也会是一个兄弟节点

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

<title>好友列表</title>
<style type="text/css">
table {
   border:#0099FF 1px solid;
   width:100px;
   border-collapse:collapse;
   }
   
table td{
   border:#0066FF 1px solid;
   background-color:#FF9900;
   text-align:center;
   }
   
table td div {
   background-color:#FFFF99;
   text-align:left;
   }
   
table td a:link, table td a:visited {
   color:#00ffFF;
   text-decoration:none;
   }
   
table td a:hover {
   color:#00CC00;
   }

/*
使用display属性:如果取值为none就是隐藏标签。
*/
table td div {
   display:none;
   }

.open {
   display:block;/*显示div*/
   }
   
.close {

   }

</style>
<script type="text/javascript">
   function openDiv(obj) {
      //1.确定obj是什么？？这是超链接a
      console.log(obj);
      //2.确定-->好友的超链接a与好友列表div的关系：并列的兄弟关系
      console.log(obj.nextSibling.nextSibling);
      //3.给div添加打开的样式
      var div=obj.nextSibling.nextSibling;//div好友列表
      /*div.className="open";*/
      //4.需要判断，如果好友列表原来是展开的那么就关闭，
      // 如果原来是关闭的则打开
      /*if(div.className=="open"){//如果好友列表原来是展开的那么就关闭
         div.className="close";
      }else{//如果原来是关闭的则打开
         div.className="open";
      }*/
      //5.当点击其中一个好友时其他好友是自动关闭状态的
      //先获取所有的div，共四个
      var divs=document.getElementsByTagName("div");//数组
      for(var i=0;i<divs.length;i++){
         //已经确定了一个div在第三步已获得，是当前点击的div
         //其余三个div状态设置关闭即可
         if(div!=divs[i]){
            divs[i].className="close";
         }else if(div.className=="open"){//如果好友列表原来是展开的那么就关闭
            div.className="close";
         }else{//如果原来是关闭的则打开
            div.className="open";
         }
      }



   }

</script>

</head>


<body>
<table>
   <tr>
      <td>
         <a href="javascript:void(0)" onclick="openDiv(this)">君王好友</a>
         <div>
            秦始皇<br />
            刘邦<br />
            李世民<br />
            康熙<br />
         </div>
      </td>
   </tr>
   <tr>
      <td>
         <a href="javascript:void(0)" onclick="openDiv(this)">三国好友</a>
         <div>
            刘备<br />
            关羽<br />
            张飞<br />
            赵云<br />
         </div>
      </td>
   </tr>
   <tr>
      <td>
         <a href="javascript:void(0)" onclick="openDiv(this)">美女好友</a>
         <div>
            西施<br />
            貂蝉<br />
            杨贵妃<br />
            王昭君<br />
         </div>
      </td>
   </tr>
   <tr>
      <td>
         <a href="javascript:void(0)" onclick="openDiv(this)">IT好友</a>
         <div>
            马云<br />
            李开复<br />
            俞敏洪<br />
            李彦宏<br />
         </div>
      </td>
   </tr>
</table>
</body>
</html>
```

##### 二级联动菜单

```html
<!DOCTYPE html >
<html >
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>二级联动菜单</title>
<script>
   function selectCity(obj) {
      //1.定义一个json对象，描述省与市之间的关系
      var data={
         "北京市":["海淀区","朝阳区","丰台区"],
         "河北省":["石家庄","唐山","秦皇岛"],
         "辽宁省":["沈阳","大连","鞍山"],
         "山东省":["青岛","济南","烟台"]
      }
      //2.获取第一级下拉框的内容:value值
      var s1=document.getElementById("province").value;
      console.log(s1);
      //3.根据s1的值，获取json数据中的数组，
      // s1代表的是json的key，数组就是json中的s1对应的value
      var citys=data[s1];
      console.log(citys);
      //4.把citys数组循环遍历，然后插入到二级下拉框中
      //获取二级下拉菜单
      var s2=document.getElementById("city");
      s2.innerHTML="<option>---请选择---</option>";//每一次选择时先清空原来的html
      for(var i=0;i<citys.length;i++){
         s2.innerHTML+="<option>"+citys[i]+"</option>";
      }
   }
</script>
</head>
<body>
<select id="province" onchange="selectCity(this)">
   <option>--选择省市--</option>
   <option>北京市</option>
   <option>河北省</option>
   <option>辽宁省</option>
   <option>山东省</option>
</select>
<select id="city">
   <option>--选择城市--</option>
</select>
</body>

</html>
```

##### 注册表单

```html
<html>
   <head>
      <title>表单页面</title>
      <meta http-equiv="Content-type" content="text/html; charset=UTF-8" />
      
      <script>
         function checkData() {
            var result;
            //1.调用非空校验的函数,只有结果都为true才返回true
            result=checkNull("username","用户名不能为空");
            result=checkNull("password","密码不能为空")&&result;
            result=checkNull("password2","确认密码不能为空")&&result;
            result=checkNull("nickname","昵称不能为空")&&result;
            result=checkNull("email","邮箱不能为空。")&&result;
            result=checkNull("type","客户类型不能为空")&&result;
            result=checkNull("img","头像不能为空")&&result;
            result=checkNull("valistr","验证码不能为空")&&result;
            result=checkNull("desc","描述信息不能为空")&&result;
            //2.性别的校验
            var genders=document.getElementsByName("gender");//数组
            var statu=false;
            for (var i=0;i<genders.length;i++){
               //当两个radio都没选时，提示不能为空
               //只要有一个radio选中了证明已选择
               if (genders[i].checked==true){//证明选中了
                  statu=true;
                  break;
               }
            }
            if(!statu){//没选时应该给出提示信息
               document.getElementById("gender_msg").innerText="性别不能为空";
               result=statu&&result;
            }else{
               document.getElementById("gender_msg").innerText="";
               result=statu&&result;
            }
            //3.爱好的校验
            //4.邮箱的校验，格式是否正确
            var email=document.getElementsByName("email")[0];
            if(email.value!=""){
               var r1=/^\w+@\w+(\.\w+)+ $/;
               if(!r1.test(email.value)){//校验不通过
                  document.getElementById("email_msg").innerText="邮箱格式不正确";
                  return false;
               }
            }
            return result;
         }
         /*专门提供一个方法验证所有的文本框的输入内容是否为空*/
         function checkNull(name,msg) {
            //1.先获取文本框的对象
            var tagName=document.getElementsByName(name)[0];
            document.getElementById(name+"_msg").innerText="";
            //2.判断value值是否为空
            if(tagName.value==""){//3.如果为空提示 ：不能为空，返回值为false
               document.getElementById(name+"_msg").innerText=msg;
               return false;
            }else{//如果不为空，直接放行即可
               return true;
            }
         }
      </script>
      <style type="text/css">
         span{
            color:red;
            font-size:12px;
         }
      </style>
   </head>
   <body>
      <form action="#" method="POST" onsubmit="return checkData()">
         <table border="1px" align="center" cellpadding="10px" cellspacing="0px" borderColor="red" bgcolor="pink">
            <caption><font color="red" size="6">注册表单</font></caption>
            <input type="hidden" name="id" value="9527"/>
            <tr>
               <td>用户名:</td>
               <td><input type="text" name="username"  /> <span id="username_msg"></span></td>
            </tr>
            <tr>
               <td>密码:</td>
               <td><input type="password" name="password"/> <span id="password_msg"></span></td>
            </tr>
            <tr>
               <td>确认密码:</td>
               <td><input type="password" name="password2"/> <span id="password2_msg"></span></td>
            </tr>
            <tr>
               <td>性别：</td>
               <td>
                  <input type="radio" name="gender" value="男"/>男
                  <input type="radio" name="gender" value="女"/>女 <span id="gender_msg"></span>
               </td>
            </tr>
            <tr>
               <td>昵称：</td>
               <td><input type="text" name="nickname"/> <span id="nickname_msg"></span></td>
            </tr>
            <tr>
               <td>邮箱：</td>
               <td><input type="text" name="email"/> <span id="email_msg"></span></td>
            </tr>
            <tr>
               <td>爱好：</td>
               <td>
                  <input type="checkbox" name="like" value="lq"/>篮球
                  <input type="checkbox" name="like" value="zq"/>足球
                  <input type="checkbox" name="like" value="qq"/>铅球
                  <input type="checkbox" name="like" value="blq"/>玻璃球
                   <span id="like_msg"></span>
               </td>
            </tr>
            <tr>
               <td>客户类型：</td>
               <td>
                  <select name="type">
                     <option value="pm">平民</option>
                     <option value="sxdy">少先队员</option>
                     <option value="gqty">共青团员</option>
                     <option value="ybdy">预备党员</option>
                     <option value="zsdy">正式党员</option>
                  </select>
                   <span id="type_msg"></span>
               </td>
            </tr>
            <tr>
               <td>头像：</td>
               <td>
                  <input type="file" name="img"/> <span id="img_msg"></span>
               </td>
            </tr>
            <tr>
               <td>描述信息：</td>
               <td>
                  <!--placeholder占位符-->
                  <textarea placeholder="请输入描述信息~！" id="desc" rows="5" cols="45" name="desc" onfocus="descFocus(this)" onblur="descBlur(this)"></textarea> <br/><span id="desc_msg"></span>
               </td>
            </tr>
            <tr>
               <td>验证码：</td>
               <td>
                  <input type="text" name="valistr"/>
                  <img src="1.jpg" width="100px" height="20px"/>
                   <span id="valistr_msg"></span>
               </td>
            </tr>
            <tr>
               <td colspan="2" align="right">
                  <input type="submit" value="提 交"/>
                  <input type="reset" value="重 置"/>
               </td>
            </tr>
         </table>
      </form>
   </body>
</html>
```

