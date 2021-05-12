[TOC]
# javaee开发模式

#### 1.纯Servlet开发

只用Servlet开发，html内容混在Java代码中不利于开发维护。

#### 2.纯JSP开发

比起纯Servlet开发，JSP改为了在HTML编写java代码，便于页面开发。大量的java代码嵌在HTML中，同样非常混乱，不利于开发维护。

#### 3.JSP+JSP标签技术开发

将JSP中的java代码通过标签技术替代， 减少了jsp页面中的java代码，jsp更加简洁。大量的自定义标签开发非常麻烦，大量的自定义标签混在html标签中，不利于开发维护。

#### 4.JSP + JavaBean(模式一)

JSP负责接收请求 封装数据到Bean 调用Bean中的业务逻辑代码获取结果展示数据

JavaBean负责封装数据 处理数据业务逻辑

初步实现了业务逻辑和数据展示的分离，结构变的跟加清晰，便于开发维护。

JSP仍然在负责处理页面展示以外的其他功能，仍然不可避免的要编写java代码

#### 5.Servlet＋JavaBean +JSP(模式二)

Servlet负责接收请求，封装数据到Bean，调用Bean中的业务逻辑代码 获取结果数据 转发到JSP中展示

JavaBean负责封装数据 处理数据业务逻辑

JPS负责展示数据

每个部分只负责自己最擅长的工作，结构清晰，便于开发维护。

#### 6.MVC设计思想

MVC设计模式认为，任何软件都可以分为负责程序执行过程的控制器(Controller) 负责业务数据封装处理的模型(Model) 和负责用户交互的视图(View)三部分组成。MVC设计思想要求软件在设计的过程中，应该将这三部分互相独立，使软件结构更加清晰，便于开发维护。

MVC设计思想不是JavaEE特有的思想，普遍应用在软件开发领域，指导软件的开发设计。

#### 7.JavaEE的经典三层架构

在JavaEE的经典三层架构中，将JavaBean的功能进一步细分，JavaBean只负责数据封装，Service负责业务逻辑处理，Dao负责数据库的访问。

JavaEE的经典三层架仍然符合MVC设计模式，可以说是模式二的进一步发展。比起模式二，模块划分跟加清晰，各司其职，便于开发维护。

目前JavaEE开发中主要采用的就是这种开发模式。

![](https://note.youdao.com/yws/api/personal/file/5C1A8A67174B48528A6ED69A2F08E987?method=download&shareKey=e78cf3773b7368dd52baf15a5baf6c7a)

![](https://note.youdao.com/yws/api/personal/file/FC9B62690AB0447984413DBFC10CC6B1?method=download&shareKey=76b0c6dd0d47959667f007d44385432a)

![](https://note.youdao.com/yws/api/personal/file/BBD736A9D6CF4F138801D36493EFB709?method=download&shareKey=9ae95612c5b6eaa6ed002190357bbdce)