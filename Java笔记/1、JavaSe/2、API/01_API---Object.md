[TOC]

# API---Object

API---Application Programing Interfaces(应用程序接口)----提供一系列的接口和接口下的类。

API里提供的所有方法要么是被protected修饰，要么是默认的被public修饰

## 一、概述

是所有类的父类，顶级父类，没有父类。所有的类在我们底层默认继承Object类。

class A extends Object{}

class B extends A{}  B多重继承继承了Object

 public Object m(Object  o){//可以接受所有类型的对象

   return new 引用类型();//Object o=new 引用类型();向上造型

}

## 二、重要方法

####  1.clone()---把原对象身上的属性值复制到新对象进行返回

1. 作用：把原对象身上的属性值复制到新对象进行返回；注意：==实现Cloneable接口的类的对象才支持克隆==

2. Cloneable接口：接口里没有东西，只是一个标记，只要我的类实现类Cloneable这个接口，类产生的对象就支持克隆操作。

3. 此方法是Protected修饰的；

4. 注意：如果是protected修饰的信息，只能是直接继承了Object类的子类A访问，不能在别的类中创建A的对象访问protected修饰的方法

   原因：写的这个类存在在当前的包内，然而Object类在java.lang包内，不是本类，子类，同包类其中的一种。所以在其他类内创建这个类的对象时不可以访问。

```java
package object;
//Cloneable接口：接口里没有东西，只是一个标记，
// 只要我的类实现类Cloneable这个接口，类产生的对象就支持克隆操作。
public class ObjectDemo1 implements Cloneable {
    int i=7;
    public static void main(String[] args) throws CloneNotSupportedException {
//        创建对象
        ObjectDemo1 ob=new ObjectDemo1();
        System.out.println(ob.i);
//        调用clone方法进行克隆
//        CloneNotSupportedException克隆不支持异常
        ObjectDemo1 ob2= (ObjectDemo1) ob.clone();
//        克隆对象调用属性
        System.out.println(ob2.i);
//        前后对象的地址值不相等---把原对象的所有属性值复制到新对象中
        System.out.println(ob==ob2);
    }
}
class ODemo implements Cloneable{
    public void m(){
        ODemo od=new ODemo();
//        od.clone();(可以执行但是要抛出异常)
        ObjectDemo1 od1=new ObjectDemo1();
    }
}
```

#### 2.finalize()---通知系统进行回收垃圾

#### 3.getClass()---返回Object的实际创建类的类型

```java
package object;
public class ObjectDemo4 {
    public static void main(String[] args) {
        //创建对象
        ObjectDemo4 od=new ObjectDemo4();
        //向上造型
        Object o=new ObjectDemo4();
        //包名+类名----全路径名
        System.out.println(o.getClass());//class object.ObjectDemo4
        //返回Object的实际创建类的类型
        System.out.println(od.getClass());//class object.ObjectDemo4
    }
}
```

#### 4.hashCode()---返回对象的哈希码值 

特点：1.分布广2.散列分布---不会重复---唯一--代表对象的地址值，不同对象的哈希码值不一样。

```java
package object;
public class ObjectDemo2 {
    public static void main(String[] args) {
    /*    //创建对象
        ObjectDemo2 od=new ObjectDemo2();
//        向上造型
        Object o=new ObjectDemo2();

        //包名+类名---全路径名
        //返回Object的实际创建类的类型
        System.out.println(o.getClass());
        System.out.println(od.getClass());*/
//    返回的是对象的哈希码值
//        不同对象的哈希码值不一样
//        1.哈希码值的取值范围（和int的取值范围一样但是没有负数0-41亿左右）
//        2.哈希码值散列分布，
//        3.因为取值范围广，散列分布所以哈希码值几乎不会重复
//        哈希码值一定唯一---代表对象的地址值
        System.out.println(new Object().hashCode());
        //把十进制的哈希码值转换为十六进制的形式
        System.out.println(Integer.toHexString(new Object().hashCode()));
        System.out.println(new Object().hashCode());
    }
}
```

#### 5.toString()---默认返回对象的拼接地址值

​    返回对象的属性值是最好的情况，需要让类重写toString方法 （描述信息的类一般都要重写此方法）

```java 
package object;
public class ObjectDemo3 {
    public static void main(String[] args) {
        ObjectDemo3 od=new ObjectDemo3();
        //两个效果一样，因为底层默认让对象调用Object类里的toString()返回对象的拼接地址
        System.out.println(od);
        //调用的是Object类里的toString()返回对象的拼接地址值
        System.out.println(od.toString());
    }
}
class ODemo1{
    //属性
   private String name;
 private   int age;
  private  char gender;
//java提供的重写方法
//重写Object类里的toString方法---返回对象的属性值
//Ctrl+O提示可以进行重写的方法
    @Override
    public String toString() {
        return "ODemo1{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", gender=" + gender +
                '}';
    }
}
```

#### 6.equals(Object obj)---默认是根据两个对象的地址值是否相等

 重写equals方法，希望比较地址值也可以比较属性值。

 重写（1.判断地址值是否相等2.判断参数对象是否为null3.判断对象类型是否一致4.比较对象的属性是否一致。。尤其是引用类型）

```java
package object;
public class ObjectDemo5 {
    public static void main(String[] args) {
        Person p1 = new Person();
        p1.name = null;
        p1.age = 10;
        p1.gender = '女';
        Person p2 = new Person();
        p2.name = null;
        p2.age = 10;
        p2.gender = '女';
        System.out.println(p1.equals(p2));
    }
}
class Person {
    String name;
    int age;
    char gender;
    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (this.getClass() != obj.getClass()) {
            return false;
        }
        Person p1=(Person)obj;
        if(p1.age!=this.age||p1.gender!=this.gender){
            return false;
        }
        if (this.name==p1.name||this.name!=null&&this.name.equals(p1.name)){
            return true;
        }
        return false;
    }
}
```

Null不是空，空不是null

![1574399482591](https://note.youdao.com/yws/api/personal/file/BF595D4E72EF40F69EF675DEF1747AF7?method=download&shareKey=1dbd9befee12df4b101ec4513bc87661)

案例：1.Student类（String name，int age，char gender，String nu）重写equals