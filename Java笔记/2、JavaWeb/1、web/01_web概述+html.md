[TOC]
# web概述+html

#### web概述

什么是web？

 web是一种简称，www的另一种称呼，world wide  wite 环球信息网，中文称为万维网

web的基本模式：web浏览器+web服务器

![](https://note.youdao.com/yws/api/personal/file/20955610042A489BA70E86814523F36C?method=download&shareKey=adaa94a8e928976d762028a9e08c1ebb)

javaweb=java+web=java（jdbc技术）+web浏览器（html/css/jss/jquery）+web服务器（servlet+jsp）

javaweb技术应用场景：

javaweb在当今互联网的优势：

- C/S架构：   Client-Server 客户端服务器模型    QQ         LOL
  - 优点: 客户端可以任意的设计, 页面的展示能力就可以很强. 由于大量的资源都已经保存在了客户端, 和服务器交互的仅仅是一些变化的数据, 所以对网速的依赖很低
  - 缺点: 第一次使用时需要下载客户端程序, 一旦程序需要升级操作, 所有的客户端程序都需要升级. 在有些场景中是不能被接受的.
-  B/S:      Browser-Server 浏览器服务器模型 WEBQQ 网页游戏
  - 优点: 不需要下载客户端程序, 使用浏览器可以直接访问. 程序的升级操作是在服务器端进行的. 浏览器只需要刷新页面就可以看到升级后的效果
  - 缺点: 浏览器具有一定的局限性, 页面的展示能力仍然是很差. 所有的页面数据都需要从服务器实时的获取, 所以对网速的依赖很高

#### html

##### html: HyperText  Markup Language超文本标记语言

- 文本：记事本，记事本只能写文字

- 超文本：不止可以写文字，还能够写图片，音乐，超链接等等，展示的内容更加丰富。

- 标记语言：html一门专门编写页面的语言

- 标记：指的是标签，标签的格式<>

  比如：标记一张图片<img>

- HTML语言没有逻辑存在只有标签

- HTML文件后缀 ：.html

- HTML文件中所有的标签，需要遵循w3c的标准

##### HTML的基本结构

```html
<!--注释快捷键和java的快捷键一样-->
<!--doctype指的是描述文档的类型。html表示该文档是html页面-->
<!DOCTYPE HTML>
<html><!--根标签：编写所有的标签-->
    <!--head标签是头部标签，里边存放了页面的基本属性-->
    <head>
        <!--meta标签设置页面编码的字符集,html5.0的写法-->
        <meta charset="UTF-8" />
        <!--title标签是页面的标题标签-->
        <title>demo01-html的基本结构</title>
    </head>
    <!--body标签是身体标签，主要是页面中的内容信息-->
    <body>
    body主要作用：用于浏览器页面的展示
    </body>
</html>
```

##### HTML的基本语法

###### 单标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>demo02-html的基本语法</title>
</head>
<body>
    <!--br/换行-->
    HTML的标签的认识:标签的基本分类<br/>
    <!--hr/下划线-->
    <hr/>
    1.双标签：开始标签，结束标签<br/>
    2.单标签(自闭标签)：指的是标签中没有内容<br/>
    <!--转义字符：主要是为了特殊的符号，避免浏览器直接解析他而进行的转义-->
    <!--&lt;转义为<-->
    <!--&gt;转义为>-->
    <!--&quot;转义双引号-->
    <!--&nbsp;转义为空格-->
    <!--&apos;转义为单引号-->
    &lt;br/&gt;是一个换行标签
    郭富城是&quot;亚&nbsp;&nbsp;洲&nbsp;&nbsp;舞&nbsp;&nbsp;王&quot;
    郭富城身高&apos;1.72&apos;米
</body>
</html>
```

###### 双标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>demo03html常用双标签</title>
</head>
<body>
    <!--font标签 ：指定字体的大小/颜色-->
    <!--font标签的属性：color指定字体的颜色,
    color属性的值：1.直接指定颜色的单词名称
                  2.指定十六进制的像素  #123456 #aaffdd
                  3.使用三基色rgb(20,20,20)  ie浏览器使用-->
    <!--size属性：字体的大小  编号1-7有效，如果值超过7就默认是7-->
    <font color="rgb(90,90,90)" size="6">字体标签font</font>
    <!--h1-h6标题标签：指的是标题的级别，从一级到六级-->
    <!--标题标签的属性：align字体的排列方式
                   align=”left/right/center/justify“
                   left/right/center是常用的
                   justify自适应，默认从左到右布局排列，不常用-->
    <h1 align="left">one</h1>
    <h2 align="right">two</h2>
    <h3 align="center">three</h3>
    <h4 align="justify">four</h4>
    <h5>five</h5>
    <h6>six</h6>
    <!--<h7>test</h7>-->
    <!--列表标签：无序列表-->
    <!--无序列表的属性：type
               circle：空心小圆点
               square:方块
               disc:默认的类型 实心小圆点-->
    <ul type="square"><!--定义一个无序列表-->
        <li>华为</li><!--表示列表项，可以有多个-->
        <li>小米</li>
        <li>联想</li>
    </ul>
    <!--列表标签：有序列表-->
    <ol>
        <li>1</li><!--表示列表项，可以有多个-->
        <li>2</li>
        <li>3</li>
    </ol>
    <!--img标签：单标签-->
    <!--必要的属性：src表示图片的存储路径
             alt表示替换图片的文字，只有在图片不存在时显示
    可选属性：width：宽度 单位px表示像素  %：占整个浏览器的百分比
             height：高度
             border:边框-->
    <img src="image/3.jpg" alt="123" width="50%" height="50%" border="10px"/>
    <!--超链接：a标签-->
    <!--必要属性：href指的是访问的资源的URL路径
                 target指的是页面的跳转方式
                        _self:默认的当前窗口打开链接
                        _blank：在新窗口打开一个链接-->
    <a href="http://www.tmooc.cn"  target="_blank">教育网站</a>
    <a href="demo02.html" target="_blank"> demo02</a>
    <!--超链接与锚点配合使用：指定位置跳转-->
    <!--锚点：name属性用于定位当前的位置-->
    <a name="top">小说目录</a>
    <p>yi</p>
    <p>yi</p>
    <!--返回顶部的超链接-->
    <a href="#top">返回顶部</a>
</body>
</html>
```

###### 表格标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>demo04-table表格标签</title>
</head>
<body>
    <!--table表格标签：是一套行和列组合的标签-->
    <!--成员：tr标签：行标签
             td标签：列标签
             th标签：列标签标题-->
    <!--table的属性：
            border：边框
            cellspacing:两个单元格之间的间距，外边距
            cellpadding:内边距，一个单元格内填充的距离
            bgcolor:背景颜色
            bordercolor：边框的颜色
            align；整个表格相对于浏览器居中-->
    <table border="2px" cellspacing="5px" cellpadding="5px"
           bgcolor="#ffc0cb" bordercolor="red" width="50%" align="center">
        <!--定义表格的标题-->
        <caption>表格标题</caption>
        <!--tr的属性：align：一整行所有的列居中
                     bgcolor：一整行的背景颜色-->
        <tr align="center" bgcolor="#7fffd4"><!--这是一行-->
            <td>华为</td><!--这是一列-->
            <td>小米</td>
            <td>苹果</td>
        </tr>
        <tr><!--这是一行-->
            <!--td的属性：colspan:可跨多少列 col->column
                         rowspan:可跨多少行-->
            <td colspan="2">mate30</td><!--这是一列-->
            <td rowspan="2">青春版</td>
            <td>iphone11</td>
        </tr>
        <tr><!--这是一行-->
            <!--th标签：自动字体加粗并居中-->
            <th>mate30pro</th><!--这是一列-->
            <th>红米</th>
            <th>iphonex</th>
        </tr>
    </table>
</body>
</html>
```

###### form表单

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>demo05-form表单</title>
</head>
<body>
    <!--浏览器传递参数的两种方式:
                1.超链接传递参数 一般用于查询数据的！！！-->
    <!--参数传递的固定格式：链接后面跟一个英文的?
                          参数是key=value的键值对
                          多个参数的键值对之间使用&隔开-->
    <!--特点：该方式地址栏中写的长度有限制；
             该方式地址栏中会显示出每一个参数的信息-->
    <a href="http:\\www.tmooc.cn?username=456&passworld=kk">教育网站</a>
    <!--form表单：主要作用是给用户通过浏览器维护数据使用-->
    <!--2.使用form表单传递参数-->
    <!--必要属性：action 表示form表单提交的URL路径
                 method form表单提交的方式-->
    <!--method方式有两种：get方式（和超链接里的方式一样会有？后边的内容）默认情况是get方式
                        post方式:一般form表单提交数据时-->
    <!--name属性：传递参数时文本框一定要加上name属性，如果不加提交的参数就没有该文本框-->
    <form action="http:\\www.tmooc.cn" method="post">
        <!--form表单中的项-->
        <!--1.input输入框-->
        用户名:<input type="text" name="username" /><br/>
        <!--2.input密码框-->
        密码:<input type="password" name="password" size="10"/><br/>
        <!--3.input单选框 选择性别男女。-->
        <!--name属性表示单选框的内容划分为一组，一组中只能选中一个-->
        <!--checked被选中的-->
        性别:<input type="radio" name="gender" value="male" checked="checked"/>男
             <input type="radio" name="gender" value="female"/>女<br/>
        <!--4.input复选框-->
        爱好:<input type="checkbox" name="hobby" value="sing" />唱
            <input type="checkbox" name="hobby" value="dance"/>跳
            <input type="checkbox" name="hobby" value="rap"/>rap<br/>
        <!--5.input隐藏框-->
        <!--value是文本框中的默认值-->
        评价：<input type="hidden" value="看似" /><br/>
        <!--6.input文件上传框-->
        上传:<input type="file"  /><br/>
        <!--7.input图片按钮-->
        点我哦：<input type="image" src="image/5.jpg" width="50px" height="50px"/><br />
        <!--8.input提交按钮-->
        <input type="submit" value="注册" />
        <!--9.input重置按钮-->
        <input type="reset" value="重置" /><br />
        <!--10.input日期-->
        <input type="date" /><br>
        <!--文本域：textarea-->
        <!--disabled禁用-->
        <!--readonly只读-->
        <textarea cols="100" >123456</textarea><br />
        <!--下拉框：select+option组合标签-->
        <select><!--定义一个下拉框-->
            <option>北京</option>
            <option>上海</option>
            <option>西安</option>
            <option>大理</option>
            <option>东北</option>
        </select>
    </form>
</body>
</html>
```























