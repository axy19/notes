[TOC]
# JS

#### js概述

- js是JavaScript的缩写，JavaScript是目前前端技术中非常流行的
- js是一门编程语言（具有逻辑性，语法与java有很多相似之处）
- js是浏览器端的技术，主要是为html页面增加一个动态的效果
- js可以说是为html这种页面布局进行提供动态支持
- js的好处：
  - 任何浏览器上都可以运行，跨平台！！！
  - 浏览器能够直接解析js代码
- js一般叫做web前端的脚本语言

#### js不得不说的历史

和JAVA的关系：语法十分相似，但其实毫无关系。

1995年5月，Netscape(网景公司)，LiveScript

1995年12月，改名为JavaScript

1996年8月，微软，Jscript

1997-1999年，ECMA，ECMAScript，基于已有的JavaScript和Jscript，提出了标准的Script语法规则，JavaScript和Jscript都遵循这套标准。      

现在为止 js的标准制定发展到了ECMAScript6.0标准

#### js和html的结合

js单独使用的话，可以单独写出页面小游戏（比如飞机大战等等）

js与html结合使用可以做出目前互联网网站的所有特效！！！

js与html如何结合？也就是js的一种写法！！

 常用的写法：head标签中，外部文件引入，页面的底部

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js01-基本用法</title>
    <!--书写方式一：在head标签中写-->
    <!--特点：对不同的html可以编写独特的js脚本，其他的页面不能使用-->
    <script>
        /*打印语句*/
        alert(123);
    </script>
    <!--书写方式二：引入外部的.js文件-->
    <!--特点：任何html都可以重复使用-->
    <!--使用双标签，不能进行自闭标签的书写，否则js失效-->
    <script  src="js01.js"></script>
</head>
<body>
    <!--书写方式四：可以写在html的某个标签中-->
    <a href="javascript:alert(5555)">测试连接</a>
</body>
    <!--书写方式三：js代码写在页面的底部-->
    <script>
        alert(789)
    </script>
</html>
```

#### JavaScript的输出语句

js的输出方式一般有四种：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js-02输出语句</title>
    <!--四种输出写法-->
    <script>
        /*1.弹窗打印*/
        alert(1);
        /*2.浏览器的控制台打印：主要是为了打桩测试！！！*/
        console.log(2);
        /*3.向浏览器的body中输出信息*/
        document.write("hello");                    
    </script>
</head>
<body>
    <p id="p1">测试段落</p>
</body>
<script>
    /*4.对html页面的某一个标签做修改（写在前边会空）*/
    document.getElementById("p1").innerText="这是新的内容";
</script>
</html>
```

#### JavaScript的基础语法

浏览器在解析整个html页面时，是自上而下顺序解析的。js代码同样是默认的自上而下的顺序解析。

如果js脚本出错了，那么你的调试方式是打开F12，进入浏览器的console控制台

 ==JavaScript是一种弱类型的语言！！！！==

js定义变量时的类型只有一中声明方式：var x;

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js03-基础语法</title>
    <script>
        /*1.javaScript是弱类型语言，所有数据类型统一使用var声明*/
        var a=10;
        var b=10.1;
        var c=true;
        var d="字符串";
        var e='字符串么么么么';
        var f="y";
        var g=new Object();
        console.log(a);
        console.log(b);
        console.log(d);
        /*2.JavaScript的语句结尾处可以不写分号，
        因为浏览器在解析时js脚本中的解析器会自动添加分号*/
        console.log(e)
        console.log(g)
        /*3.单引号和双引号的区别
        *   单独成句的时候，没有区别，都是表示字符串
        *   单引号与双引号混合使用时，一定要成对出现，
        *   而且双引号里面不能直接在写双引号
        * */
        var x="hello";
        var y='hello';
        console.log(x==y);//结果为true，表示单引号和双引号没有区别
        console.log(x===y);//结果为true，值相等，证明两者完全一样
        var x1="'您好'";//输出结果：’您好‘
        var x2='"hello"';//输出结果 "hello"
        console.log(x1==x2);//false
    </script>
</head>
<body>
```

#### js的基本数据类型--6种

- ##### Number类型（数值类型）

  NaN 非数字，非数字非常特殊，和任何值都不相等，包括本身，即NaN==NaN的值为false。可以通过isNaN()判断某值是否为非数字，true表示确实为非数字的值，false表示为数字的值。

```html
/*1.Number类型：表示数值类型，并为数值之间的转换提供方法*/
//1.1常用的属性
console.log(Number.MAX_VALUE);//能够表示的最大值1.7976931348623157e+308
console.log(Number.MIN_VALUE);//最小值5e-324
console.log(Number.POSITIVE_INFINITY);//超出最大值是无穷大（Infinity）
console.log(Number.NEGATIVE_INFINITY);//超出最小值   无穷小(-Infinity)
console.log(Number.NaN);//非数字
//1.2number的精度问题
var a=0.9;
var b=0.99999999;
var a1=0.2;
var a2=0.2;
console.log(a);
console.log(b);
console.log(a1+a2);
//1.3Number类型的数值：底层默认都是浮点型
var a3=10;
var a4=10.0;
console.log(typeof a3);
console.log(typeof a4);
//1.4Number类型的八进制和十六进制
var n1=0o377;//八进制前缀是0或者是 0o
var n2=0xddd;//十六进制前缀0x
//1.5判断一个数值是否为数字
/*isNaN是一个全局方法，调用时不应该写*/
var f=isNaN("ab型");//结果为true表示非数字，结果为false表示是数字
console.log(f);
```

- ##### String类型（字符串类型）

```html
/*2.String类型：字符串*/
//2.1String属性：length长度
var s1="abcdeADCVCfghi";
console.log(s1.length)
//2.2String的常用方法：indexof返回结果是下标值（从0开始）
console.log(s1.indexOf('i'));
//2.3字符串的索引：s1[] 返回结果是下标对应的某一个字符
console.log(s1[3]);
//2.4字符串的匹配：s1.match(),
// 返回结果如果匹配上返回匹配的结果对象，
// 如果没有匹配上返回匹配的结果是null
var s2=s1.match('c')
console.log(s2);
//2.4字符串的拆分  返回结果是一个数组
console.log(s1.split(''));
//2.5字符串的大小写转换
console.log(s1.toLocaleUpperCase());//转为大写
console.log(s1.toLocaleLowerCase());//转为小写
```

- ##### Boolean类型（布尔类型）

```html
/*3.Boolean类型：true，false*/
var b1=true;
var b2=false;
console.log(b1&&b2);//与  并且  false
console.log(b1||b2);//或者  true
console.log(b1|b2);//结果 1 表示true
console.log(b1&b2);//结果0，表示false
```

- ##### Null类型(空)

  Null只有一个值就是null。null用来表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。

```html
/*4.Null空类型，什么都没有*/
var x1=null;
console.log(x1)//
// 这里的方法IsNull是官方文档中提供的。
// 如果是null返回true，如果不为null返回false
console.log(IsNull(x1));
```

- ##### Undefined类型（未定义）

```
/*5.Undefined:未定义，声明变量时没有直接初始化*/
var x2;
console.log(x2);
```

- ##### Symbol类型（唯一，不与任何其他值相等）

```html
/*6.Symbol类型：唯一性,数值不相等*/
var m1=Symbol(1);
var m2=Symbol(1);
console.log(m1==m2);//false
console.log(m1===m2);//false
```

#### js的变量定义和使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js05-变量的定义和使用</title>
    <script>
        var a=0;
        var b=1,c=2;
        /*如果变量没有定义时，直接使用：写代码时不会报错，执行时报错*/
        // console.log(x);
        /*如果两个变量重名，那么js中会：把上一个变量覆盖掉*/
        var a=1;
        console.log(a);
    </script>
</head>
<body>

</body>
</html>
```

#### js的运算符

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js06-运算符</title>
    <script>
        /*1.算数运算符：+ - * / % ++ -- */
        /*2.逻辑运算符：|| && ！*/
        /*3.位运算符：| & */
        /*4.关系运算符 > <  ==()  ===(值)  <= >=*/
        var a=10;
        /*if(a=20)  先进行赋值操作，那么a变成20
        *  就相当于if(20)    输出结果为20*/
        /*原因：js中如果是0或者null代表的是false
        *       如果是非0或者是非null代表的就是true*/
        if(a=20){
            console.log(a);
        }else{
            console.log(1)
        }
        /*5.赋值运算符   += -= *= /= %=*/
        /*6.三目运算符*/
        var y=10>20?true:false;
        console.log(y)
    </script>
</head>
<body>
</body>
</html>
```

#### js的数据类型转换

##### 自动转换

![](https://note.youdao.com/yws/api/personal/file/EA27E6C64B6244BCBC5611B9F0B29F73?method=download&shareKey=509b45054692041a38d703263fc32c0f)

自动转换规则

| 数字     | 可以在需要时转换为对应的字符串形式。0会转换为false，其他数字会转换为true.在需要对象时可以自动转换位Number对象。 |
| -------- | ------------------------------------------------------------ |
| 字符串   | 可以在需要时转换位对应的数值，需要注意当数值和字符串进行加法运算时，会处理为字符串的拼接，所以需要通过parseInt或parseFolat将字符串强制转换为数值类型参与运算。非空字符串转换为true，空字符串转换为false。需要对象时自动转换为String对象 |
| 布尔类型 | true转换位1，false转换位0.转换为字符串的"true"和"false".转换为Boolean对象 |
| 对象类型 | 如果为null则转成字符串"null",如果不为null转换为false         |

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js03-数据类型转换</title>
    <script>
        /*问题：小括号中的参数如何自动转换*/
        /*答案：js内部的函数在执行时，
        会根据函数的参数以及函数的定义自动的尝试去转换为我们期望的类型*/
        console.log(isNaN(1));//false  是数字
        console.log(isNaN("1"));//false-->证明自动转换了
        console.log(isNaN("123sdc"));//true--->自动转换失败
        console.log(isNaN(" "));//false-->空格自动转为0，在判断是否为nan
        console.log(isNaN(null));//false-->null自动转为0，在判断是否为nan
        console.log(isNaN(true));//false-->true自动转为1，再判断是否为nan
        /*通过js的函数进行强制类型转换*/
        /*1.将数字转换为非数字*/
        var a1=10;
        console.log(String(a1));
        console.log(a1.toString())
        /*2.将非数字转换为数字:主要是使用Number()*/
        var a2="        ";//多个空格转为数字时，都成了0
        var a3="123ec";//如果是一个非数字的字符串，会变成nan表示非数字
        var a4=true;//true-->1  false-->0
        var a5=new Date();//结果是日期的毫秒数
        console.log(Number(a2));//0
        console.log(Number(a3));//NaN
        console.log(Number(a4));//1
        console.log(Number(a5));//1575339602118
    </script>
</head>
<body>
</body>
</html>    
```

#### js语法---语句

##### 分支结构

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js01-分支结构</title>
    <script>
        /*分支结构的语法与java里的完全一样*/
        var a = 10;
        if (a > 10) {
            alert(">")
        } else if (a == 10) {
            alert("=")
        } else {
            alert("<")
        }
        var b = "周一";
        switch (b) {
            case "周一":
                console.log(1);
                break;
            default:
                console.log(5);
        }
    </script>
</head>
<body>

</body>
</html>
```

##### 循环结构

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js02-循环结构</title>
    <script>
        /*1.for循环*/
        for (var i=0;i<10;i++){
            console.log(i);
        }
        /*2.while循环*/
        var a=1;
        while(a<10){
            a++;
        }
        // console.log(a);
        /*3.do...while循环*/
        do {
            a++;
        }while(a<20);
        /*4.for/in循环：与java的forecah完全不同*/
        /*因为js是弱类型的，所以数组定义时也是弱类型的，其中的元素也是弱类型的*/
        var arr=[1,3,2.34,null];//js的数组使用中括号
        for(var i in arr){
            //特别注意，输出的i是 数组中的index下标
            console.log(i)
        }
    </script>
</head>
<body>

</body>
</html>
```

#### js的复杂数据类型（对象，数组，函数）    

##### 数组

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js04-数组</title>
    <script>
        /*js中的复杂数据类型：数组*/
        /*1.数组的定义：4种方式*/
        var a1=new Array();//定义一个空的数组
        var a2=new Array(3);//定义长度为3的数组
        var a3=new Array("12",5,true,31.4);//数组的初始化赋值
        var a4=[1,2,3,true];//使用直接量定义数组
        /*2.数组的存取操作*/
        a2[0]=1;
        a2[1]=5;
        a2[30]='a';//js的数组是自动扩容的，扩容时不会出现数组越界
        console.log(a2);
        console.log(a2[30]);//数组取数时，根据下标值取值
        console.log(a2[31]);//当下标超出长度时，不会报错，但是返回值 是undefined未定义
        /*3.数组的push方法：添加元素*/
        a4.push('abc');
        console.log(a4);
        /*4.数组的删除元素的方法：pop()  shift()*/
        console.log(a4.pop());//删除并返回数组的最后一个元素  abc
        console.log(a4);//(4) [1, 2, 3, true]
        console.log(a4.shift());//删除并返回数组的第一个元素  1
        console.log(a4);//(3) [2, 3, true]
        /*5.数组的排序：sort()方法*/
        var a5=[1,2,4,3,55,22];
        //默认按照字符的字典顺序升序排列
        console.log(a5.sort());// [1, 2, 22, 3, 4, 55]
        var a6=a5.sort(function (a,b) {
            return a-b;//a-b的结果，如果是负数，a与b不交换，
                       // 如果是正数a和b交换，内部是一个冒泡算法
        });
        console.log(a6)//[1, 2, 3, 4, 22, 55]
    </script>
</head>
<body>

</body>
</html>
```

##### 函数

- js中的函数是一堆可执行代码的合集。在需要的时候可以通过函数的名字调用其中的代码。函数可以理解为一种特殊的对象，其实本质上就是一段可执行的字符串。

- js中函数调用时，实参的数量可以和形参的数量不一致。如果实参少于形参，则形参依次赋值，没有被赋值到的形参取值为undefined。如果实参多余形参，则依次赋值，对于没有被赋值到的实参也不会丢失可以在方法中通过arguments来获取。
- js中的函数可以认为是一种特殊的对象，可以任意的赋值给不同的引用甚至通过方法来当做参数传递，唯一特殊的是，通过跟一对小括号可以执行函数中的代码。其实js是一门解释运行的语言，函数的本质就是一段js代码的字符串，来回赋值、来回传递都可以，一旦跟一对小括号，就执行这段js代码字符串。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js05-函数</title>
    <script>
        /*js中的复杂数据类型：function*/
        /*1.定义函数的语法格式：三种,第一种和第三种必须掌握！！第二种了解*/
        //1.1普通函数定义方式
        function f1() {
            console.log("无参的函数");
        }

        function f2(a, b, c) {
            console.log(c)
            return a - b ;
        }

        //1.2动态的定义一个函数
        /*小括号中可以有N个参数，但是前N-1个是形参，最后一个是方法体*/
        var f3 = new Function('v', 's', 'return v-s');
        console.log(f3);//结果如下
        /*ƒ anonymous(v,s) {
            return v-s
        }*/
        //1.3匿名函数的定义方式
        var f4=function (a,b) {
            return a-b;
        }
        /*2.函数的调用*/
        f1();
        console.log(f2(1,2,3));
        console.log(f3(4,5));
        console.log(f4(5,6));
        /*3.函数的调用：扩展*/
        f1(1,2,3,4);//参数列表可以随意定义长度，参数的个数不确定
        console.log(f2(1,2));//参数列表如果参数较少，其他的参数是undefined未定义的

    </script>
</head>
<body>

</body>
</html>
```

##### 内置对象

String -- 基本数据类型 字符串类型 的包装对象

Boolean -- 基本数据类型 布尔类型 的包装对象

Nubmer -- 基本数据类型 数值类型 的包装对象

Array -- 数组类型 的包装对象

Function -- 函数类型 的包装对象

Math -- 数据对象，封装了很多数学常量和数学方法

Date -- 日期时间对象，封装了很多和日期实现相关的方法

Global -- 全局对象。js中有一些方法和属性经常使用，但归到哪个对象上都不合适，所以js中有一个Global对象整合了这些方法和属性。Global中定义的方法和属性特点是属于全局，可以直接使用。

思考：parseInt("123abc123");打印结果。

RegExp --正则对象，保存有关正则表达式模式匹配信息的固有全局对象。Partten

邮箱正则。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js06-js内置对象</title>
    <script>
        /*内置对象：js已经为我们提供好的对象*/
        /*已经学过的String，Number，Boolean,Array.Function*/
        /*需要了解的常用的四种：Math,Global(全局对象)，Date(日期对象) ,RegExp(正则对象)*/
        /*Math对象：常用属性和方法,其他方法参考js的API文档*/
        console.log(Math.PI);
        console.log(Math.E);
        console.log(Math.round(3.14))//四舍五入
        console.log(Math.pow(2,3))//2的3次方
        console.log(Math.random());//随机数[0,1)
        /*Global的全局对象：主要是提供全局方法直接使用即可*/
        console.log(isNaN(123));
        console.log(parseFloat('3.14'))//把字符串转为浮点数
        console.log(parseInt('123'));//把字符串转为整数（默认转为十进制）
        /*Date日期对象*/
        var d1=new Date();//日期的创建
        //注意：字符串的类型不能随意定义，一般是2010-01-01或者2010/01/01
        var d2=new Date('2010-01-01');//把字符串转为日期
        console.log(d1.getDate());//返回月份的第几天
        console.log(d1.getDay());//星期的第几天，，星期天开始（0开始）
        console.log(d1.getFullYear());//返回本地时间的年份
        /*4.RegExp正则表达式:作用匹配特定格式的字符串*/
        //比如：邮箱地址：test@qq.com,
        var r1=/^\w+@\w+(\.\w+)+$/;
        console.log(r1.test("txsy@qq.com"))

    </script>
</head>
<body>

</body>
</html>
```

##### js的自定义对象

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js07-自定义对象</title>
    <script>
        /*创建自定义对象的基本步骤：
        * 1.创建一个构造函数
        * 2.添加属性和方法
        * 3.使用new关键字创建对象*/
        /*1.构造函数就是一个普通定义的function*/
        //1.1无参构造，没有属性和方法
        function Person() {

        }
        //创建对象
        var p = new Person();
        console.log(p);
        //添加属性
        p.name = 'tom';
        p.age = 16;
        //添加方法
        p.say = function () {
            alert('hahaha');
        }
        //调用属性和方法
        console.log(p.age);
        console.log(p['name']);//中括号中是["key"]
        p.say();
/*        var p1 = new Person();
        console.log(p1.name)*/
        //1.2有参构造函数
        function Person(name,age){
            this.name=name;
            this.age=age;
            this.say=function () {
                alert('hello');
            }
        }
        var p=new Person('翠花',12);
        console.log(p.name);
        console.log(p['age']);
        //1.3直接量定义一个对象
        console.log(p);
        //语法格式：{key1:value1,key2:value2,......}
        var p={
            name:"小张",
            age:23,
            sleep:function () {
                alert('blue')
            }
        };
        console.log(p);

    </script>
</head>
<body>

</body>
</html>
```

##### JSON对象

特点：

- 可以描述复杂的数据类型关系
- json对象是目前web浏览器与web服务器之间传递参数的主要形式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js08-json对象</title>
    <script>
        /*1.json本质上就是一段字符串，解析时特别方便 {}的样子*/
        /*2.json格式的数据：是目前web数据传输格式的最流行的方式*/
        var data = {
            name: 'AXY',
            age: 18,
            wife: [//数组中每一个元素都是一个对象
                {name: 'A1', age: 15, hobby: ['1', '2']},
                {name: 'A2', age: 16, hobby: ['11', '22']},
                {name: 'A3', age: 17, hobby: ['111', '2222']}
            ]
        };
        //调用json对象输出其中的某个字段
        console.log(data["wife"][1]["hobby"][0]);
    </script>
</head>
<body>

</body>
</html>
```

