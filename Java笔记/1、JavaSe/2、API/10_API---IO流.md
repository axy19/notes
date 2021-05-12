[TOC]
# API---IO流

## 一、概念

 一套用于数据传输的机制 Input Output Stream输入输出流

根据传输的方向：输入流---往内存传输数据  输出流---从内存往外传输数据

根据传输方式：字符流---底层以字符形式进行传输（.java ,.txt在底层以字符或者字节存储）

​                            字节流---底层以字节形式进行传输

四大基本流：

|        | 输入流                  | 输出流                   |
| ------ | ----------------------- | ------------------------ |
| 字符流 | 字符输入流（Rreader）   | 字符输出流(Writer)       |
| 字节流 | 字节输入流(InputStream) | 字节输出流(OutputStream) |

代表四大基本流的类都是抽象类

根据传输的场景（数据存储/获取的位置）：硬盘，内存，网络，外设设备

所有的字符流都可以通过字节流完成---转换流

## 二、硬盘内的输入输出流

#### 1.字符流  字符输出流

在硬盘的txt文件里写入数据   字符输出流  文件 FileWriter（文件字符输出流）

```java
import java.io.FileWriter;
import java.io.IOException;
public class FileWriterDemo1 {
    public static void main(String[] args) throws IOException {

        //创建文件字符输出流对象
        //创建对象时会去检测路径是否存在，如果不存在就会创建一个新的空文件
        //如果已经有文件存在就会把创建的空文件进行覆盖，保证开始一定是空文件
        FileWriter fw=new FileWriter("D:\\a.txt");
        //写出数据---提供的写出方法都和字符有关
        //底层根据缓冲区来传输数据，规定只有缓冲区存满才能把数据传输过去
        //此时要传输的数据没有存满缓冲区就会滞留在缓冲区里有可能就会造成数据丢失
        fw.write("hello");
        //提供冲刷缓冲区，无论缓冲区是否存满数据都强制让缓冲区往文件内传数据
        fw.flush();
        //关流(关闭通道)
        //在关流动作中缓冲区中有可能有滞留的数据，为了防止数据滞留在缓冲区中，
        //在关流的过程当中会自动进行一次冲刷缓冲区的操作。
        fw.close();
        //回收对象--让对象变为无用对象
        fw=null;
    }
}
```

##### 1.1 数据的传输过程

![](https://note.youdao.com/yws/api/personal/file/F3C3B3C986B9415899346B4B36533565?method=download&shareKey=1b0bb8fd95ea1a8dccf4c31ad87e77a8)



##### 1.2 IO流的异常捕获

1. 把对象在外进行声明赋值为null，在try块内进行真正地初始化
2. 只有流对象进行了真正地初始化才能进行关流
3. 无论关流成功与否都要把流对象置为null，变成无用对象。
4. 关流的失败有可能发生在自动冲刷之前，有可能产生数据滞留在缓冲区造成数据丢失，需要手动冲刷。

```java
import java.io.FileWriter;
import java.io.IOException;
public class FileWriterDemo2 {
    public static void main(String[] args) {
        //1.声明流对象，赋予初始值为null
        FileWriter fw=null;
        try {
            //在try块里对流对象进行初始化
             fw=new FileWriter("D:\\l.txt");
             //往外写数据
            fw.write("123");
            //4.手动冲刷---防止关流失败没有冲刷
            fw.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {//内容一定会执行
            //2.只有流对象成功初始化才能成功关流
            if(fw!=null){
                try {
                    fw.close();

                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    //3.无论关流是否成功，都要将对象置为无用对象
                    fw=null;
                }
            }

        }

    }
}
```

简化版：

```java
import java.io.FileWriter;
import java.io.IOException;
public class FileWriterDemo3 {
    public static void main(String[] args) {
        //jdk1.7提供的try with resources 
        //会自动进行冲刷缓冲区以及关流
        try(FileWriter fw=new FileWriter("D:\\a.txt")){
            fw.write("566");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 2.字符流  字符输入流

从硬盘txt文件中获取数据  字符输入流 文件  FileReader（文件字符输入流）

```java
import java.io.FileReader;
import java.io.IOException;
public class FileReaderDemo1 {
    public static void main(String[] args) throws IOException {
        //创建文件字符输入流的对象---没有缓冲区
        FileReader fr=new FileReader("D:\\l.txt");
        //读取文件
        //read()---每次返回读取到的字符的编码值（）
       /* System.out.println(fr.read());//1---49
        System.out.println(fr.read());//50
        System.out.println(fr.read());//51
        //当返回值是-1时表明读取结束
        System.out.println(fr.read());//-1*/
       //通过while循环实现
        int i=-1;
       while ((i=fr.read())!=-1){
           System.out.println(i);//49 50 51
       }
    }
}
```

```java
//写读数据的缓冲区
import java.io.FileReader;
import java.io.IOException;
public class FileReaderDemo2 {
    public static void main(String[] args) throws IOException {
        //创建文件字符流对象
        FileReader fr=new FileReader("D:\\a.txt");
        //自建缓冲区---数组
        char[] c=new char[5];
        //read(char[] c)---每次返回的是读取到的字符个数
        //返回值为-1读取结束
        int len=-1;
        //每次到得内容都要存放到数组里
        while((len=fr.read(c))!=-1){
            //输出的是数组的内容--读取文件的内容
            System.out.println(new String(c,0,len));//把数组内容转成字符串
        }
    }
}

```

![1574493358480](https://note.youdao.com/yws/api/personal/file/5FF6CC83F68A4054833BE99172CDE6DC?method=download&shareKey=7bd42d9dfbdbf60f4a55162a928b8f7d)

#### 3.字节流   字节输出流

往硬盘txt文件里写入数据   字节输出流   文件   FileOutputStream（文件字节输出流）

```java
import java.io.FileOutputStream;
import java.io.IOException;
public class FileOutputStreamDemo1 {
    public static void main(String[] args) throws IOException {
        //创建文件字节输出流对象---没有缓冲区
        //如果指定布尔值为true就会从覆盖改为追加，如果不指定默认就是覆盖
        FileOutputStream fos=new FileOutputStream("D:\\1.txt",true);
        //写入数据---转成字节会涉及到编码问题，保证程序和文件的编码一致
        fos.write("abc".getBytes());
        //关流
        fos.close();
    }
}
```

#### 4.字节流  字节输入流

从硬盘txt文件中获取数据   字节输入流  文件   FileInputStream(文件字节输入流)

```java
import java.io.FileInputStream;
import java.io.IOException;
public class InputStreamDemo1 {
    public static void main(String[] args) throws IOException {
        //创建文件字节输入流对象
        FileInputStream fis=new FileInputStream("D:\\l.txt");
        //自键缓冲区---字节数组
        byte[] b=new byte[2];
        int len=-1;
        //read(byte[] b)---每次返回的结果值是字节个数
        //结束标志就是返回-1
        while((len=fis.read(b))!=-1){
            //把每次读取到的自己数组的内容转成字符串进行输出
            System.out.println(new String(b,0,len));
        }
    }
}

```

## 三、缓冲流（给其他的流提供缓冲区）

#### 1.BufferedReader ---给字符输入流提供缓冲区，提供新功能读取一行

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
public class BufferedReaderDemo1 {
    public static void main(String[] args) throws IOException {
        //创建对象---缓冲流对象的创建根据字符输入流类型的对象创建
        //缓冲流对象创建成功之后会给字符输入流对象提供一个缓冲区用于传输数据
        //底层真正读取数据的还是文件字符输入流的对象，缓冲流对象只是提供缓冲区
        BufferedReader b=new BufferedReader(new FileReader("D:\\a.txt"));
        //读取数据--缓冲流对象操作提供的缓冲区去帮助文件字符输入流来读数据
        //缓冲流对象提供新的功能读取一行
     /*   System.out.println(b.readLine());
        System.out.println(b.readLine());
        System.out.println(b.readLine());
        //结束标志为null
        System.out.println(b.readLine());*/
     String str="";
     while((str=b.readLine())!=null){
         System.out.println(str);
     }
    }
}
```

#### 2.BufferedWriter---给字符输出流提供一个更大的缓冲区 提供新功能换行

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
public class BufferedWriterDemo {
    public static void main(String[] args) throws IOException {
        //创建缓冲流对象--由字符输出流对象来构建--真正写出数据的还是字符输出流
        BufferedWriter bw=new BufferedWriter(new FileWriter("D:\\a.txt"));
        //写入数据
        bw.write("abc");
        //换行
        //windows的换行符--\r\n回车换行
        //linux换行符---\n
        bw.newLine();
        bw.write("'adjs");
        bw.close();
    }
}
```

#### 3.装饰者设计模式

模式（遇到同一类问题的解决方式）

设计模式（在软件开发过程中，遇到的同一问题提供统一的解决方案）

装饰者设计模式（根据同类对象给本类对象新增功能，完善功能）

## 四、转换流（只有转换流可以指定编码）

#### 1.OutputStreamWriter---把字符流转成字节流

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
public class OutputStreamWriterDemo1 {
    public static void main(String[] args) throws IOException {
        //创建转换流对象
        //底层真正往外写数据的是字节输出流，但是提供的数据是字符串，只能由字符流来承接
        //字符流转成了字节流
        OutputStreamWriter osw=new OutputStreamWriter(new FileOutputStream("D:\\aa.txt"));
        //写出数据---提供的往外写的数据是字符串，只能被字符流来承接
        osw.write("ann");
        //关流
        osw.close();
    }
}
```

#### 2.InputStreamReader---把字节流转成字符流

```java
import java.io.*;
public class InputStreamDemo {
    public static void main(String[] args) throws IOException {
        //创建转换流对象、
        //在底层真正读取的是字节输入流，但是数据展示时只能被字符数组接收，只能通过字符流读取
        //字节流转成字符流
        InputStreamReader isr=new InputStreamReader(new FileInputStream("D:\\aa.txt"));
        //自建缓冲区--数组
        char[] c=new char[2];
        int len=-1;
        //读取到数据--是字符形式因为是被字符数组接受的--字符流完成
        while((len=isr.read(c))!=-1){
            System.out.println(new String(c,0,len));
        }
    }
}
```

需要冲刷的：Writer  BufferedWriter    BufferedOutputStream

#### 3.练习

##### 3.1 通过文件字符流完成文件复制（需要异常捕获）

```Java
import java.io.*;

//1.通过文件字符流完成文件复制（需要异常捕获）
public class FileTest1 {
    public static void main(String[] args) {
        FileWriter fw = null;
        try {
            FileReader fr = new FileReader("D:\\a.txt");
            BufferedReader br = new BufferedReader(fr);
            fw = new FileWriter("D:\\b.txt");
            BufferedWriter bw=new BufferedWriter(fw);
            String str = "";
            while ((str = br.readLine()) != null) {
                bw.write(str);
                bw.newLine();
                System.out.println(str);
                bw.flush();//无论关流是否成功都要冲刷缓冲区
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fr != null) {
                try {
                    fr.close();
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally{
                    fr=null;
                    br=null;
                }
            }
             if (fw != null) {
                try {
                    fw.close();
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally{
                    fw=null;
                    bw=null;
                }
            }
        }

    }
}

```

##### 3.2 通过文件字节流完成文件复制（视频，压缩包）

```Java

import java.io.*;

//2.通过文件字节流完成文件复制（视频，压缩包）
public class FileTest2 {
    public static void main(String[] args) throws IOException {
        FileInputStream  fip=new FileInputStream("D:\\day01.zip");
//        BufferedInputStream bis=new BufferedInputStream(fip);
        FileOutputStream fos=new FileOutputStream("D:\\day02.zip");
//        BufferedOutputStream bos=new BufferedOutputStream(fos);
        int len=-1;
        byte[] b=new byte[5];
        while((len=fip.read(b))!=-1){
//            System.out.println(new String(b,0,len));
            fos.write(b,0,len);
        }
        fos.close();
        fip.close();
    }
}

```

##### 3.3 统计工作空间里所有的.java文件有多少行·

```Java

import java.io.*;

//3.统计工作空间里所有的.java文件有多少行
public class FileTest3 {
    static int count=0;
    public static void main(String[] args) throws IOException {
        File f=new File("C:\\Users\\13366\\IdeaProjects\\javasecode");
        System.out.println(countJava(f));

    }
    public static String countJava(File f) throws IOException {
//        int count
        FileReader fr=null;
        if(f.isDirectory()){
            File[] fs=f.listFiles();
            for (File f1:fs) {
                countJava(f1);
            }
        }else if(f.getName().endsWith(".java")){
            fr=new FileReader(f);
            BufferedReader  br=new BufferedReader(fr);
            String str="";
            while((str=br.readLine())!=null){
                count++;
//                System.out.println(str);
            }
            fr.close();
            br.close();
        }

        return ".java文件有"+count+"行";
    }
}

```

## 五、内存的输入输出流

字符的输入输出流：

StringReader---从内存读取数据

StringWriter----往内存里边写数据

字节的输入输出流：

ByteArrayOutputStream---内存字节输出流

#### 1.系统流--底层根据字节流完成

1.  out ，err，in（都是字节流）

2. 系统流都是静态的，使用系统流时不要关流

   ```java
   import java.util.Scanner;
   public class SystemDemo1 {
       public static void main(String[] args) {
           //out是字节输出流的类型
           //System.out.println(1);
           //err输出错误的内容---输出的颜色是红色
           //err是字节输出流
           //System.err.println(1);
           //in是字节输入流类型
           //new Scanner(System.in );
           //
           Scanner sca=new Scanner(System.in);
           int num1=sca.nextInt();
           System.out.println(num1);
           //关流---系统流是静态的被所有变量共享，所以只要有一个对象关流，其他的都是关流的状态
           //
           //sca.close();
           Scanner sc2=new Scanner(System.in);
           int num2=sc2.nextInt();
           System.out.println(num2);
           //sc2.close();
       }
   }
   ```

#### 2.练习

  通过已学的流完成从控制台获取一行数据

#### 3.PrintStream打印流

把需要打印的信息放到输出流之中进行数据传输

```java
import java.io.FileNotFoundException;
import java.io.PrintStream;
public class PrintStreamDemo {
    public static void main(String[] args) throws FileNotFoundException {
        //创建打印流对象
        PrintStream ps=new PrintStream("D:\\1.txt");
        //往外打印信息
        ps.println("djs");
        ps.println(15);
        ps.close();
    }
}
```

## 六、序列化和反序列化

![](https://note.youdao.com/yws/api/personal/file/2AE1BD1E56DA4124A21CFF3F6FD1F86E?method=download&shareKey=b868fba14f3b90860b0250c52be91e21)







序列化：把对象以及相关信息转成字节数组  

反序列化：把字节数组转会成对象

持久化：字节数组存储在硬盘

ObjectOutputStream---序列化    ObjectInputStream---反序列化

注意：

1. 序列化时也会把类的属性一起序列化过去

2. static /transient修饰的属性不能被序列化

3. 类要实现Serializable接口产生的对象才能支持序列化

4. serialVersionUID---版本号  

   当对象即将要进行序列化时会根据当时类的属性和方法一起计算出一个版本号，版本号计算出来之后会随着对象序列化一起序列化出去。当进行反序列化时会再次根据类的当前属性和方法计算出一个版本号，然后再拿着计算出来的版本号和之前序列化一起过来的版本号进行比较，如果相等就能正常反序列化，反之就会报错。如果想让前后版本号一定一致，就可以给类里添加版本号的属性，这个属性serialVersionUID要被private static final long共同修饰。

5. 集合和映射对象都不能被序列化过去，只能一 一遍历集合和映射，获取每个元素对象再进行序列化。

```java
import java.io.Serializable;
//只有实现Serializable这个接口，类产生的对象才能进行序列化
public class Person implements Serializable {
    //指定版本号的值---前后两个版本号不在根据属性和方法进行计算只看指定的值
    private static final long serialVersonUID=156276798797566L;
    //属性--在相关信息中
    String name;
    int age;
    // char gender;
    //  static String room;静态的--不能被序列化过去
    //强制不想让属性序列化过去--transient修饰
    transient int weight;
    //方法---不在相关信息中
    public void m() {
        System.out.println("m");
    }
}
```

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
//序列化
public class ObjectOutputStreamDemo1 {
    //一般后边有able的都是接口
    public static void main(String[] args) throws IOException {
        //创建流对象
        ObjectOutputStream oos=new ObjectOutputStream(new FileOutputStream("p.data"));
        //创建person类对象
        Person p=new Person();
        p.name="dd";
        p.age=15;
        //通过流对象传输对象--把对象转为字节数组
        oos.writeObject(p);
        //关流
        oos.close();
    }
}
```

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.ObjectInputStream;

//反序列化
public class ObjectInputStreamDemo {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        //创建流对象
        ObjectInputStream ois =
                new ObjectInputStream(new FileInputStream("p.data"));
        //开始读取字节数组--转回成一个对象
        Person o = (Person) ois.readObject();
        //关流
        ois.close();
        System.out.println(o.name + "," + o.age);
    }
}
```













