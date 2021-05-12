[TOC]
# CSS

#### css样式

##### 网页组织的两种常用方式

1. 表格套表格方式定义网页结构--目前不再是主流，只在一些简单的页面中有所使用
2. div+css方式定义网页结构--目前主流的网页开发方式，可以非常灵活的定义网页。

##### 概念

- css单独使用没有意义必须配合html页面一起使用

- css:层叠样式表 

- html负责页面布局，css负责页面美化

- css的主要作用

  用于装饰html页面，美化页面

- 单独使用html的属性，比如border，bgcolor等等，远远不够进行美化的工作，所以需要新的技术css

- css四种引入方式：行内样式表，内联样式表（写在head里），外部样式文件.css

##### 容器标签

本身没有任何的特殊能力，最主要的功能是用来包含其他标签组成一个整体。

常见的容器标签：div span p

<div>是一个块级元素，这意味着它的内容自动的开始一个新行

```
<div>是一个块级元素，这意味着它的内容自动的开始一个新行
<span>是一个行内元素，多个行内元素不会要求独占一行
<p>是一个块级元素，用来声明一个段落。会在当前段落前后多出额外的空行。
```

##### 基础语法格式

/**/ --->css中的注释只有这一种。
p{
    background-color: darkcyan;
    color: aliceblue;
}

##### 四种引入方式

如果多种方式为同一元素设定了样式，则样式起作用的原则是：从上到下，由外到内，优先级由低到高（编辑样式的代码那个里标签近那个有效）

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>demo06-css样式</title>
    <!--css样式语法3：外部样式表-->
    <link rel="stylesheet" href="demo06.css" />
    <!--css样式语法2：内部样式表，只在当前html中有效
             特点：如果遇到方式一行内样式，那么会优先使用行内样式
             缺点：代码的重用性较低-->
    <style>
      /*  !*css的注释与html完全不同*!
        !*1.对所有的div添加背景颜色*!
        div {
            background-color: pink;!*背景颜色*!
            color: #3fff4a;!*字体颜色*!
        }*/
        /*css样式语法4：import导入外部文件 */
        @import "demo06.css";
    </style>
</head>
<body>
    <!--容器（元素）标签：div  span p -->
    <!--容器标签单独使用，也就是不存放任何其他标签时是没有意义的-->
    <!--div容器标签：块状元素，把浏览器划分成不同的块，
                    div的内容，自动的占有浏览器一行-->
    <!--css样式语法1：行内样式表
        缺点：1.所有样式都写在一个标签中，代码不够简洁
             2.样式只在一个标签中有效，其他标签无效-->
    <div style="background-color:#ffff3e;color: #3fff4a">one</div>
    <div>two</div>
    <div>three</div>
    <!--span元素：行内元素，不会要求独占浏览器一行（不会换行）-->
    <span>span1</span>
    <span>span2</span>
    <span>span3</span>
    <!--p段落元素：块级元素，和div相同独占一行，
    但是会在段落前后多出空白行-->
    <p>p1</p>
    <p>p2</p>
    <p>p3</p>
</body>
</html>
```


#### css选择器

选择器的主要作用：选择html中的标签，对标签做样式的装饰

css选择器的分类：基本选择器，扩展选择器

##### 基本选择器（3种）

标签名选择器；类选择器；id选择器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>css01-css选择器</title>
    <style>/*内嵌样式表*/
    /*1.元素选择器：根据标签名选择同一类型的标签*/
    /*语法格式：标签名{}*/
    div{
        color: cadetblue;
        background-color: yellow;
    }
    /*2.id选择器：每一个html标签都有一个id属性，id是标签的唯一标示*/
    /*语法格式：#id属性的值{}*/
    #d1{
        color: pink;
        background-color: aqua;
    }
    /*3.class选择器（类选择器）：根据标签中的class选择器选择，可选多个*/
    /*语法格式：.class属性的值{}*/
    .c1{
        color: bisque;
        background-color: cadetblue;
        }

    </style>
</head>
<body>
    <!--容器标签-->
    <div>1</div>
    <div>1</div>
    <div id="d1">id选择器</div>
    <div class="c1">class选择器</div>
    <div class="c1">class选择器</div>
    <div class="c1">class选择器</div>   
</body>
</html>
```

#####   扩展选择器（6种）

后代选择器；子元素选择器；分组选择器；属性选择器；相邻兄弟选择器；伪元素选择器

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>css01-css选择器</title>
    <style>/*内嵌样式表*/
    /* 4.后代选择器：html标签有多层嵌套关系，不止有子节点还有孙子节点等等*/
	/*语法格式：父元素选择器 后代元素选择器{}*/
    div span{
        color: blue;
    }
    /*5.子元素选择器：选择特定的标签的子节点，不包含孙子节点*/
    /*语法格式：父元素选择器>子元素选择器{}*/
    #d2>p{
        color: pink;
    }
    /*6.分组选择器：可以同时选择多个不同的标签，分成一组设置样式*/
    /*语法格式：选择器1,选择器2,....{}*/
    span,p,#d1{
        color: crimson;
    }
    /*7.属性选择器：*/
    /*包含所有属性type的标签*/
    *[type]{
            background-color: crimson;
    }
    /*可以选择指定标签的某个属性*/
    input[value]{
        background-color: cadetblue;
    }
    /*可以选择指定标签的某个属性=“值”的形式*/
    input[name="password"]{
        background-color: bisque;
    }
    /*可以选择指定标签的多个属性键值对的形式*/
    input[type][name][value]{
        background-color: springgreen;
    }
    /*8.相邻兄弟选择器*/
    /*语法格式：选择器+兄弟元素名{}*/
    #d1+div{
   	 	background-color: cadetblue;
    }
    /*9.伪元素选择器：伪指的是html已经为我们提供好的选择器*/
    a:link{
        background-color: blueviolet;
    }
    a:visited{/*被点击过的状态*/
        background-color: aqua;
    }
    a:hover{/*鼠标停留的状态*/
        background-color: pink;
    }
    a:active{/*鼠标正在点击的状态*/
        background-color: cadetblue;
    }
    </style>
</head>
<body>
    <!--容器标签-->
    <div>1</div>
    <div>1</div>
    <div id="d1">id选择器</div>
    <div class="c1">class选择器</div>
    <div class="c1">class选择器</div>
    <div class="c1">class选择器</div>
    <!--页面布局是嵌套的形式-->
    <div id="d2">
        测试后代选择器
        <div>这是子节点div
            <span>孙节点span</span>
            <p>这是孙节点p</p>
        </div>
        <span>这是子节点span</span>
        <p>这是子节点p</p>
    </div>
    <!---->
    <input type="text" name="username" value="测试" />
    <input type="text" name="password" />
    <input type="text" name="email" />
    <a href="#">测试伪元素选择器</a>
</body>
</html>
```

#### HTML+CSS的综合练习

思路一：使用table表格设计注册页面

思路二：使用div容器标签和css样式设计

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册页面</title>
    <style>
        #center{
            width: 45%;/*相对于body说的*/
            height:750px;
           /* border: 2px solid pink;*/
            margin: 0 auto;/*外边距，上下距离0px，左右距离  自动居中*/
            text-align: center;/*文本居中*/

        }
        h1{
            color: pink;
        }
        #register{
            width: 100%;/*相对于上一级的div*/
            height: 660px;
            border: 2px solid pink;
            border-bottom: 0px;/*为了解决底部线条重合问题*/
            background-color: bisque;/*注册表单的背景色*/
        }
        .rows{
            width: 100%;
            height: 53px;/*每一行的高度*/
            border-bottom:  2px solid pink;/*每一行的底边显示*/
            line-height: 53px;/*行高垂直居中*/
        }
        .cols1{
            width: 30%;
            height: 53px;
            border-right: 2px solid pink;
            float: left;/*div飘逸到左边*/
        }
        .cols2{
            width: 68%;
            height: 53px;
           /* border: 2px solid black;!*做测试使用*!*/
            float: right;/*div飘逸到右边*/
        }
        textarea{
            resize: none;/*禁止拖拽文本域的大小*/
        }
    </style>
</head>
<body>
    <div id="center">
        <h1 >注册表单</h1>
        <div id="register">
            <form>
                <div class="rows">
                    <div class="cols1">
                        用户名：
                    </div>
                    <div class="cols2">
                        <input type="text" name="username" />
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        密码：
                    </div>
                    <div class="cols2">
                        <input type="password" name="password" />
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        确认密码：
                    </div>
                    <div class="cols2">
                        <input type="password" name="password1" />
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        昵称：
                    </div>
                    <div class="cols2">
                        <input type="text" name="name" />
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        邮箱：
                    </div>
                    <div class="cols2">
                        <input type="text" name="email"  />
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        上传头像：
                    </div>
                    <div class="cols2">
                        <input type="file" name="file" />
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        性别：
                    </div>
                    <div class="cols2">
                        <input  type="radio" name="gender" value="mail" />男
                        <input  type="radio" name="gender" value="femail" />女
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        爱好：
                    </div>
                    <div class="cols2">
                        <input type="checkbox" name="hobby" value="eat" />吃饭
                        <input type="checkbox" name="hobby" value="slepp" />睡觉
                        <input type="checkbox" name="hobby" value="play" />玩
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        所在城市：
                    </div>
                    <div class="cols2">
                        <select name="s1">
                           <option>北京</option>
                           <option>大理</option>
                           <option>西安</option>
                           <option>上海</option>
                        </select>
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        自我介绍：
                    </div>
                    <div class="cols2">
                        <textarea maxlength="50" cols="28" rows="3">最多写50个字符</textarea>
                    </div>
                </div>
                <div class="rows">
                    <div class="cols1">
                        验证码：
                    </div>
                    <div class="cols2">
                        <input type="text" name="check" />
                        <!--<img src="" alt="验证码" />-->
                        <input type="button" value="点" >
                    </div>
                </div>
                <div class="rows">
                    <div class="cols3">
                        <input type="submit" />
                        <input  type="reset" />
                    </div>
                </div>

            </form>
        </div>
    </div>
</body>
</html>
```

