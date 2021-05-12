[TOC]
# NGINX

## 一、问题 ##

#### 1.高并发场景下的问题 ####

在开发好的项目真正上线后，一旦访问的并发量上升，由于大量的并发涌入，往往会造成服务器运行时的各种问题，包括服务器并发压力、数据库访问压力、数据库数据量限制等等。我们将在互联网架构阶段讨论问题的产生和解决的方案。

#### 2.服务器并发压力 ####

单台tomcat在理想情况下可支持的最大并发数量在200~500之间，如果大于这个数量可能会造成响应缓慢甚至宕机。

解决方案是通过多台服务器分摊并发压力，这不仅需要有多台tomcat服务器，还需要有一台服务器专门用来分配压力，我们称之为反向代理服务器程序。这样的操作称之为基于反向代理服务器实现负载均衡操作。

## 二、Nginx概述 ##

1. Nginx是一款优秀的反向代理服务器程序，能够为若干台服务器提供反向代理服务，一方面为客户端提供的统一的访问地址，另一方面为后台多个服务器提供了负载均衡的能力。
2. Nginx是目前最主流的反向代理服务器，能够提供可靠的负载均衡、动静分离的能力。
3. 反向代理（Reverse Proxy）方式是指以代理服务器来接受Internet上的连接请求，然后将请求转发给内部网络上的服务器；并将从服务器上得到的结果返回给Internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。通常的代理服务器，只用于代理内部网络对Internet的连接请求，客户机必须指定代理服务器,并将本来要直接发送到Web服务器上的http请求发送到代理服务器中。当一个代理服务器能够代理外部网络上的主机，访问内部网络时，这种代理服务的方式称为反向代理服务。

## 三、Nginx的安装配置 ##

#### 1.下载 ####

<http://nginx.org/en/download.html>

#### 2.安装 ####

将下载好的安装包解压到一个没有中文没有空格的目录下即可。

#### 3.命令 ####

```
验证配置是否正确: nginx -t
查看Nginx的版本号：nginx -V
启动Nginx：start nginx
快速停止或关闭Nginx：nginx -s stop
正常停止或关闭Nginx：nginx -s quit
配置文件修改重装载命令：nginx -s reload
```

nginx是否在运行可以在windows的任务管理器中查看，正在运行ngxin会由两个进程显示。（win10在任务管理器->详细信息,可以看到类似的进程）

![](https://note.youdao.com/yws/api/personal/file/484DBAD69A7644BBBCA513CFBFEA959D?method=download&shareKey=98723bd519ddb43fb5fc0e44a88af5f3)

#### 4.配置 ####

nginx的工作是基于[conf/nginx.conf]配置文件来进行的。

nginx.conf的配置结构：

```
http{ #代表处理http请求
	 #配置一个虚拟服务器
	server{
		#此虚拟服务器接收对80端口的访问
		listen 80; 
		#此虚拟服务器接收对localhost主机名的访问
		server_name localhost; 
		#当访问/user资源时由此配置处理
		location /user{
			规则
		}
		#当访问/order资源时由此配置处理
		location /order{
			规则
		}
		...
	}
	
	#其他Server配置
	server ...
	...
}
```

## 四、入门案例-通过Nginx实现请求转发

当客户端访问http://www.pq.com时，由nginx转发给http://127.0.0.1:8080端口进行处理

配置hosts文件

```
127.0.0.1 www.pq.com
```

在nginx.conf中配置

```
http{
	#为nginx配置一个虚拟服务器,
	server {
		#监听本机80端口
		listen 80;
		#接收对www.pq.com主机名的访问
		server_name www.pq.com;
		#对/即任意路径的访问进行处理
		location / {
			#转发到指定地址
			proxy_pass http://127.0.0.1:8080;
		} 
		#可以配置多个location
		...
	}
	#可以配置多个server
	...
}
```

启动 tomcat

```
startup.bat
```

启动nginx

```
start nginx
```

浏览器访问地址，发现确实可以跳转到指定页面

![](https://note.youdao.com/yws/api/personal/file/68DFFE0F537B499F87404E1C21060566?method=download&shareKey=8eab0c85957a36166769dfc3d0abafa9)

## 五、location路径配置和匹配规则 ##

#### 1.location路径的写法 ####

在配置虚拟服务器时，可以配置多个location，指定不同路径采用不同的处理方案，location支持多种写法，规则如下：

| =    | =/aaa/1.jpg | 路径严格匹配，路径必须一模一样才会匹配到     |
| ---- | ----------- | -------------------------------------------- |
| ^~   | ^~/aaa      | 有修饰的，只要是指定路径开头的路径都可以匹配 |
| ~    | ~.png$      | 区分大小写按正则匹配路径                     |
| ~*   | ~*.png$     | 不区分大小写按正则匹配路径                   |
| /    | /           | 通用匹配，所有路径都可以匹配到               |

#### 2.location路径配置的优先级 ####

由于location的路径配置非常灵活，所有有可能一个路径被多个location所匹配，此时按照如下规则判断匹配优先级：

```
• 首先匹配 =
• 其次匹配 ^~
• 其次是按文件中顺序的正则匹配
• 最后是交给 / 通用匹配
• 当有匹配成功时候，停止匹配，按当前匹配规则处理请求
----总的规律是，精度越高优先级越高
```

案例

```
location = / {
   #规则A
}
location = /login {
   #规则B
}
location ^~ /static/ {
   #规则C
}
location ~ \.(gif|jpg|png|js|css)$ {
   #规则D
}
location ~* \.png$ {
   #规则E
}
location / {
   #规则F
}
```

```
访问根目录 /， 比如 http://localhost/ 将匹配规则 A
访问 http://localhost/login 将匹配规则 B
http://localhost/register 则匹配规则 F
访问 http://localhost/static/a.html 将匹配规则 C
访问 http://localhost/a.png, http://localhost/b.png 将匹配规则 D和规则 E，但是规则 D 顺序优先，规则 E不起作用
 http://localhost/static/c.png则优先匹配到规则 C
访问 http://localhost/a.PNG 则匹配规则 E，而不会匹配规则 D，因为规则 E 不区分大小写
访问 http://localhost/category/id/1111 则最终匹配到规则 F
```

## 六、Nginx的负载均衡实现 ##

#### 1.负载均衡原理 ####

![](https://note.youdao.com/yws/api/personal/file/F872659FCFE04AE8ACA5FE3C9021FE82?method=download&shareKey=3c128848141b0ab6469933408a984266)

#### 2.配置三台tomcat，分别监听不同端口，并启动tomcat ####

第22行

| tomcat1 | 8015 |
| ------- | ---- |
| tomcat2 | 8025 |
| tomcat3 | 8035 |

第69行

| tomcat1 | 8081 |
| ------- | ---- |
| tomcat2 | 8082 |
| tomcat3 | 8083 |

第91行

| tomcat1 | 8019 |
| ------- | ---- |
| tomcat2 | 8029 |
| tomcat3 | 8039 |

#### 3.修改Nginx配置，并启动nginx ####

```
#upstream是nginx配置文件中的关键字，用来配置一组服务器地址供后续使用
upstream big1907{
	server 127.0.0.1:8081;
	server 127.0.0.1:8082;
	server 127.0.0.1:8083;
}

server {
	listen 80; #对80端口的访问
	server_name www.pq.com; #对此主机名的访问
	location / {
		#转发到上面配置的服务器组
		proxy_pass http://big1907;
	}
}
```

#### 4.测试

经过测试，发现确实可以通过Nginx负载均衡访问到tomcat中的资源 

![](https://note.youdao.com/yws/api/personal/file/C5CEAD453C9E4AB48BA258900FA8023E?method=download&shareKey=427c041dd3dbf4fd6be5ca76b914ee70)

![](https://note.youdao.com/yws/api/personal/file/70B01185F2084AC884F7B5B620AD663C?method=download&shareKey=654953b0d6cdab8acb1cab14fe404170)

![](https://note.youdao.com/yws/api/personal/file/C2215AB0081A4FCB8F332626FC498A10?method=download&shareKey=479e35e335c4b2413085cedf9b7766f6)

#### 5.负载均衡策略 ####

nginx在分发资源到后端服务器时，如何分配请求是可以配置的，称之为nginx的负载均衡策略。

##### 5.1 轮询 #####

默认不配置就是轮询

连接请求轮流分配给后端服务器

```
http{ 
        upstream sampleapp { 
        server <<dns entry or IP Address(optional with port)>>; 
	server <<another dns entry or IP Address(optional with port)>>; 
	} 
	.... 
	server{ 
	        listen 80; 
	        ... 
	        location / { 
	                proxy_pass http://sampleapp; 
        }  
} 

```

##### 5.2 ip哈希 #####

ip_hash;

abs(客户端ip.hash())%服务器数量，根据余数决定连接请求去往哪个服务器

```
http{ 
        upstream sampleapp { 
        ip_hash; 
	server <<dns entry or IP Address(optional with port)>>; 
	server <<another dns entry or IP Address(optional with port)>>; 
        } 
        .... 
        server{ 
                listen 80; 
                ... 
                location / { 
                proxy_pass http://sampleapp; 
        }  
} 

```

##### 5.3 最少连接 ####

least_conn;

将连接请求分配给目前连接数最少的服务器

```
http{ 
        upstream sampleapp { 
        least_conn; 
        server <<dns entry or IP Address(optional with port)>>; 
        server <<another dns entry or IP Address(optional with port)>>; 
        } 
        .... 
        server{ 
                listen 80; 
                ... 
                location / { 
                proxy_pass http://sampleapp; 
        }  
} 
```

##### 5.4 基于权重 #####

直接在地址后配置weigh=x

根据权重进行分配，权重值越大，被分配的连接越多。可以直接配置为down，则不再分配连接

```
http{ 
        upstream sampleapp { 
                server <<dns entry or IP Address(optional with port)>> weight=2; 
                server <<another dns entry or IP Address(optional with port)>> weight=5; 
                server <<another dns entry or IP Address(optional with port)>> down;
        } 
        .... 
        server{ 
                listen 80; 
                ... 
                location / { 
                proxy_pass http://sampleapp; 
        } 
} 
```

## 七、Nginx的动静分离的实现 ##

#### 1.动静分离的原理 ####

动 --> 动态资源  --> servlet jsp --> 程序 

静 --> 静态资源 --> jpg mp3 mp4 html css js --> 文件 

tomcat能够处理动态和静态资源，但本质上是为处理动态资源而设计的服务器，过多静态资源交由tomcat管理会降低tomcat处理动态资源的能力，得不偿失。

nginx本身无法处理动态资源，但可以处理静态资源，而且性能优良。 

因此可以将静态资源和动态资源拆分，将静态资源交由nginx处理，动态资源仍由tomcat处理，从而解放了tomcat对动态资源的处理能力，整体上实现动静分离，提升了效率。

![](https://note.youdao.com/yws/api/personal/file/95B3CB00AB944F2DA7F13C9A70627C97?method=download&shareKey=022fb07817859ee2239ced9f89532fad)

#### 2.动静分离实现 ####

配置方式

```
server {
	listen 80;
	server_name www.staticfile.com;
	location / {
		#root可以指向nginx服务器中的本地磁盘地址
		#静态文件就放置在这个磁盘地址中
		#之后对server中资源的访问会被转换到对本地磁盘资源的访问
		#www.staticfile.com/aaa/bbb/1.html-->d://html/aaa/bbb/1.html
		root D://html;
		#默认访问的首页配置
		index index.html;
	} 
}
```

#### 3.案例 ####

对http://www.pq.com/html路径及其子路径的访问会被转到对磁盘d://html文件夹的静态资源的访问。

```
server {
	listen 80;
	server_name www.pq.com;
	location ^~/html {
		root d://;
		index index.html;
	}

}
```

## 八、综合案例

用户订单积分案例的Nginx动静分离,负载均衡

1.将用户订单积分案例的静态资源存储到磁盘

![](https://note.youdao.com/yws/api/personal/file/B44289DBC5434DB7AAF020342DDCA049?method=download&shareKey=aa00b9ede632e426d07f0fe903c259bd)

2.将ssmdemo02项目打成war包，发布到tomcat1、tomcat2、tomcat3

通过maven打war包

放置到tomcat1、tomcat2、tomcat3的webapps目录下，自动解压为web应用

3.配置Nginx

```
#练习案例 用户积分案例
upstream big1910{
	server 127.0.0.1:8081;
	server 127.0.0.1:8082;
	server 127.0.0.1:8083;
}
server {
        listen       80;
        server_name  www.pq.com;
	
	#动态资源
	location ~*\.(jsp|action)$ {
		proxy_pass http://big1910;
	}

	#静态资源
	location / {
		root D://static;
	}
}

```

