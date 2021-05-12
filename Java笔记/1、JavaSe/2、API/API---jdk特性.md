[TOC]
# API---jdk特性

#### jdk1.5新特性

- ##### 静态导入  导入静态方法，优先加载

  ```java
  //导入静态方法  *可以匹配当前类下的所有静态方法
  import static java.lang.Math.*;
  
  public class StaticImportDemo {
      public static void main(String[] args) {
          System.out.println(random());
          System.out.println(ceil(1.2));
      }
  }
  ```

- ##### 可变参数 

  - 可以接受任意多个参数
  - 底层基于数组实现
  - 只能出现在参数列表的最后边而且只能出现一次

  ```java
  public class VarialbleDemo {
      public static void main(String[] args) {
          System.out.println(sum(1, 2));
          System.out.println(sum(2, 2, 3));
          System.out.println(sum(2,3,6,5));
      }
      //求和
      //...可变参数---可以接受任意多个参数（类型）的传入问题
      //可变参数的底层是由数组实现
      //就会把传入的参数值一一赋值到数组的对应元素中
      //当有多个参数类型时，可变参数要放在最后(double d,int...i)而且最多只能出现一次
      public static int sum(double d,int...i) {
         int sum=0 ;
          for (int j :i) {
              sum+=j;
          }
          return sum;
      }
  }
  ```

- ##### 枚举

  - 一一列举出值
  - 用Enum表示枚举
  - 枚举常量要在首行而且要在同一行
  - 构造方法需要私有化
  - 可以正常定义属性和方法
  - 可以定义抽象方法
  - jdk1.5开始switch。。。case表达式支持枚举类型

  ```java
  public class EnumDemo {
      public static void main(String[] args) {
          //
          Season spring = Season.spring;
          //支持枚举类型
          switch (spring) {
              case spring:
                  System.out.println("春天");
                  break;
              case summer:
                  System.out.println("夏天");
                  break;
              case autumn:
                  System.out.println("秋天");
                  break;
              case winter:
                  System.out.println("冬天");
                  break;
  
          }
      }
  }
  
  //枚举类
  enum Season {
      //spring 等效于 public final static Season spring = new Season();
      //枚举常量保证要在类的首行，要在同一行,默认调无参
      spring(10) {
          @Override
          public void mm() {
              System.out.println("踏春");
          }
      }, summer(2) {
          @Override
          public void mm() {
              System.out.println("游泳");
          }
      }, autumn(1) {
          @Override
          public void mm() {
              System.out.println("爬山");
          }
      }, winter(3) {
          @Override
          public void mm() {
              System.out.println("滑雪");
          }
      };
  
      //构造方法一定私有化
      private Season(int month) {
      }
  
      //可以正常的定义属性和方法
      int i = 0;
      public void m() {
      }
      //定义抽象方法
      public abstract void mm();
  }
  ```

#### JDK1.8新特性

Lambda表达式，函数式接口

- ##### Stream(操作集合的流式结构)（深入了解）

  提供了大量的函数式接口---使用Lambda表达式

```java
public class StreamDemo {
    public static void main(String[] args) {
        //创建一个集合对象
        ArrayList<String> list = new ArrayList<>();
        list.add("C");
        list.add("C++");
        list.add(".Net");
        list.add("Python");
        list.add("java");
        list.add("C#");
        list.add("GO");
        //筛选出以C开头的元素
       /* for (String str:list) {
            if(str.startsWith("C")){
                System.out.println(str);
            }
        }*/
        //把集合身上的元素放到这个流式结构里进行操作
        Stream<String> s = list.stream();
       /*s.filter(new Predicate<String>() {
           //指定过滤规则
           @Override
           public boolean test(String s) {
               return s.startsWith("C");
           }
       }).forEach(new Consumer<String>() {
           //把过滤之后的元素进行输出
           @Override
           public void accept(String s) {
               System.out.println(s);
           }
       });*/
        //lanmbda表达式
        //筛选C开头,然后排序，然后变小写
        s.filter(s1 -> s1.startsWith("C")).sorted((s2, s3) -> s2.compareTo(s3)).map(str1 -> str1.toLowerCase()).forEach(s1 -> System.out.println(s1));
        //
    }
}
```

- ##### Time包---LocalTime     LocalDate

```java
import java.time.LocalTime;
import java.time.temporal.ChronoUnit;

public class LocalTimeDemo {
    public static void main(String[] args) {
        LocalTime l=LocalTime.now();
        //System.out.println(l);//17:54:17.095
        System.out.println(l.plus(10, ChronoUnit.DAYS));
    }
}
```

排序（堆排序，哈希排序），集合（数据结构），线程，JVM，