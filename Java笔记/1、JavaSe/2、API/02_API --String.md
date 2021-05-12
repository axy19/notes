[TOC]
# API --String

## 一、类的介绍

1.  ==最终类：没有子类（提供了大量方法的返回值是新串）==

   "123"---字符串直接量可以拼接任何数据

   int a=7;---int a是变量  7是直接量 a是变量名

2. Java中所有的字符串直接量都是String类的对象。String类的对象都是常量

3. 字符串底层都是==依赖于不可改变的字符数组来实现的==（123无法改变，但是引用可以改变），创建后不能改变。

```Java 
 String str="123";
 //123不可以发生变化，但是str的指向可以变。
 str="abc";
 System.out.println(str);
```

1. 如果String类的内容和后边的一致，就会共用同一个常量池的引用。---共享
2. 所有的常量都存储在常量池中。常量是指123等这些具体的值。

```java
public static void main(String[] args) {
       /* String str="123";//123不可以发生变化，但是str的指向可以变。
        str="abc";
        System.out.println(str);*/
       String s1="ab";//指向常量池
       String s2=new String("ab");//指向堆
       String s3="ab";
       //因为String类前边对象的内容和后边的一致就会共享同一个常量池的引用，
        //所以s3也指向常量池
        //byte b=128-1;
        // 当右边再做运算时如果都是常量在编译时就会自动优化，
        // 直接计算结果值在进行赋值
       String s4="a"+"b";//先自动优化，然后步骤和s3一样。
       String s5="a";//
        // string类底层依赖于无法改变的字符数组实现的所有的不能直接做拼接
//        可以由StringBuilder来实现拼接
      // s5= new  StringBuilder("a").append("b").toString();
    //s5指向堆里
       s5=s5+"b";
        System.out.println(s1==s2);//false
        System.out.println(s1==s3);//true
        System.out.println(s1==s4);//true
        System.out.println(s1==s5);//false
    //s2 s5都指向堆，但是属于堆里不同的区域。
        System.out.println(s2==s5);//false
    }
```

![1573788432738](https://note.youdao.com/yws/api/personal/file/7D73B7E71DBC403682B9F3A38CB6F729?method=download&shareKey=ec130e22cfa95c8e885bded343eeab01)

两张图都是jdk1.7之前的内存图。

![1573788921338](https://note.youdao.com/yws/api/personal/file/94B3232ED7E44B5C896465B33227D60C?method=download&shareKey=908c54d84abf20cc8ed8fad5ce400308)

```java
String s="a";//一个对象

s+="b";

// new StringBuilder("a").append("b").toString();//4个对象
1个new StringBuilder("a"）1个字符数组b对象  1个 new StringBuilder("ab")  .tostring()转字符串=1个
```

两句话一共创建了五个对象。

比较+和StringBuilder()==[jdk1.5出现，不安全]==拼接字符串的效率高低。

1. 空间(StringBuilder占用的空间更小)

    String[] ss={....};//含有100个字符串元素

   a.用+拼接（jdk1.0-1.5使用StringBuffer实现，1.5之后用StringBuilder实现）

     String str="";

   for(String i:ss){

   ​    str+=i;//循环一次创建3个对象，在初始化时i已经存在

   }

   一共创建301个对象

   b.用StringBuilder

   StringBuilder sb=new StringBuilder("")；//1

   for(String i:ss){

      sb.append（i）//循环一次创建1个对象，在初始化时i已经存在

   }

   String str=sb.toString();

   一共创建102个对象

2. 时间（StringBuilder耗时更短）

   ```Java 
   public class StringDemo2 {
       public static void main(String[] args) {
           //返回的是从1970年1月0时0分0秒到现在的毫秒值
           long start=System.currentTimeMillis();
          /* String str="";
           for (int i = 0; i <10000 ; i++) {
               str+="a";
           }*/
          //用StringBuilder拼接耗费的时间更短。
           StringBuilder cb=new StringBuilder("");
           for (int i = 0; i <100000; i++) {
               cb.append("a");
           }
           String str=cb.toString();
           long end=System.currentTimeMillis();
           long time=end-start;
           System.out.println(time);
       }
   }
   
   ```

   综合来说拼接大量的字符串时，StingBuilder效率更高

StringBuilder是==线程不安全，效率较高（jdk1.5出现），底层依赖于可变（指数组可以被外界获取到）的字符数组实现。==在底层Java可以默认通过StringBuilder来把原字符串中所有的内容复制到底层这个可变的字符数组中。底层根据可变字符数组的扩容来实现字符串的拼接。拼接结束之后把扩容之后的可变字符数组内容复制给了新的字符串对象的不可改变的字符数组中。

![1573867201555](https://note.youdao.com/yws/api/personal/file/665DFF59747D4CB49ECF8551980C7409?method=download&shareKey=d8dd612dd2c71db2f73853f0b16c66a4)

==StringBuffer是线程安全，效率较低（jik1.0出现）==

## 二、主要方法

1.charAt(int index)---根据下标值返回对应的字符

2.length()---返回字符串的长度

3.toCharArray()---把字符串转成字符数组进行返回

4.new String(char[] cs,int offset,int count)---把字符数组任意内容转成新的字符串对象进行返回

```java
public class StringDemo3 {
    public static void main(String[] args) {
       String str="edhgv";
//        根据指定的下标返回字符串中指定的字符（下标从0开始）
//        System.out.println(str.charAt(3));//g  下标不能越界
//        数组长度length没有小括号是数组的属性，
//        字符串长度带小括号是方法
      for (int i = 0; i <str.length() ; i++) {
            System.out.println(str.charAt(i));
        }
//       把字符串中的内容转成一个字符数组
        char[] cs=str.toCharArray();
//        把字符数组转成一个新的字符串对象
        String st1=new String(cs);
//        把字符数组转为一个新的字符串对象
        String st2=new String(cs,0,3);
//        遍历数组
        System.out.println(Arrays.toString(cs));
        System.out.println(st1);
        System.out.println(st2);
    }
}
```

5.compareTo(String anotherString)---对两个字符串对象进行字典比较，返回差值

 compareToIgboreCase---对两个字符串对象进行字典比较，返回差值,忽略大小写

字典比较（排序）：

把两个字符串对象转成字符数组依次比较相应的字符是否相等，如果不相等就把两个字符的差值当作返回值，如果相等就接着比较下一位对应的字符是否相等直到其中一个数组遍历完，就返回两个数组长度之差。

如果返回值是正数就表明前面的字符串对象大于后边的字符串对象。

如果返回值是一个负数表明前边的字符串对象小于后边的字符串对象。

如果返回值为0就表明字符串大小相等。

```java
public class StringDemo4 {
    public static void main(String[] args) {
        String str="abhd";
        String str1="chdd";
        System.out.println(str.compareTo(str1));//-2
        String str3="ABHD";
        System.out.println(str.compareToIgnoreCase(str3));//0
    }
}
```

7.concat()----把指定的字符串拼接到原串的末尾

把指定的字符串拼接到原串的末尾，然后当做一个新的字符串对象进行返回。

 +和concat的区别：

​    1. +底层使用StringBuilder实现，concat使用数组扩容完成。

​    2.+可以拼接任意数据，concat只能拼接字符串。

```java
public class StringDemo5 {
    public static void main(String[] args) {
        String str1="abc";
        String  str2="cd";
     //拼接到原串的末尾然后当做一个新的字符串对象进行返回
        System.out.println(str1.concat(str2));
        //abccd
    }
}
```

8.contains()---判断新字符串是否在原字符串中是一个连续的区域（子串可以包含原串）

```java
public class StringDemo8 {
    public static void main(String[] args) {
        String str1="hndhggg";
        System.out.println(str1.contains("g"));//true
    System.out.println(str1.contains("ju"));//false
    }
}
```

 9.endsWith()，startsWith()---判断原串是否以某个子串结尾/开头。

```java
	Stringstr="dsvvgerw";
	//判断原串是否以某个子串结尾
	//XXXXX.avi
	//文件后缀.doc.txt
	System.out.println(str.endsWith(".avi"));
	//判断原串是否以某个子串开头
	//火车G（高铁）Z(直达)
	//车牌号粤（广东）
	//身份证
	System.out.println(str.startsWith("dsv"));

```

10.equals(),equalsIgnoreCase---判断两个字符串对象的地址值和内容是否相等。后边忽略大小写，前边不忽略大小写

```java
public class StringDemo6 {
    public static void main(String[] args) {
        String str1="abc";
        String str2="abc";
        System.out.println(str1.equals(str2));//true
        String str3=new String("abc");
//        instanceof---左边是对象右边可以是本类子类父类
//        判断左边的对象和类的关系
//        String类重写的equals方法会判断地址值
//        也会判断内容是否完全一致
        System.out.println(str1.equals(str3));//true
        String str4="ABC";
//        忽略大小写比较两个对象是否相等--验证码
        System.out.println(str1.equalsIgnoreCase(str4));//true
    }
}
```

11.getBytes()---将字符串对象按照码表转成对应的字节数组

12.new String(byte[] bs,int offset,int length,Charset)---把字节数组部分的信息按照对应的码表转成新的字符串对象

```java
import java.io.UnsupportedEncodingException;

public class StringDemo7 {
    public static void main(String[] args) throws UnsupportedEncodingException {
//        编码：把文字转换为数字的过程叫做编码
        String str = "abc字符";
//        使用utf-8编码，一个汉字占三个字节，
//        所有的完整码表默认兼容西欧码表iso8859-1
//        把字符串转成对应的字节数组--默认按u8转
        byte[] bs = str.getBytes();
// 按照指定的编码进行转化字节数组，gbk一个汉字占两个字节
        byte[] bs1 = str.getBytes("gbk");
        System.out.println(bs.length);//9
        System.out.println(bs1.length);//7
//     将字节数组转换为字符串
        String str2 = new String(bs);//abc字符
//        乱码的产生只能因为前后码表不一致
        String str3 = new String(bs1);//abc�ַ�
//        解决方法：指定编码方式
        String str4 = new String(bs1,"gbk");//abc字符
        String str5 = new String(bs1,0,6,"gbk");
        System.out.println(str2);//abc字符
        System.out.println(str3);//abc�ַ�
        System.out.println(str4);//abc字符
        System.out.println(str5);//abc字�

    }
}
```

13.hashcode()---返回字符串对象的哈希码值，只和字符串的内容和顺序有关。

```java
public class StringDemo9 {
    public static void main(String[] args) {
        String str="abc";
        String  str1=new String("abc");
       //String 类重写的hashcode方法的返回值只和字符串的内容以及顺序有关系
        System.out.println(str.hashCode());//96354
        System.out.println(str1.hashCode());//96354
    }
}
```

14.indexOf()---返回指定字符串在原串中第一次出现的下标值

```java
public class StringDemo9 {
    public static void main(String[] args) {
        String str="abcbc";
//        返回的新串的在原串中第一次出现的下标值（就是新串第一个字符出现的下标）
//       如果没有查找到就返回-1
        System.out.println(str.indexOf("bc"));//1
        // 从指定的下标开始往后找返回的新串下标值
        System.out.println(str.indexOf("bc",2));

    }
}
```

15.intern()---就是把对象的引用指向常量池

```java
public class StringDemo10 {
    public static void main(String[] args) {
        String str1="abc";
        String str2=new String("abc");
        //会把引用的指向由堆内存转向常量池
        str2=str2.intern();
        System.out.println(str1==str2);//true
    }
}
```

16.isEmpty()---判断字符串对象是否是空串

```java
public class StringDemo10 {
    public static void main(String[] args) {
        String str="";//空串
        String str1=new String();//空串栈内的没有默认初始值
        System.out.println(str1.isEmpty());
    }
}
```

17.matches（）---填入正则的语法用于匹配字符串指定信息

```java
public class PatternDemo1 {
    public static void main(String[] args) {
        String str="abc9";
        //匹配字符串abc、
        System.out.println(str.matches("abc"));
        //匹配是否为数字
        //[起始值-结束值]--只能匹配起始值到结束范围内的任意一个字符
        String str1="3";
        System.out.println(str1.matches("[0-9]"));
        //代表匹配任意一个字符
        System.out.println(str.matches("."));
        // \\d--字符串先转义--\d---正则语法[0-9]
        System.out.println(str1.matches("\\d"));
        //匹配字符串中是否出现了数字
        //*代表前面的字符出现的次数可以不出现也可以出现多次--数量词
        System.out.println(str.matches(".*\\d.*"));

    }
}

```

18.replaceAll()--填入正则语法匹配字符串中指定的信息把这些匹配到信息全部替换成新的字符串。

```java
import java.util.Arrays;

public class PatternDemo2 {
    public static void main(String[] args) {
        String str="ndj1njh5643475";
        //根据正则语法匹配对应的字符串中的信息全部替换为对应的内容
        System.out.println(str.replaceAll("\\d","*"));//ndj*njh*******
        //对字符串中数字进行排序
        //把字符串中所有的非数字替换成空串
        str=str.replaceAll("\\D","");
        //转换为字符数组
        char[] s=str.toCharArray();
        //排序
        Arrays.sort(s);
        System.out.println(Arrays.toString(s));
    }
}
```

19.split()--根据正则语法匹配指定的信息当做切割符，就把原串切成字符串数组

```java
public class PatternDemo2 {
    public static void main(String[] args) {
        String str="shd3n2f4s";
        //根据正则的语法匹配指定的内容变成逗号+空格，就把原串切割成字符数组
        // \\d---正则要匹配的信息---切割符
        //如果切割符在最前边就切成空串，最后边直接切掉。
        //如果最前边和中间出现多个切割符在一起，有几个切割符就切几个空串（逗号+空格）
        
        String[] ss=str.split("\\d");
        System.out.println(Arrays.toString(ss));//[shd, n, f, s]
    }
}
```

正则表达式（Pattern）

   主要功能：用特殊的语法格式去匹配字符串指定的信息再进行操作。（jdk1.6Pattern类中可以细看）

20.replace()--把原串中指定的所有子串替换成新的字符串进行返回

```java
public class StringDemo11 {
    public static void main(String[] args) {
        String str="hisndsla";
        //把原串中指定的所有子串替换成一个新的字符串
        System.out.println(str.replace("h","*"));//*isndsla
    }
}
```

  21.substring（）--根据指定下标截取子串进行返回

```java
public class StringDemo11 {
    public static void main(String[] args) {
       /* String str="hisndsla";
        //把原串中指定的所有子串替换成一个新的字符串
        System.out.println(str.replace("h","*"));//*isndsla*/
       String str="aswfdx";
       //根据指定的下标往后截取子字符串
        System.out.println(str.substring(2));// wfdx
        //根据开始下标和结束下标截取子串--左包右不包
        System.out.println(str.substring(1,4));//swf
    }
}
```

22.trim（）--删除前面和后面的空格。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    

```java
public class StringDemo11 {
    public static void main(String[] args) {
        String str=" dskjj\tjmn ";
        System.out.println(str+"m");// dskjj	jmn m
        //删除前面后面的空格，掐头去尾
        System.out.println(str.trim()+"m");//dskjj	jmnm
    }
}
```

23.valueOf（）--可以支持所有的类型数据转成字符串对象

```java
public class StringDemo12 {
    public static void main(String[] args) {
        boolean b=false;
        //把布尔值转成字符串对象
        String str=String.valueOf(b);
        System.out.println(str);//false
        //把整型转成字符串对象
        System.out.println( String.valueOf(100));//100
        int[] arr=new int[2];
        char[] cs={'1','c','y'};
        //底层默认通过String.valueOf转成字符串
        System.out.println(arr);//[I@1540e19d
        //底层默认没有转成字符串
        System.out.println(cs);//1cy
        //如果想看地址值可以.toString()方法
        System.out.println(cs.toString());//[C@677327b6
    }
}

```

#### 练习：

1.输入一个字符串，统计字符串中小写英文字母，数字，其他字符出现的个数。

```java
import java.util.Scanner;

public class StringTest1 {
    public static void main(String[] args) {
        Scanner sc=new Scanner(System.in);
        System.out.println("请输入一个字符串。");
        String str=sc.next();
        char[] c=str.toCharArray();
        int letter=0;
        int num=0;
        for (int i = 0; i <c.length ; i++) {
            if(c[i]>='a'&&c[i]<='z'){
                letter++;
            }
            if(c[i]>='0'&&c[i]<='9'){
                num++;
            }
        }
        System.out.println("小写字母的个数为"+letter+",数字的个数为"+num+",其他字符为"+(str.length()-num-letter));
    }
}
```

2.输入一个字符串，输出字符串中所有的数字之和 字符编码’4‘-’0‘；

```java
import java.util.Scanner;

public class StringTest2 {
    public static void main(String[] args) {
        Scanner sc=new Scanner(System.in);
        System.out.println("请输入一个字符串。");
        String str=sc.next();
        char[] c=str.toCharArray();
        int sum=0;
        for (int i = 0; i <c.length ; i++) {
            if(c[i]>='0'&&c[i]<='9'){
                sum+=c[i]-'0';
            }
        }
        System.out.println(sum);
    }
}
```

3.输入一个字符串，输出字符串中所有的数字排序

```java
import java.util.Arrays;
import java.util.Scanner;

public class StringTest3 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个字符串。");
        String str = sc.next();
        char[] c = str.toCharArray();
        char[] c1 = new char[str.length()];
        int index = 0;
        for (int i = 0; i < c.length; i++) {
            if (c[i] >= '0' && c[i] <= '9') {
                c1[index++]=c[i];
            }
        }
        c1= Arrays.copyOf(c1,index);

        System.out.println(Arrays.toString(c1));
        Arrays.sort(c1);
        System.out.println(Arrays.toString(c1));
    }
}
```

4.输入一个字符串，输出字符串中每个字符出现的个数                             

```java
import java.util.Arrays;
import java.util.Scanner;

public class StringText4 {
    public static void main(String[] args) {
        StringText4.test2();
    }
    //
    public static void test1() {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个字符串。");
        String str = sc.next();
        char[] c = str.toCharArray();
        Arrays.sort(c);
        int[] counts = new int[str.length()];
        outter:
        for (int i = 0; i < c.length; i++) {
            if (i == 0) {
                counts[0]++;
                continue;
            }
            for (int j = 0; j <= i - 1; j++) {
                if (c[j] == c[i]) {
                    counts[j]++;
                    continue outter;
                }
            }
            counts[i]++;

        }

        for (int i = 0; i < counts.length; i++) {
            if (counts[i] != 0) {
                System.out.println(c[i] + "的个数为" + counts[i]);
            }
        }

    }

    //标记的方法实现字符串中每个字符出现的次数
    public static void test2() {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个字符串。");
        String str = sc.next();
        boolean[] b = new boolean[str.length()];
        //初始化数组true未统计，false已统计
        for (int i = 0; i < b.length; i++) {
            b[i] = true;
        }
        for (int i = 0; i < str.length(); i++) {
            if (b[i]) {
                char c = str.charAt(i);
//                表示统计数
                int count = 1;
                for (int j = i + 1; j < str.length(); j++) {
                    if(c==str.charAt(j)){
                        count++;
                        b[j]=false;
                    }
                }
                System.out.println(c+":"+count);
            }

        }
    }
}
```

5.String[] ss={"agf","da","a","abc","dac","daa"}通过冒泡排序进行排序（通过compareTo方法来实现     晚自习）

```java
public class StringTest5 {
    public static void main(String[] args) {
        String[] ss = {"agf", "da", "a", "abc", "dac", "daa"};
        for (int i = 0; i < ss.length; i++) {
            for (int j = 0; j < ss.length - i-1; j++) {
                if (ss[j].compareTo(ss[j + 1]) > 0) {
                    String temp;
                    temp=ss[j];
                    ss[j]=ss[j+1];
                    ss[j+1]=temp;
                }
            }
        }
        System.out.println(Arrays.toString(ss));
    }
}
```

6.把字符串转成字节数组，再把字节数组转成一个新的字符串对象，给定字节数组转换成字符串对象字节数，保证无论给定多少字节数新对象都没有?(晚自习)

```java
public class StringTest6 {
    public static void main(String[] args) throws UnsupportedEncodingException {
        String str = "abc字符串";
        byte[] b = str.getBytes("gbk");
        //字节数
        int n = 5;
//        按照给定的字节数把部分字节数组转成新的字符串
        String str1 = new String(b, 0, n, "gbk");
//    出现？的条件---新串的最后一个字符和原串对应位置的字符是否相等
        if (str.charAt(str1.length()-1)!=str1.charAt(str1.length()-1)){
            char[] c=str1.toCharArray();
            str1=new String(c,0,c.length-1);
        }
        System.out.println(str1);

    }
}
```

7.输入一个字符串，返会第一个字符出现的所有下标值（indexof方法）

```java
public class StringTest7 {
    public static void main(String[] args) {
        String str="jxjsad57452szzzz";
        int index=0;
        char c=str.charAt(0);
        for (int i = 0; i <str.length() ; i++) {
           index=str.indexOf(c,index);
            if (index!=-1){
                System.out.println(c+"："+index);
                index++;
            }else{
                break;
            }
        }
    }
}
```

8.输出字符串中每个字符出现的次数（replaceAll实现）

```java
public class StringTest8 {
    public static void main(String[] args) {
        String str = "hujjhiamkhh";
    /*   while (str.length()!=0){
           char c=str.charAt(0);
           int length=str.length();
           str=str.replaceAll(c+"","");
           int len=str.length();
           System.out.println(c+":"+(length-len));
       }*/
    for (;str.length()!=0;){
        char c=str.charAt(0);
        int length=str.length();
        str=str.replaceAll(c+"","");
        int len=str.length();
        System.out.println(c+":"+(length-len));
    }
    }
}
```

