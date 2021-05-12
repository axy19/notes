[TOC]
# API---网络编程（套接字）

## 一、概念

1. 通过IO流在网络上实现数据传输。 

2. 网络模型：

   物理层-数据链路层-网络层-传输层（udp,tcp）-会话层-表示层-应用层

3. IP地址----确定主机在网络中的位置。IPV4 ---四位0-255组成   10.9.20.254

4. 端口---用于和计算机外界进行数据交互的媒介

5. 端口号---给端口进行编号（0-65535），端口号在0-1024之间大部分都被系统提前占用了，最好以后指定端口号时尽量选择1024之后的。

6. 域名---域名可以和ip地址一一映射   

![](https://note.youdao.com/yws/api/personal/file/4F985CD77C3340A9A229F3AF66EF38C9?method=download&shareKey=b52c6ac11e1d696573420df2bbec72a0)

##  二、InetSocketAddress

 代表ip地址和端口号的类

```java
import java.net.InetSocketAddress;
public class InetSocketAdressDemo {
    public static void main(String[] args) {
        //创建代表ip和端口号的类的对象
        //127.0.0.1永远指向本地ip  对应的域名---localhost
        InetSocketAddress isa=new InetSocketAddress
                ("localhost",8089);
        //获取ip地址
        System.out.println(isa.getAddress());
        //获取主机名---计算机的名称
        System.out.println(isa.getHostName());
        //获取端口号
        System.out.println(isa.getPort());
    }
}
```

## 三、UDP协议

1. 基于io流
2. 传输数据时不会建立连接，不可靠（数据有可能丢失）传输速度快。
3. 底层是根据数据包进行传输数据，数据包最大不能超过64kb
4. 使用场景---直播，视频，语音等等
5. DatagramSocket    DatagramPacket

发送端

- 创建UDP套接字对象
- 创建数据包，指定发送的数据，长度，ip地址和端口号
- 发送数据包
- 关流

```java
public class UDPSender {
    public static void main(String[] args) throws IOException {
        //创建UDP套接字对象
        DatagramSocket ds=new DatagramSocket();
        //创建数据包---底层通过字节流传输
        //byte[]---要发送的数据
        //length---可以传输的数据的字节个数最大为byte数组的长度
        //指定ip地址和端口号
        DatagramPacket dp=new DatagramPacket("hello".getBytes(),
                "hello".getBytes().length,
                new InetSocketAddress("localhost",8087));
        //发送数据包
        ds.send(dp);
        //关流
        ds.close();
    }
}
```

接收端：

- 创建UDP套接字的对象，绑定端口号
- 创建数据包，指定接收的字节数组以及可以接受的数据长度
- 接受数据包
- 关流
- 解析数据包

```java
public class UDPReceiver {
    public static void main(String[] args) throws IOException {
        //创建UDP套接字对象---监听端口号
        DatagramSocket ds=new DatagramSocket(8087);
        //创建接受数据的数据包
        //byte[]--待接受数据的字节数组
        //length--指定可以接受的数据的长度
        DatagramPacket dp=new DatagramPacket(new byte[1024],1024);
        //接受数据--阻塞
        ds.receive(dp);
        //关流
        ds.close();
        //解析数据包
        //获取ip地址
        System.out.println(dp.getAddress());
        //getData（）---获取数据内容  getLength（）---获取数据长度
        System.out.println(new String(dp.getData(),0,dp.getLength()));
        //
        System.out.println(dp.getPort());
    }
}
```

## 四、TCP协议

- 基于IO流
- 会先创建连接（三次握手），可靠（数据的完整），传输速度慢
- 不根据数据包传输数据，不限制数据的大小
- 使用场景：传输文件，聊天等
- Socket(客户端)   ServerSocket(服务器端)

客户端

1. 创建TCP客户端对象
2. 发起连接，指定ip地址和端口号
3. 获取自带的字节输出流写出数据
4. 通知服务器端数据已经发送完毕
5. 关流

```java
public class TCPClientDemo {
    public static void main(String[] args) throws IOException {
        //创建客户端对象
        Socket s = new Socket();
        //建立连接--阻塞
        s.connect(new InetSocketAddress("localhost", 9094));
        //往服务器端发送数据
        //获取自带的字节输出流，默认往服务器端写数据
        OutputStream os = s.getOutputStream();
        //通过自带的输出流写出数据
        os.write("hello".getBytes());//阻塞
        //通知服务器端数据已经写完
        s.shutdownOutput();
        //接受服务器端发送的数据
        InputStream in=s.getInputStream();
        byte[] b=new byte[1024];
        int len=-1;
        while((len=in.read(b))!=-1){
            System.out.println(new String(b,0,len));
        }
        s.shutdownInput();
        //关流
        s.close();
    }
}
```

服务器端

1. 创建TCP服务器端对象
2. 接受连接
3. 获取自带的字节输入流读取数据
4. 通知客户端数据已经接收完毕
5. 关流

```java
public class TCPSeverDemo {
    public static void main(String[] args) throws IOException {
        //创建服务器端对象---监听端口
        ServerSocket ss=new ServerSocket(9094);
        //接受连接---阻塞
        Socket s=ss.accept();
        //接受客户端发送的数据
        //获取自带的字节输入流
        InputStream in=s.getInputStream();
        //自建缓冲区
        byte[] b=new byte[3];
        int len=-1;
        while ((len=in.read(b))!=-1){//阻塞
            System.out.println(new String(b,0,len));
        }
        //通知客户端数据接受完毕
        s.shutdownInput();
        //获取自带的字节输出流给客户端发送数据
        OutputStream os=s.getOutputStream();
        os.write("你也好".getBytes());
        //通知客户端发送完毕
        s.shutdownOutput();
        //关流
        ss. close();
    }
}
```

==receive，connect，accept，write，read都会导致线程阻塞==

## 五、练习

- 通过UDP实现简易聊天室（线程）

```java
public class UDPChatDemo {
    public static void main(String[] args) {
        //开启线程
        new Thread(new Recive()).start();
        new Thread(new Send()).start();
    }
}

//代表线程发送的类
class Send implements Runnable {
    @Override
    public void run() {
        try {
            //UDP套接字对象
            DatagramSocket ds = new DatagramSocket();
            //创建Scanner对象
            Scanner sc = new Scanner(System.in);
            //保持可以一起发送
            while (true) {
                byte[] data = sc.next().getBytes();
                //创建数据包
                DatagramPacket dp = new DatagramPacket(data, data.length,
                        new InetSocketAddress("255.255.255.255",
                                9791));
                //发送数据包
                ds.send(dp);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

//代表线程接受的类
class Recive implements Runnable {
    @Override
    public void run() {

        try {
            //创建UDP的套接字对象
            DatagramSocket ds = new DatagramSocket(9091);
            //保持一直接受
            while (true) {
                DatagramPacket dp = new DatagramPacket(new byte[1024], 1024);
                ds.receive(dp);
                //解析数据包
                System.out.println(dp.getAddress());
                System.out.println(new String(dp.getData(),0,dp.getLength()));

            }
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

- 通过TCP实现文件的上传或下载(服务器端可以直接拿到客户端文件的名称 ）











