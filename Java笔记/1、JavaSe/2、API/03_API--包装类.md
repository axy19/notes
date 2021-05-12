# API--包装类

#### 一、概念与分类

 基本数据类型操作起来很不方便，java为了提高操作基本类型数据的效率，提供了基本数据类型对应的类，只要创建出这些类的对象就能对我们的基本类型数据进行更加快捷，方便的操作。

![1573889784100](https://note.youdao.com/yws/api/personal/file/5EB8A43F14BC4E2283E1406BCE3D0746?method=download&shareKey=c8d01e318e9ca1de1ec31204d1257c40)

#### 二、注意

   1.八大包装类除了Integer和Character以外都是首字母大写

   2.通过基本类型数据来构建包装类的对象---封箱

   3.==基本类型数据直接赋值给对应的包装类对象---自动封箱==

​        如何实现：包装类.valueOf()

​        ==包装类对象直接赋值给基本类型数据---自动拆箱==

​         如何实现：包装类对象.xxxValue()			

   4.==自动封箱和自动拆箱都是jdk1.5的新特性==

```java
public class BZDemo1 {
    public static void main(String[] args) {
        //定义整型变量
        int i = 1;
        //创建Integer包装类对象
        //包装类的对象是根据基本类型数据构建的对象--封箱
        Integer in = new Integer(i);
        // 可以支持填入比double类型小的类型
        Double d = new Double(2);
        //把基本类型数据直接赋值给对应的包装类对象--自动封箱
        //自动封箱是从jdk1.5出现
        //下边这句话在底层的完整写法 Integer in=Integer.valueOf（i）
        //自动封箱数据类型必须前后对应
        //包装类.valueOf（包装对应的基本类型数据）
        Integer in1 = 12;//只支持int类型
        //Character.valueOf()来实现自动封箱
        Character c='4';
        System.out.println(in);//1
        System.out.println(d);//2.0
        Integer in2=new Integer(2);
        //包装类的对象直接赋值给我们对应的基本类型数据--自动拆箱
        //jdk1.5出现的 底层： int j=in.intValue();
        //包装类的对象.xxxValue();
        int j=in2;
        double d1=in2;//可以支持比int类型范围大的基本类型来接收
        Double d3=new Double(2.3);
        // double d4=d3.doubleint();
        double d4=d3;
        System.out.println(j);
    }
}
```

   5.所有整数值类型（byte，short，int ，long,char）做自动封箱时会有一个共享区域（-128到128，Charcter：0-128）,返回的是同一个数组的元素，如果不在这个范围就返回一个新对象。

```java
public class BZDemo2 {
    public static void main(String[] args) {
        //在-128到127之间如果做自动封箱，包装类的对象就共享同一个数组
        Integer in1=150;
        Integer in2=150;
        Integer in3=4;
        Integer in4=4;
        //Byte.valueOf((byte)1); //-128到127共享同一个数组
       // Short.valueOf((short)2); //-128到127共享同一个数组
        Character.valueOf((char)4);//0到127
        Long.valueOf((long)444); //-128到127共享同一个数组
        Float.valueOf(2.1f);//没有了
        System.out.println(in1==in2);//false
        System.out.println(in3==in4);//true
    }
}
```

```java
public class BZDemo3 {
    public static void main(String[] args) {
        Integer in=new Integer("123");
        System.out.println(in);//123
        Integer in1=new Integer("+123");
        System.out.println(in1);//123
        Integer in2=new Integer("-123");
        System.out.println(in2);//-123
       /* // NumberFormatException--数字格式异常
        //除了在字符串的最左边允许出现+或者-号其他字符都不能出现
        Integer in3=new Integer("0x123");
        System.out.println(in3);*/
        Double d=new Double("1.2");//可以允许.字符出现
        System.out.println(d);//1.2
        //字符串内容和true的比较忽略大小写，
        // 如果比较成功就返回true，其他情况都返回false
        Boolean b=new Boolean("hjjdi");
        System.out.println(b);//false
        //把字符串直接转成基本数据类型
        int i=Integer.parseInt("123");
        double d1=Double.parseDouble("1.2");
    }
}
```

6.所有的包装类的对象的哈希码值都是固定值

```java
public class BZDemo4 {
    public static void main(String[] args) {
        //固定值
        System.out.println(new Integer(2).hashCode());//2
        System.out.println(new Byte((byte)1).hashCode());//1
        System.out.println(new Short((short)3).hashCode());//3
        System.out.println(new Character('a').hashCode());//97
        System.out.println(new Long(23l).hashCode());
        System.out.println(new Float(2.3f).hashCode());
        System.out.println(new Double(2.3).hashCode());
        System.out.println(new Boolean(true).hashCode());
    }
}
```

7.默认null的哈希码值为0；（object类中算）

8.所有数值类型的包装类的父类都是Number类。

9.Double 的属性NaN 不是一个数。即：

​          NaN 连本身也不相等。--》Double.NaN!=Double.NaN