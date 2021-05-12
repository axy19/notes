[TOC]
# Maven

## 一、Maven的相关概念

#### 1.maven的概念

Maven是目前市场上最流行的包管理工具、项目构建工具。

通过maven可以管理整个项目从创建、开发到编译、测试、打包、发布的整个流程，进行标准化开发。

特别是通过依赖机制可以优雅的解决项目开发中包的依赖问题，大大简化了项目开发、管理流程。

Maven基于项目对象模型（POM）概念，利用中央信息片段管理一个项目的构建，生成，报告等等步骤；

是目前最主流的项目构建工具。

maven并不是市场上唯一的项目构建工具，但是是目前最流行的项目构建工具。

常见的项目管理工具

| ANT    | 最早的项目构建工具之一，目前很少再使用                       |
| ------ | ------------------------------------------------------------ |
| Maven  | 目前最主流的项目构建工具，使用非常广泛                       |
| Gradle | 目前比较新颖的项目构建工具，相对Maven在进行大项目管理时性能更好 |

#### 2.Maven的基本概念

##### 库（repository）

maven使用库的概念来管理项目资源。

maven库又分为本地库和远程库，远程库可以细分为中央库、镜像库(代理库)和私服。

1. 中央库

   指的是maven官方管理维护的库，是全世界最大的maven仓库，管理着大量的资源。

2. 镜像库(代理库)

   为了分摊中央库的访问压力、为了使全世界不同地区的用户都可以有较好的下载体验，除了中央库，全世界范围内还有多镜像库存在，镜像库可以认为是对中央库全部或部分资源的拷贝，全世界开发者可以选择去连接速度最优的镜像库获取资源。

   国内比较知名的maven镜像库有网易镜像库和阿里镜像库。

3. 私服

   公司或个人也可以利用maven的机制搭建在一定范围内使用的类似中央库的库，在一定范围内管理项目资源，这样的库只在一定范围内起作用，且不一定和中央库互通，这样的库称之为私服库。

4. 本地库

   在当前机器内部保存资源的库。

maven在工作时优先从本地库寻找资源，如果找不到就去从配置的镜像库或私服或中央库中自动下载资源，下载的资源保存在本地库中，以便于重复使用。

![](https://note.youdao.com/yws/api/personal/file/44FDC6BB574E46F593BD2D1CB7F4B5CF?method=download&shareKey=14add4d943093cb67cfeab29f2490014)

#### 3.库中资源的定位

在maven库中管理着大量的资源，如何唯一的标识这些资源是一个基本的问题。maven中是通过资源的坐标地址来解决这个问题的。

```
<groupId>org.springframework</groupId>
<artifactId>spring-beans</artifactId>
<version>4.3.7.RLELEASE</version>
```

其中：

| <groupId>    | 指定项目名称                    |
| ------------ | ------------------------------- |
| <artifactId> | 指项目下某一模块名称(jar包名称) |
| <version>    | 指版本信息                      |

## 二、Maven的安装

#### 1.下载

<http://maven.apache.org/download.cgi>

#### 2.安装

1. 安装好jdk

   maven3.6以上的版本至少需要jdk8，配置好JAVA_HOME环境变量。

2. 安装maven

   解压maven到任意目录，==注意目录路径中不要出现中文或空格==。

3. 配置环境变量

   配置MAVEN_HOME环境变量指向maven的安装目录

   配置PATH环境变量指向maven安装目录中的bin目录

   ![](https://note.youdao.com/yws/api/personal/file/D4AF6C461E1143F1B1FA14C06C3922BA?method=download&shareKey=0a3f933eae860441ee8a893f6951f737)

   ![](https://note.youdao.com/yws/api/personal/file/42C90C831A644FD9A2CFBD0FF5127AEC?method=download&shareKey=7e83ff0af60a21b43802870d74f59696)

4. 配置maven

   maven的核心配置文件是conf/settings.xml

   在正式使用maven之前需要配置这个文件

   主要需要指定==本地库(localRepository)和镜像库(mirror)的地址==

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
     <!--本地库配置，默认在 ${user.home}/.m2/repository-->
    <!-- localRepository
      | The path to the local repository maven will use to store artifacts.
      |
      | Default: ${user.home}/.m2/repository
     <localRepository>/path/to/local/repo</localRepository>
   -->
     <localRepository>D:/mvn_repo</localRepository>
     <pluginGroups>
     </pluginGroups>
       <proxies>
       </proxies>
      <servers>
      </servers>  
       <!--配置镜像库-->
      <mirrors>
   	<!--配置阿里镜像库-->   
        <mirror>
   	<id>nexus-aliyun</id>
   	<mirrorOf>central</mirrorOf>
   	<name>Nexus aliyun</name>
   	<url>http://maven.aliyun.com/nexus/content/groups/public</url>
         </mirror>
     </mirrors>
   	...其他配置...
   </settings>
   ```
   
5. 解压ali_repo到本地仓库

   由于大家的机器没有开放外网，我们将阿里库中常用的资源打了个压缩包发给大家，大家直接解压放在本地库中，这样既是无法连接远程库也可以进行开发了。

## 三、Maven的使用

可以利用maven管理项目的整个生命周期，这是通过maven的不同命令来实现的。

#### 1. mvn  archetype:generate--创建项目

1. 1. 进入要创建项目的目录，执行命令mvn       archetype:generate

   2. 提示要求选择创建项目的程序骨架

      默认提供了十种，我们目前知道两种即可

      所谓项目的骨架指的是项目的不同的结构，不同项目往往是具有不同的结构的，例如基本的java项目和javaweb项目的结构就是不同的，在使用mvn创建这些项目时通过指定不同的骨架来创建出不同结构的项目。

      * 提示选择创建项目要使用的骨架

        ```
        Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 7: 
        ```

      * 普通java项目

        ```
        7: internal -> org.apache.maven.archetypes:maven-archetype-quickstart (An archetype which contains a sample Maven project.)
        ```

      * 普通web项目

        ```
        10: internal -> org.apache.maven.archetypes:maven-archetype-webapp (An archetypewhich contains a sample Maven Webapp project.)
        ```

   3. 要求输入groupId

      通常情况下，groupid要以公司域名的倒写形式来声明

      ```
      Define value for property 'groupId':cn.tedu
      ```

   4. 要求输入artifactId:

      ```
      Define value for property 'artifactId':MVNDemo01
      ```

   5. 要求输入version：--可以直接敲回车默认即可

      ```
      Define value for property 'version' 1.0-SNAPSHOT:
      ```

   6. 要求输入package:

      ```
         Define   value for property 'package' MVNDemo01: :   
      ```

   7. 检查信息并确认：

      ```
       Y: ://可以直接敲回车默认就是Y
      ```

   8. 创建出的项目结构

      ![](https://note.youdao.com/yws/api/personal/file/8B37E9D0C94A4BF4AAF46F1BB575C304?method=download&shareKey=d8dcf6f622585c1adae44ba2e73669a7)
      
      ```
      MVNDemo01
      	|-src
      		|-main
      			|-java --> 主程序代码
      			|-resources --> 主程序的资源文件(主要是配置文件)
      		|-test
      			|-java -->测试代码
      			|-resources -->测试程序的资源文件(主要是配置文件)
      	|-target-->目标文件夹，src/main和src/test下的资源处理过后产生的文件存放在此处
      	|-pom.xml -->当前项目的maven核心配置文件
      ```

#### 2.mvn compile--编译main目录下的源码

会自动导入pom文件中指定的依赖。

会将src/main目录下的源码和资源编译后存放到target/classes下。

==注意，test文件夹下的所有内容在编译，打包，安装过程都不参加，但是会参加测试过程。==

![](https://note.youdao.com/yws/api/personal/file/28721BE52542412FA13F9DE564A64CF8?method=download&shareKey=9c5eb6a990fb9764f00f560b39b6cd3c)

#### 3.mvn test--编译test文件下的源码

通过此命令可以执行test文件夹下的测试用的内容，实现项目测试

首先会将src/test目录下的源码和资源编译后存放到target/test-classes下，之后执行其中的测试代码，输出测试结果到控制台，同时测试结果保存一份到target/surefire-reports中

![](https://note.youdao.com/yws/api/personal/file/3C6FD115C44941A69E6175A5E2D84FB7?method=download&shareKey=463d52894aee273b2639c53a051fec02)

![](https://note.youdao.com/yws/api/personal/file/BAAE2396FCB94CB3B2D803C5F68F7964?method=download&shareKey=229568c6ddb412af292442705fa4d8fe)

#### 4.mvn clean--清除编译的target文件夹

清理mvn命令，此命令可以清除target文件夹，在其他mvn命令执行之前，通常都建议大家先执行一次mvn clean，这样可以将之前其他操作产生的结果清除，防止对本次执行的命令产生影响。

#### 5.mvn package--打jar包或者war包

打包命令，会将编译完成的资源打包成指定格式(jar包/war包)，具体怎样打包取决于pom.xml文件中的配置

如下配置指定了项目打成什么样的包：

```
<packaging>jar</packaging>
```

mvn package命令同时隐含了编译 测试 打包过程，建议在执行此命令之前最好先执行一次mvn clean 防止之前其他命令产生target内容影响此次命令执行的结果。

#### 6.mvn install--安装项目到本地库

maven项目安装，这会将打包好的包及其相关的资源文件存放到本地库中由maven进行管理，成为了maven所管理的一个资源。

maven install命令会隐含进行编译 测试 打包 安装，建议在执行此命令之前最好先执行一次mvn clean 防止之前其他命令产生 的target影响此次命令执行的结果。

#### 7.mvn deploy--将项目发布到远程库

maven项目发布，将maven本地库中管理的资源发布到远程库中。

但是无论是中央库还是镜像库都不允许随意上传部署，所以无法在中央库和镜像库中实现这个过程。

但是如果是自己搭建的私服，是可以通过这个过程完成资源发布的。

#### 8.真实的开发过程

```
mvn archetype:generate -->创建项目
配置pom文件，声明需要的依赖
mvn compile --->项目编译，此时会自动导入pom文件中声明的依赖jar

while 开发未完成:
	进行开发...
	mvn clean -->项目清理
	mvn compile -->项目编译，将写好的代码编译到target
	mvn test -->项目测试
	...

mvn package -->项目打包
mvn install -->项目安装
mvn deploy -->项目发布
```

## 四、pom文件的编写

maven管理的项目中，通过项目根目录下的pom.xml文件进行核心配置。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
	http://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<groupId>cn.tedu</groupId>
	<artifactId>MVNDemo03</artifactId>
	<version>1.0-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>MVNDemo03</name>
	<url>http://maven.apache.org</url>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>4.3.7.RELEASE</version>
			<exclusions>
				<exclusion>
					<groupId>org.springframework</groupId>
					<artifactId>spring-core</artifactId>
					<version>4.3.7.RELEASE</version>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>
	
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
				<configuration>
					<archive>
						<manifest>
							<addClasspath>true</addClasspath>
							<mainClass>cn.tedu.App</mainClass> <!-- 此处为主入口-->
						</manifest>
					</archive>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-source-plugin</artifactId>
				<configuration>
					<attach>true</attach>
				</configuration>
				<executions>
					<execution>
						<phase>compile</phase>
						<goals>
							<goal>jar</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>
```

#### 1.项目的信息

```xml
<project 
	xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<!--当前项目的maven资源坐标-->
	<groupId>cn.tedu</groupId>
	<artifactId>MVNDemo01</artifactId>
	<version>1.0-SNAPSHOT</version>

	<!--此项目在mvn package的过程中，要打成什么样的包，通常是jar或war-->
	<packaging>jar</packaging>

	<!--项目名称-->
	<name>MVNDemo01</name>
	<!--项目主页-->
	<url>http://maven.apache.org</url>

	<!--参数配置-->
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
</project>
```

#### 2.依赖信息

声明当前项目所依赖的资源，maven根据此配置自动导入相关资源。

可以查询需要导入的资源的官网，通常会提供dependency的写法，或者也可以去maven中央库的网页版(<https://mvnrepository.com/>)中搜索，同样可以找到相应资源的依赖配置方法。

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>3.8.1</version>
	<scope>test</scope>
</dependency>
```

可选配置：

##### 2.1 scope属性

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>3.8.1</version>
	<scope>test</scope>
</dependency>
```

在项目中导入的依赖并不一定在项目全生命周期中都要用，此时可以通过scope属性指定依赖应用的范围。

| compile  | 编译范围，scope的默认值就是compile。   在编译,测试,打包,安装,发布全部生命周期都存在的依赖资源 |
| -------- | ------------------------------------------------------------ |
| test     | 测试范围   运行测试代码时,才加载的依赖资源,打包,安装,发布都不参加. |
| runtime  | 运行时范围和compile的唯一区别,就是不参加编译;  例如mysql可以不写代码编译,但是必须在运行,打包安装其他阶段参加. |
| provided | 在编译阶段使用,但是运行,打包安装都不参加。官方给了一个案例:servlet-api,编辑servlet,web应用等代码使用的内容,必须使用provided;(web应用,在tomcat中运行),如果将servlet-api依赖资源打包到war包,扔到tomcat执行会出现冲突;编写任何web应用时,使用到servlet-api的资源,必须添加provided的范围; |
| system   | 系统范围   在当前项目的环境中存在需要使用的jar包资源,maven没有提供groupId artifactId version,可以使用system指定从本地路径进行加载   <dependency>                                                      <groupId>cn.tedu</groupId>   <artifactId>wotongzhuo</artifactId>   <version>1.0</version>   <scope>system</scope>   <systemPath>D:\\piaoqian\\xxw\\xxw.jar</systemPath>   </dependency> |

##### 2.2 exclusions移除依赖传递

在项目中导入A依赖，单A本身又依赖B和C,B又依赖的D，则Maven会智能的自动导入B、C和D，这样依赖就被传递了，这个过程就称之为依赖的传递。

依赖传递是一个非常优良的特性，可以省去maven使用过程中的大量配置细节，但这种智能的自动导入过程偶尔也会造成包引入的冲突，造成程序运行出错。此时可以通过配置exclusions来打断依赖的传递来解决问题。

简单来说配置的exclustions就是在告诉maven在导入某个包的过程中，指定的依赖传递不要自动导入。

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.7.RELEASE</version>
	<exclusions>
		<exclusion>
			<!-- 坐标指定移除内容 -->
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>4.3.7.RELEASE</version>
		</exclusion>
	</exclusions>
</dependency>
```

#### 3.构建信息（插件信息）

可以额外的增强maven的功能，实现一些特殊效果

##### 3.1 main插件

用来指定打包出来的jar中的入口方法

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-jar-plugin</artifactId>
	<configuration>
		<archive>
			<manifest>
				<addClasspath>true</addClasspath>
				<mainClass>cn.tedu.App</mainClass> <!-- 此处为主入口-->
			</manifest>
		</archive>
	</configuration>
</plugin>
```

##### 3.2 打包同时把源码打成jar包的插件

用来额外打包源代码

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-source-plugin</artifactId>
	<configuration>
		<attach>true</attach>
	</configuration>
	<executions>
		<execution>
			<phase>compile</phase>
			<goals>
				<goal>jar</goal>
			</goals>
		</execution>
	</executions>
</plugin>
```

##### 3.3 maven项目的导入

在右边有一个maven选项，选择maven，有一个+点击+，选择pom文件。等待一会即导入成功。