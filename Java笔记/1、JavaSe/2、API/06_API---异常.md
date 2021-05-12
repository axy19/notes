[TOC]

# API---异常

#### 一、概述

##### 1.概念

在java中异常用于发现问题，反馈问题以及解决问题的一套机制

```java
public class ExceptionDemo1 {
    public static void main(String[] args) {
        //ArithmeticException--编译没错，运行有错 可以处理可以不处理
        System.out.println(1/0);
    }
}
```

##### 2.Throwable

1. Throwable---是异常的顶级父类；

2. 子类：

   1. Error (错误  是合理（逻辑正确语法正确）的应用程序 一般是由外界的环境或者需求来导致) 

   2. Exception（异常  是合理的应用程序可以处理也可以不处理  ）

      处理：1.抛出异常 2.捕获异常

      分类：编译时异常（编译时期就会报错 一定要处理）

      ​              CloneNotSupportedException 克隆不支持异常

      ​              ParseException解析异常

      ​            运行时异常（运行时期报错，可以处理也可以不处理 父类是       RuntimeException）

      ​                ArithmeticException 算数异常

      ​                ArrayIndexOutOfBoundsException数组下标越界异常

      ​               NullPointerException空指针异常

      ​                ClassCastException类型转换异常

      ​                 NumberFormatException 数字格式异常

```java
public class ExceptionDemo1 {
    public static void main(String[] args) {
        //捕获异常
        //方法抛出多少个编译时异常就要捕获多少个异常
        try {//try块内存放的是可能出错的代码
            //调用方法读取文件
               String str=readFile("W:a.txt");
        } catch (PathNotExitsException p) {
            //catch块里会实时的监测try块里是否有异常，
            // 如果出现了异常就进行捕获处理
            //（）用于匹配try块里出现的异常类型 指定具体类型就只能捕获具体类型
            //PathNotExitsException p=new PathNotExitsException();
            //通过方法抛出的异常类对象获取异常类的属性
            System.out.println(p.getMessage());
        } catch (FileNotExitsException f) {
            //输出反馈信息
            //调用父类的getMessage方法
            System.out.println(f.getMessage());
        }catch(NullPointerException n){
            n.printStackTrace();//打印异常栈轨迹
        }
        //捕获异常处理完之后，后边的代码会继续执行

        System.out.println("读取成功");

    }

    //方法上抛出了异常--让调用方法的位置知道会出现异常
    //方法抛出多个异常时用逗号分隔
    //方法抛出多少个编译时异常就需要处理多少个异常
    public static String readFile(String path) throws PathNotExitsException, FileNotExitsException ,NullPointerException{
       //判断参数是否为null
        if(path==null){
            //反馈问题--运行时异常（可以处理也可以不处理）
            throw new NullPointerException();
        }

        //判断路径是否以W盘符
        if (path.startsWith("W")) {
            //路径提供的盘符出现问题
            //发现问题，反馈问题
            throw new PathNotExitsException("您输入的路径有错！！");
            //抛出异常后边的代码都不会执行
        }
        //判断路径是否是.txt文件
        if (!path.endsWith(".txt")) {//把所有不是.txt文件都判断进来
            //发现问题进行问题反馈
            throw new FileNotExitsException("文件不存在。");

        }
        //找到文件 并进行读取
        return "文件内容";

    }
}

//自定义异常类（编译时异常）
class PathNotExitsException extends Exception {


    public PathNotExitsException(String message) {
        super(message);
    }

}
//自定义异常（编译时异常）
//如果自定义异常类继承了除 RuntimeException以及子类的其他类默认就是编译时异常
class FileNotExitsException extends Exception {

    //有参构造
    public FileNotExitsException(String message) {
        //调用父类的有参构造---给父类的属性赋值
        super(message);
    }
}
```

#### 二、抛出多个异常的方式

1. 抛出多个异常想分别处理异常，用多个catch分别捕获

2. 抛出多个异常想进行统一处理，找到多个异常类的统一父类进行捕获。

3. 对于抛出的异常进行分组捕获，用|（或）来分隔一组之间的异常类（jdk1.7出现）

   ```java
   catch (PathNotExitsException1|FileNotExitsException1|NullPointerException p)
   ```

4. 对于抛出多个异常时，子类异常在前，父类异常在后

方法的重写于重载

​       1.同一个类中，方法名一致，参数列表（数据类型 数据个数 顺序）不一致（重载和其他都没有关系）

​       2.父子类中出现了方法签名一致的方法

​          重写原则：方法签名一致；如果父类的方法返回值是基本数据类型/void那么子类要与父类一致；如果父类的返回值类型是引用数据类型，那么子类的方法返回值类型是父类一致或者是父类返回类型的子类；子类不能抛出比父类范围更大范围的异常；子类的访问权限修饰符要大于等于父类的权限修饰符；

```java
import java.io.EOFException;
import java.io.FileNotFoundException;
import java.io.IOException;

public class ExceptionDemo3 {

}
class A{
    //方法重载和抛出的异常,访问权限修饰符，static，final，abstract，返回值没有关系
    public void m()throws IOException {}
}
class B extends A{
    //子类抛出的编译时异常的范围要小或者等于父类编译时异常范围
    //子类不能抛出比父类更大范围的编译时异常
    @Override
    public void m()throws EOFException, FileNotFoundException,NullPointerException {}
}
```

#### 三、finally

   不管try块或者catch块是否执行，finally块里东西都会被执行。

```java 
public class ExpectionDemo4 {
    public static void main(String[] args) {
        try {
            System.out.println("try");
        }catch(Exception e){
            System.out.println("catch");
        }finally{
            //无论try块是否出现异常都会执行finally块
            System.out.println("finally");
        }
    }
}
```

```java
public class ExceptionDemo6 {
    public static void main(String[] args) {
        System.out.println(m());//1
    }
    public static int m(){
        int i=1;
        try{//如果没有catch就表明try块里没有异常被捕获
            //代码从上往下从左往右依次执行
            //执行到try块内容   return i++----return 1；
            //当程序要执行return进行返回时监测到下边有finally块，
            // finally块的内容一定要执行
            //java就会把return 1;这个代码的状态以及返回值都会进行挂起不执行
            //挂起之后执行finally块里的内容 i++  3；执行完毕
            //当finally块执行完毕，java会回头执行挂起的内容 return 1;
            return i++;
        }finally{
            i++;
        }

    }
}
```

```java
public class ExceptionDemo7 {
    public static void main(String[] args) {
        System.out.println(m());//Person{name='jack', age=4, gender=男}
    }
    public static Person m(){
        Person p=new Person();
        p.name="lili";
        p.age=10;
        p.gender='女';
        try{
            p.name="tom";
            p.age=7;
            p.gender='男';
            //挂起的是p的地址值，执行完finally，p对象的属性值可以发生改变
            return p;

        }finally {
            p.name="jack";
            p.age=4;
            p.gender='男';
        }
    }
}
class Person{
    String name;
    int age;
    char gender;

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", gender=" + gender +
                '}';
    }
}
```

```java
public class ExceptionDemo8 {
    public static void main(String[] args) {
        System.out.println(m());//abx
    }
    public static String m(){
        String str;
        try{
             str="abx";
            return str;//挂起的是地址值，地址值不会发生改变所以指定的内容也不会改变。
        }finally{
            str="xbc";
        }
    }
}
```





