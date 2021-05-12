[TOC]
# jQuery

#### 一、概念

##### 1.概念

- 概念：完全基于js开发出来的另外一套脚本语言。本质上来说js与jQUERY的功能，作用是完全等价的！！
- jQuery是一个前端技术的脚本框架！！内部完全封装了js脚本。封装其实就是把js的代码以一种更加简单，更加容易学习，更加面向对象的思想设计出来的。特点：简单易学！！！
- 举例：<div id="d1"> </div>
- jquery是第三方开发的一个API文档，在使用之前先把JQUERY-1.4.2.js这个文件导入到项目中！！！
- JQUERY-1.4.2.js里边的代码就是对js的封装逻辑！！（不用看）
- JQUERY-1.4.2.js和JQUERY-1.4.2.min.js区别：
  - JQUERY-1.4.2.js：特点：文档中有注释，代码是换行的，版面比较工整；缺点：文件较大，一般用于学习和测试环境
  - JQUERY-1.4.2.min.js：特点：所有代码没有换行，没有注释；缺点：阅读性较差，但是文件小，生产环境一般使用
  - 两个版本本质没有区别，功能都是相同的

##### 2.jQuery对象和js有什么区别

- jQuery对象：$()
- js对象：document创建
- 对于浏览器而言两者不同，对象格式不一样

##### 3.jquery的优势

* 可以简化JavaScript代码
* 可以像css那样获取元素
* 可以修改css来控制页面效果
* 可以兼容常用的浏览器

##### 4.jquery版本支持

jQuery分为很多版本, 还分为未压缩版和压缩版, 根据需要选择对应的版本进行下载!

1.x  支持常用的浏览器和IE6+

2.x  支持常用的浏览器和IE9+

3.x  支持常用的浏览器和IE9+

注意:  jQuery不兼容老版本. 因为jQuery升级除了会做一些内部优化之外, 还会删除以前的一些代码, 比如删除一些方法. 或者是添加一些新的方法。所以在升级之后， 以前的代码可能会无法执行。

#### 二、jQuery的基本语法

##### 1.jQuery核心用法

1. 1. $("css选择器")选出的元素是jQuery对象，而不是普通的dom对象
   2. $("HTML元素")可以直接将字符串描述的html元素创建出来，构成一个jQuery对象。
   3. $(document).ready(function(){…….});可以设定在页面加载完成之后执行的函数，也可以简写成$(function(){…..})；
   4. 基本格式：$("#d1")  $(".class")  $("div")

##### 2.$介绍

$符号等价于jQuery, 是jQuery单词的简写

例如：$()相当于调用jQuery()，该函数会返回一个jQuery对象, 这个jQuery对象中包含零个或多个html元素, 比如: $("div")，可以通过jQuery中提供的方法来操作这些匹配的元素，比如$("div").remove()；

##### 3.文档就绪事件

所谓的文档就绪事件, 就是在整个html文档加载完之后立即触发, 执行一些操作，格式如下：

```
$(document).ready(function(){

//xxxx

});
```

该函数会在整个html文档加载完之后立即执行! 其作用相当于: 

```
window.onload = function(){ //xxx  }

**其简写形式为****:** 

$(function(){

//xxxx

});
```

##### 4.jquery脚本的引入

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>jquery01-脚本引入</title>
    <!--需要在html页面中引入jquery的脚本，才可以使用jquery-->
    <!--../:返回上一级-->
    <script src="../js/jquery-1.4.2.js"></script>
    <script>
        //案例1：操作div并给div中添加文本元素：hello
        //使用jquery取代js页面加载完毕的代码
        $(function () {
            alert("hello");
            $("#d1").text("hello");
        });
    </script>
</head>
<body>
    <div id="d1"></div>
</body>
</html>
```

##### 5.jquery和js（dom对象）的相互转换

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>jquery02-jquery与js的相互转换</title>
    <script src="../jquery-1.4.2.js" ></script>
    <script>
        $(function () {
            //1.获取jquery的一个对象
            var d1=$("#d1");
            console.log(d1);
            //2.获取js的一个对象
            var d2=document.getElementById("d1");
            console.log(d2);
            //总结：jquery对象是把js对象重新包装了一次
            //jquery对象的第0号元素就是被包装的js对象
            //3.jquery对象转换为js对象
            var jsobj=d1[0];
            console.log(jsobj);
            //4.js对象转换为jquery对象
            var jqobj=$(d2);
            console.log(jqobj);
            //5.jquery如何获取所有的div元素
            var $divs=$("div");
            console.log($divs);
            console.log($divs[0]);
            console.log($divs[5]);//如果超出了数组的范围，返回结果undefined
        });
    </script>
</head>
<body>
    <div id="d1"></div>
    <div ></div>
    <div ></div>
    <div ></div>
</body>
</html>
```

#### 三、jquery选择器

![](https://note.youdao.com/yws/api/personal/file/9E2F7ED8839B4B11B7DD2C85D3AAC0B3?method=download&shareKey=d6552349755385c626ba2554cd719ee2)

##### 1.基本选择器（selector案例一）

元素名选择器，类选择器，id选择器，多元素选择器，*选择器

- 元素名选择器
  - $("div") – 匹配所有的div元素
- class选择器
  - $(".c1") – 匹配所有class值为c1 的元素
  - $("div.c1") – 匹配所有class值为c1的div元素
- id选择器
  - $("#d1") – 匹配所有id值为d1的元素
  - $("div#d1") – 匹配所有id值为d1的div元素
- *号匹配符
  - $("*") – 匹配所有的元素
- 多元素选择器
  - $("div,span,#d1,.c1") – 匹配所有的div/span元素以及id值为d1的元素和class值为c1的元素。。。

```html
<!DOCTYPE>
<html>
<head>
<title>基本选择器练习</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
body{
   font-family: "微软雅黑"
}
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   width: 40px;
   height: 43px;
   background: #CC66FF;
   border: #000 1px solid;
   font-size: 12px;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>
<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   //让整个页面加载完成
   $(document).ready(function() {
      /* ---------基本选择器练习--------- */
      //改变元素名为 <div> 的所有元素的背景色为 #FF69B4"  id="b1"
      $("#b1").click(function () {
         $("div").css("background","#FF69B4");
      });

      //改变 id 为 one 的元素的背景色为 #9ACD32"  id="b2"
      $("#b2").click(function () {
         $("#one").css("background","#9acd32");
      });
      //改变 class 为 mini 的所有元素的背景色为 #FF0033"  id="b3"
      $("#b3").click(function () {
         $(".mini").css("background","#FF0033");
      });

      //改变所有元素的背景色为 #FDF5E6"  id="b4"
      $("#b4").click(function () {
         $("*").css("background","#FDF5E6");
      });

      //改变所有的<span>元素和 id 为 two 的元素的背景色为 #FF1493"  id="b5"
      $("#b5").click(function () {
         $("span,#two").css("background","#FF1493");
      });
      //改变所有的<span>元素和 id 为 two 的,id为one的，class为 mini的所有的元素的背景色为 #006400"  id="b6"
      $("#b6").click(function () {
         $("#two,span,#one,.mini").css("background","springgreen");
      });
   });
</script>
</head>

<body>
   <input type="button" value=" 改变元素名为 <div> 的所有元素的背景色为 #FF69B4" id="b1" />
   <input type="button" value=" 改变 id 为 one 的元素的背景色为 #9ACD32" id="b2" />
   <input type="button" value=" 改变 class 为 mini 的所有元素的背景色为 #FF0033" id="b3" />
   <input type="button" value=" 改变所有元素的背景色为 #FDF5E6" id="b4" />
   <input type="button" value=" 改变所有的<span>元素和 id 为 two 的元素的背景色为 #FF1493"
      id="b5" />
   <input type="button"
      value=" 改变所有的<span>元素和 id 为 two 的,id为one的，class为 mini的所有的元素的背景色为 #006400"
      id="b6" />


   <h1>天气冷了</h1>
   <h2>天气又冷了</h2>

   <div id="one">id为one</div>

   <div id="two" class="mini">
      id为two class是 mini
      <div class="mini"> class是 mini</div>
   </div>

   <div class="one">
      class是 one
      <div class="mini"> class是 mini</div>
      <div class="mini"> class是 mini</div>
   </div>
   <div class="one">
      class是 one
      <div class="mini01"> class是 mini01</div>
      <div class="mini"> class是 mini</div>
   </div>

   <br>
   <div id="mover">动画</div>
   <br>
   <span class="spanone"> span </span>
   <span class="mini"> span class是mini </span>
</body>
</html>
```

##### 2.层级选择器（selector案例二）

如果想通过DOM元素之间的层次关系来获取特定元素。例如子元素、兄弟元素等。则需要通过层次选择器。

- $("div span") – 匹配div下所有的span元素
- $("div>span") – 匹配div下所有的span子元素
- $("div+span") – 匹配div后面紧邻的span兄弟元素
- $("div~span") – 匹配div后面所有的span兄弟元素 

```html
<!DOCTYPE HTML>
<html>
<head>
<title>层级选择器</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   font-size: 14px;
    height: 53px;
    width: 47px;
   background: #CC66FF;
   border: #000 1px solid;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>

<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   $().ready(function() {
      /* ---------层级选择器练习--------- */
      //改变 <body> 内所有 <div> 的背景色为 #F08080"  id="b1"
      $("#b1").click(function () {
         /*层次选择器：父元素下的所有的后代元素 使用空格隔开*/
         $("body div").css("background","#F08080");
      })
      //改变 <body> 内子 <div> 的背景色为 #FF0033"  id="b2"
      /*层级选择器：父节点的直接子节点，不包括孙子节点*/
      $("#b2").click(function () {
         $("body>div").css("background","#FF0033");
      });

      //改变 id 为 one 的下一个 <div> 的背景色为 #0000FF"  id="b3"
      $("#b3").click(function(){
         /*相邻元素选择器，只改变与该节点同一等级的兄弟节点，不包括兄弟节点的子节点*/
         $("#one+div").css("background","#0000FF")
      });

      //改变 id 为 two 的元素后面的所有兄弟<div>的元素的背景色为 #9ACD32"  id="b4"
      $("#b4").click(function(){
         $("#two~div").css("background","#9ACD32");
      });
      //改变 id 为 two 的元素所有 <div> 兄弟元素的背景色为 #FF6347"  id="b5"
      $("#b5").click(function(){
         /*siblings():该节点的所有的同级兄弟节点*/
         $("#two").siblings("div").css("background","#FF6347")
      });
   })
</script>
</head>

<body>
   <input type="button" value=" 改变 <body> 内所有 <div> 的背景色为 #F08080" id="b1" onclick="fn()"/>
   <input type="button" value=" 改变 <body> 内子 <div> 的背景色为 #FF0033" id="b2" />
   <input type="button" value=" 改变 id 为 one 的下一个 <div> 的背景色为 #0000FF"
      id="b3" />
   <input type="button"
      value=" 改变 id 为 two 的元素后面的所有兄弟<div>的元素的背景色为 #9ACD32" id="b4" />
   <input type="button" value=" 改变 id 为 two 的元素所有 <div> 兄弟元素的背景色为 #FF6347"
      id="b5" />

   <h1>天气冷了</h1>

   <div id="one">id为one</div>

   <div id="two">
      id为two
      <div class="mini"> class是 mini</div>
   </div>

   <div class="one">
      class是 one
      <div class="mini"> class是 mini</div>
      <div class="mini"> class是 mini</div>
   </div>
   <div class="one">
      class是 one
      <div class="mini01"> class是 mini01</div>
      <div class="mini"> class是 mini</div>
   </div>

   <br>
   <div id="mover">动画</div>
   <br>
</body>
</html>
```

##### 3.基本过滤选择器（selector案例三）

过滤选择器主要是通过特定的过滤规则来筛选出所需的DOM元素, 该选择器都以 ":" 开头

- $("div:first") – 匹配所有div中的第一个div元素
- $("div:last") – 匹配所有div中的最后一个div元素
- $("div:even") – 匹配所有div中索引值为偶数的div元素，0开始
- $("div:odd") – 匹配所有div中索引值为奇数的div元素，0开始
- $("div:eq(n)") – 匹配所有div中索引值为n的div元素，0开始
- $("div:lt(n)") – 匹配所有div中索引值小于n的div元素，0开始
- $("div:gt(n)") – 匹配所有div中索引值大于n的div元素，0开始
- $("div:not(.one)") – 匹配所有class值不为one的div元素

```html 
<!DOCTYPE HTML>
<html>
<head>
<title>基本过滤选择器</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   font-size: 14px;
    height: 53px;
    width: 47px;
   background: #CC66FF;
   border: #000 1px solid;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>

<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   $(function() {
      /* ---------基本过滤选择器练习--------- */
      //改变第一个 div 元素的背景色为 #FF6347"  id="b1"
      $("#b1").click(function () {
         $("div:first").css("background","#FF6347");
      });
      //改变最后一个 div 元素的背景色为 #9ACD32" id="b2" 
      $("#b2").click(function(){
         $("div:last").css("background ","#9ACD32");
      });
      //改变class不为 one 的所有 div 元素的背景色为 #FF0033" id="b3" 
      $("#b3").click(function(){
         $("div:not(.one)").css("background","#FF0033");
      });
      //改变索引值为等于 3 的 div 元素的背景色为 #006400"  id="b4" 
      $("#b4").click(function(){
         /*这里的索引值是从0开始计数的*/
         $("div:eq(3)").css("background","#006400")
      })
      //改变索引值为小于 3 的 div 元素的背景色为 #FF69B4"  id="b5"/>
      $("#b5").click(function(){
         $("div:lt(3)").css("background","#FF69B4");
      })
      //改变索引值为大于 3 的 div 元素的背景色为 #F08080"  id="b6"
      $("#b6").click(function(){
         $("div:gt(3)").css("background","#F08080");
      });
      //改变索引值为偶数的 div 元素的背景色为 #FF6347" id="b7"
      $("#b7").click(function(){
         $("div:even").css("background","#FF6347");
      });
      //改变索引值为奇数的 div 元素的背景色为 #FF1493" id="b8"
      $("#b8").click(function(){
         $("div:odd").css("background","#FF1493");
      });
   });
</script>
</head>

<body>
   <input type="button" value=" 改变第一个 div 元素的背景色为 #FF6347" id="b1" />
   <input type="button" value=" 改变最后一个 div 元素的背景色为 #9ACD32" id="b2" />
   <input type="button" value=" 改变class不为 one 的所有 div 元素的背景色为 #FF0033" id="b3" />
   <input type="button" value=" 改变索引值为等于 3 的 div 元素的背景色为 #006400" id="b4" />
   <input type="button" value=" 改变索引值为小于 3 的 div 元素的背景色为 #FF69B4" id="b5" />
   <input type="button" value=" 改变索引值为大于 3 的 div 元素的背景色为 #F08080" id="b6" />
   <input type="button" value=" 改变索引值为偶数的 div 元素的背景色为 #FF6347" id="b7" />
   <input type="button" value=" 改变索引值为奇数的 div 元素的背景色为 #FF1493" id="b8" />

   <h1>天气冷了</h1>

   <div id="one">id为one</div>

   <div id="two">
      id为two
      <div class="mini"> class是 mini</div>
   </div>

   <div class="one" title="aa">
      class是 one
      <div class="mini"> class是 mini</div>
      <div class="mini"> class是 mini</div>
   </div>
   <div class="one">
      class是 one
      <div class="mini01"> class是 mini01</div>
      <div class="mini"> class是 mini</div>
   </div>

   <br>
   <div id="mover">动画</div>
   <br>
</body>
</html>
```

##### 4.内容选择器（selector案例四）

- $("div:contains('abc')") – 匹配所有div中包含abc内容的div元素

  如: <div>xxxabcxx</div>

- $("div:has(p)") – 匹配所有包含p元素的div元素

  如: <div><p></p></div>

- $("div:empty") – 匹配所有内容为空的div元素

  如: <div></div>

- $("div:parent") – 匹配所有内容不为空的div元素

  如: <div>xxxxx</div>

##### 5.可见选择器

- $("div:hidden") – 匹配所有隐藏的div元素
- $("div:visible") – 匹配所有可见的div元素

```html 
<!DOCTYPE HTML>
<html>
<head>
<title>内容选择器</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   font-size: 14px;
    height: 53px;
    width: 47px;
   background: #CC66FF;
   border: #000 1px solid;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>

<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   $(function() {
      /* ---------内容选择器--------- */
      //改变含有文本 ‘id’ 的 div 元素的背景色为 #FF6347" id="b1" <div>xxidxx</div>
      $("#b1").click(function(){
         $("div:contains('id')").css("background-color","#FF6347");
      });
      //改变空元素div(既不包含文本也不包含子元素)的背景色为 #9ACD32" id="b2"
      $("#b2").click(function(){
         $("div:empty").css("background-color","#9ACD32");
      });
      //改变包含div子元素的div元素的背景色为 #FF0033" id="b3"
      $("#b3").click(function(){
         $("div:has(div)").css("background-color","#FF0033");
      });
      //改变非空div元素的背景色为 #006400" id="b4"
      $("#b4").click(function(){
         $("div:parent").css("background-color","#006400");
      });
      //====================================================
      /* ---------可见选择器练习--------- */
      //改变所有可见 div 元素背景色为 #F08080" id="b5"
      $("#b5").click(function(){
         $("div:visible").css("background-color","#F08080");
      });
      //让所有隐藏的div元素显示, 并改变背景色为 #FF69B4" id="b6"
      $("#b6").click(function(){
         //show（）将结点显示出来
         // $("div:hidden").css("background-color","red").show();
         $("div:hidden").css("display","block").css("background","springgreen")
      });     
   })
</script>
</head>

<body>
   <input type="button" value=" 改变含有文本 ‘id’ 的 div 元素的背景色为 #FF6347" id="b1" />
   <input type="button" value=" 改变空元素div(既不包含文本也不包含子元素)的背景色为 #9ACD32" id="b2" />
   <input type="button" value=" 改变包含div子元素的div元素的背景色为 #FF0033" id="b3" />
   
   <input type="button" value=" 改变非空div元素的背景色为 #006400" id="b4" />

   <hr/>
   <hr/>

   <input type="button" value=" 改变所有可见 div 元素背景色为 #F08080" id="b5" />
   <input type="button" value=" 让所有隐藏元素显示, 并改变背景色为 #FF69B4" id="b6" />

   <h1>天气冷了</h1>

   <div id="one">id为one</div>

   <div id="two">
      id为two
      <div class="mini"> class是 mini</div>
   </div>

   <div class="one" title="aa">
      class是 one
      <div class="mini"></div>
      <div class="mini"> class是 mini</div>
   </div>

   <br>
   <div id="mover"></div>
   <br>
   <div style="display:none">看不见我...,看不见我....</div>
</body>
</html>
```

##### 6.属性选择器（selector案例五）

- $("div[id]") – 匹配所有具有id属性的div元素
- $("div[id='d1']") – 匹配所有具有id属性并且值为d1的div元素
- $("div[id!='d1']") – 匹配所有id属性值不为d1的div元素
- $("div[id^='d1']") – 匹配所有id属性值以d1开头的div元素
- $("div[id$='d1']") – 匹配所有id属性值以d1结尾的div元素

##### 7.子元素选择器

- $("div:nth-child(n)") – n从1开始, 匹配div中第n个子元素。
- $("div:first-child") –匹配div中第1个子元素。
- $("div:last-child") –匹配div中最后一个子元素。。。

```html
<!DOCTYPE HTML>
<html>
<head>
<title>基本过滤选择器</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   font-size: 14px;
    height: 53px;
    width: 47px;
   background: #CC66FF;
   border: #000 1px solid;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>

<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   $(function() {
      /* ---------属性选择器练习: [属性]/[属性=值]--------- */
      //改变包含id属性的 div 元素的背景色为 #FF6347" id="b1"
      $("#b1").click(function () {
         $("div[id]").css("background","#ff6347");
      })
      //改变包含属性title='aa' 的div 元素的背景色为 #9ACD32" id="b2"
      $("#b2").click(function () {
         /*注意：不包含该节点的子节点，因为子节点没有title=aa的属性*/
         $("div[title='aa']").css("background","#9ACD32")
      })
      //改变type属性不等于button的input 元素的背景色为 #FF0033" id="b3" 
      $("#b3").click(function () {
         $("input[type!=button]").css("background","#FF0033")
      })
      //=================================================

      /* ---------子元素选择器练习--------- */

      //改变div 第二个子元素的背景色为 #006400"  id="b4"
      $("#b4").click(function () {
         /*特别注意：第n个元素，n是从1开始计数的*/
         $("div:nth-child(2)").css("background-color","#006400");
      })
      //改变div 第一个子元素的背景色为 #FF69B4"  id="b5"
      $("div:first-child").css("background-color","#FF69B4");
   })
</script>
</head>

<body>
   <input type="button" value=" 改变包含id属性的 div 元素的背景色为 #FF6347" id="b1" />
   <input type="button" value=" 改变包含属性title='aa' 的div 元素的背景色为 #9ACD32" id="b2" />
   <input type="button" value=" 改变type属性不等于button的input 元素的背景色为 #FF0033" id="b3" />

   <hr/>
   <hr/>

   
   <input type="button" value=" 改变div 第二个子元素的背景色为 #006400"  id="b4" />
   <input type="button" value=" 改变div 第一个子元素的背景色为 #FF69B4"  id="b5" />

   <h1>天气冷了</h1>

   <div id="one">id为one</div>

   <div id="two">
      id为two
      <div class="mini"> class是 mini</div>
   </div>

   <div class="one" title="aa">
      class是 one title为aa
      <div class="mini"> class是 mini</div>
      <div class="mini"> class是 mini</div>
   </div>
   <div class="one" title="bb">
      class是 one title为bb
      <div class="mini01"> class是 mini01</div>
      <div class="mini"> class是 mini</div>
   </div>

   <br>
   <div id="mover">id为mover 动画</div>
   <br>
   <input type="submit"/>
</body>
</html>
```

##### 8.表单选择器（selector案例六）

- $(":input") – 匹配所有的input文本框、密码框、单选框、复选框、select框、textarea、button。
- $(":password") – 匹配所有的密码框
- $(":radio") – 匹配所有的单选框
- $(":checkbox") – 匹配所有的复选框
- $(":checked") – 匹配所有的被选中的单选框/复选框/option
- $("input:checked") – 匹配所有的被选中的单选框/复选框
- $(":selected") – 匹配所有被选中的option选项

```html
<!DOCTYPE HTML>
<html>
<head>
<title>表单选择器</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   font-size: 14px;
    height: 53px;
    width: 47px;
   background: #CC66FF;
   border: #000 1px solid;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>

<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   $(function() {
      /* ---------表单选择器练习：开头的格式是冒号$（”：input“），匹配的是type--------- */
      //改变所有:input 元素的背景色为 #F08080" id="b1"
      $("#b1").click(function(){
         /*：input是所有的input类型包括单选框复选框等（显示不出来因为太小了）*/
         $(":input").css("background","#F08080");
      });
      //改变:password（指的是type） 元素的背景色为 #9ACD32" id="b2"
      $("#b2").click(function(){
         /*：password指的是密码文本框*/
         $(":password").css("background","#9ACD32")
      })
      //弹出 :radio 元素的个数" id="b3"
      $("#b3").click(function(){
         alert($(":radio").length);
      })
      //弹出所有 :checked 元素的value值" id="b4"
      $("#b4").click(function(){
         $(":checked").each(function(){
            /*$(this)指的是当前遍历的数组中的一个元素对象*/
            alert($(this).val())
         });
      });
      //弹出所有 :selected 元素的value值" id="b5"
      $("#b5").click(function(){
         alert($(":selected").val());
      });
   });
</script>
</head>

<body>
   <input type="button" value=" 改变所有input 元素的背景色为 #F08080" id="b1" />
   <input type="button" value=" 改变:password 元素的背景色为 #9ACD32" id="b2" />
   <input type="button" value=" 打印 :radio 元素的个数" id="b3" />
   <input type="button" value=" 打印所有 :checked 元素的value值" id="b4" />
   <input type="button" value=" 打印所有 :selected 元素的value值" id="b5" />

   <h1>天气冷了</h1>
   用户名：<input type="text" disabled="disabled" name="username" value="被禁用了"/>
   密码：<input type="password" name="password" value="123"/>
   性别：<input type="radio" name="gender" value="man"/> 男
      <input type="radio" name="gender" value="woman" checked="checked" />女
   <br>
   <br>
   爱好：
      <input type="checkbox" name="like" value="lanqiu"  checked="checked" /> 篮球
      <input type="checkbox" name="like" value="taiqiu"/> 台球
      <input type="checkbox" name="like" value="zuqiu"/> 足球
   出生地：
      <select name="city">
         <option value="beijing">北京</option>
         <option value="shanghai" selected="selected">上海</option>
         <option value="guangzhou">广州</option>
      </select>
   简介：
      <textarea name="discription" rows="2" cols="10">请求输入个人描述...</textarea>
      
</body>
</html>
```

#### 四、jquery实现qq列表分组

```html
<!DOCTYPE HTML>
<html>
<head>
<title>表单选择器</title>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
<style type="text/css">
div,span {
   width: 140px;
   height: 140px;
   margin: 20px;
   background: #9999CC;
   border: #000 1px solid;
   float: left;
   font-size: 17px;
   font-family: Roman;
}

div.mini {
   font-size: 14px;
    height: 53px;
    width: 47px;
   background: #CC66FF;
   border: #000 1px solid;
   font-family: Roman;
}
input{ margin: 5px 5px; }
</style>

<!--引入jquery的js库-->
<script src="../js/jquery-1.4.2.js"></script>
<script type="text/javascript">
   $(function() {
      /* ---------表单选择器练习：开头的格式是冒号$（”：input“），匹配的是type--------- */
      //改变所有:input 元素的背景色为 #F08080" id="b1"
      $("#b1").click(function(){
         /*：input是所有的input类型包括单选框复选框等（显示不出来因为太小了）*/
         $(":input").css("background","#F08080");
      });
      //改变:password（指的是type） 元素的背景色为 #9ACD32" id="b2"
      $("#b2").click(function(){
         /*：password指的是密码文本框*/
         $(":password").css("background","#9ACD32")
      })
      //弹出 :radio 元素的个数" id="b3"
      $("#b3").click(function(){
         alert($(":radio").length);
      })
      //弹出所有 :checked 元素的value值" id="b4"
      $("#b4").click(function(){
         $(":checked").each(function(){
            /*$(this)指的是当前遍历的数组中的一个元素对象*/
            alert($(this).val())
         });
      });
      //弹出所有 :selected 元素的value值" id="b5"
      $("#b5").click(function(){
         alert($(":selected").val());
      });
   });
</script>
</head>

<body>
   <input type="button" value=" 改变所有input 元素的背景色为 #F08080" id="b1" />
   <input type="button" value=" 改变:password 元素的背景色为 #9ACD32" id="b2" />
   <input type="button" value=" 打印 :radio 元素的个数" id="b3" />
   <input type="button" value=" 打印所有 :checked 元素的value值" id="b4" />
   <input type="button" value=" 打印所有 :selected 元素的value值" id="b5" />

   <h1>天气冷了</h1>
   用户名：<input type="text" disabled="disabled" name="username" value="被禁用了"/>
   密码：<input type="password" name="password" value="123"/>
   性别：<input type="radio" name="gender" value="man"/> 男
      <input type="radio" name="gender" value="woman" checked="checked" />女
   <br>
   <br>
   爱好：
      <input type="checkbox" name="like" value="lanqiu"  checked="checked" /> 篮球
      <input type="checkbox" name="like" value="taiqiu"/> 台球
      <input type="checkbox" name="like" value="zuqiu"/> 足球
   出生地：
      <select name="city">
         <option value="beijing">北京</option>
         <option value="shanghai" selected="selected">上海</option>
         <option value="guangzhou">广州</option>
      </select>
   简介：
      <textarea name="discription" rows="2" cols="10">请求输入个人描述...</textarea>
      
</body>
</html>
```

#### 五、更多操作

##### 1.文档操作

parent() ---$("#d1").parent() – 获取id为d1元素的父元素

parents()---

​	$("#d1").parents() – 获取id为d1元素的祖先元素

​	$("#d1").parents("tr") – 获取id为d1元素的tr祖先元素

next()---

​	$("div").next() – 获取所匹配元素后面紧邻的兄弟元素

​	$("div").next("span") – 获取所匹配元素后面紧邻的span兄弟元素

 nextAll()---

​	$("div").nextAll() – 获取所匹配元素后面所有的兄弟元素

​	$("div").nextAll("span") – 获取所匹配元素后面所有的span兄弟元素

 prev()---

​	$("div").prev() – 获取所匹配元素前面紧邻的兄弟元素

​	$("div").prev("span") – 获取所匹配元素前面紧邻的span兄弟元素

 prevAll()---

​	$("div").prevAll() – 获取所匹配元素前面所有的兄弟元素

​	$("div").prevAll("span") – 获取所匹配元素前面所有的span兄弟元素

 siblings()---

​	$("div").siblings() – 获取所匹配元素前后所有的兄弟元素

​	$("div").siblings("span") – 获取所匹配元素前后所有的span兄弟元素

append()---$("div").append("<span></span>") –为所匹配元素追加一个span子元素

remove()---$("div").remove() – 删除所匹配元素 

html()---

​	$("div").html() – 获取所匹配元素的html内容

​	$("div").html("xxx") – 为所匹配元素设置html内容

text()---

​	$("div").text() – 获取所匹配元素的文本内容

​	$("div").text("xxx") – 为所匹配元素设置文本内容

attr()---

​	$("div").attr("id") – 获取所匹配元素的id属性值

​	$("div").attr("id", "xx") – 为所匹配元素设置id属性

 css---

​	$("div").css("width") – 获取所匹配元素的width样式属性值

​	$("div").css("width", "200px") – 为所匹配元素设置width样式属性

​	$("div").css({"width":"200px", "color":"red","font-size":"24px" }) ;    – 为所匹配元素设置width样式属性

##### 2.事件

click()---$("div").click(function(){}) – 为所匹配元素绑定点击事件 

blur()---$("input").blur(function(){}) – 为所匹配元素绑定失去输入焦点事件

focus()---$("input").focus(function(){}) – 为所匹配元素绑定获得输入焦点事件

change()---$("select").change(function(){}) – 为所匹配元素绑定选项切换事件 

ready() ---$(document).ready(function(){}) – 文档就绪事件

​	其作用相当于:  window.onload = function(){}

​	简写形式为:---$(function(){}) – 在整个文档加载完成后立即执行

##### 3.效果

show()---$("div").show() – 将隐藏元素设置为显示(底层操作的是display);

hide()---$("div").hide() – 将显示元素设置为隐藏(底层操作的是display);

toggle()---$("div").toggle() – 切换元素的可见状态, 如果元素显示则设置为隐藏, 如果元素隐藏则设置为可见.。