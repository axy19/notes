[TOC]
# API---反射

#### 1.概念

耦合度：多个模块之间的关联关系（高内聚，低耦合）

![](https://note.youdao.com/yws/api/personal/file/4ED95886DFD4405CA371ACA1C92A33CF?method=download&shareKey=26f8318b74140b873a9e25a697e2d2f4)

反射：解析类的信息，获取==字节码对象==产生实例对象的过程。==（发生在运行时期）==

Class----代表类的类（产生的对象就是实际的类---字节码对象）

Method---代表方法的类（产生的对象就是实际的方法）4

Field----代表属性的类（产生的对象就是实际的属性）

Constructor---代表构造方法的类 （产生的对象就是实际的构造方法）

Annotation----代表注解的类（产生的对象就是实际的注解）

#### 2.获取字节码对象

1. 通过类名.class返回字节码对象
2. 通过对象名.getClass()方法返回字节码对象
3. 通过Class.forName"全路径名")

```java
public class ClassDemo {
    public static void main(String[] args) throws ClassNotFoundException {
        //获取字节码对象
        //1.通过名称.class返回字节码对象
        //字符串的字节码对象
        Class<String> s = String.class;
        System.out.println(s);//class java.lang.String
        //接口的字节码对象
        Class<List> l = List.class;
        System.out.println(l);//interface java.util.List
        //数组的字节码对象
        Class<int[]> i = int[].class;
        System.out.println(i);//class [I
        ///整型的字节码对象
        Class i2 = int.class;
        System.out.println(i2);//int
        //2.通过对象调用getClass()方法返回字节码对象
        String str = "nxz";
        //getClass()返回的是范围，需要强制转换成Class<String>
        Class<String> c = (Class<String>) str.getClass();
        System.out.println(c);//class java.lang.String
        Class<String> c2 = (Class<String>) Class.forName("java.lang.String");
        System.out.println(c2);//class java.lang.String
    }
}

```

#### 3.创建实例对象

- 字节码对象直接调用newInstance()，在执行无参构造创建实例对象
- 获取有参构造，调用newInstance（参数），再给有参构造赋值并执行有参构造。

```java
public class ClassDemo2 {
    public static void main(String[] args) throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, ClassNotFoundException {
        //String类的字节码对象
        Class<String> c=String.class;
        //获取实例对象---字节码对象调用newInstance()就是在调用无参构造，执行方法
       /* String str=c.newInstance();
        System.out.println(str);//空串*/
       //获取有参构造---返回一个构造方法类型的对象---具体的构造方法
        Constructor<String> c1=c.getConstructor(c);
        //执行构造方法---返回想要的实例对象
        String str2=c1.newInstance("cbdjh");
        System.out.println(str2);
        //通过字符串获取Integer的实例对象
        Class<Integer> c2=(Class<Integer>)
                Class.forName("java.lang.Integer");
        //获取int类型参数的有参构造
        Constructor<Integer> c3=c2.getConstructor(int.class);
        //执行有参构造返回实例对象
        Integer in=c3.newInstance(14);
        System.out.println(in);
    }
}
```

```java
public class ClassDemo3 {
    public static void main(String[] args) throws Exception {
        //获取字节码对象
        Class<String> c=String.class;
        //获取有参构造---获取指定的构造方法（不管方法的权限修饰符）
        Constructor<String> c1=c.getDeclaredConstructor(char[].class,boolean.class);
        //无论方法是什么修饰的暴力破解就可以访问。
        c1.setAccessible(true);
        //执行构造方法获取实例对象
       String str= c1.newInstance(new char[]{'a','c'},true);
        System.out.println(str);//ac
        //获取所有的指定的构造方法
        Constructor[] cs=c.getDeclaredConstructors();
        for (Constructor i:cs) {
            System.out.println(i);
        }
    }
}
```

#### 4.属性

```java
public class ClassDemo4 {
    public static void main(String[] args) throws Exception{
        //创建字节码对象
        Class<String> c=String.class;
        //获取指定属性---打破了封装的原则
       Field f= c.getDeclaredField("hash");
       //暴力破解
        f.setAccessible(true);
        //对象
        String str="abc";
        System.out.println(str.hashCode());
        //根据获取的属性，改变str对象的属性值
        f.set(str,10);
        System.out.println(str.hashCode());
    }
}
```

#### 5.方法

```java
public class ClassDemo5 {
    public static void main(String[] args) throws Exception{
        Class<String> c=String.class;
        //获取指定的方法---需要指定方法名以及参数类型的字节码文件
        Method m=c.getDeclaredMethod("charAt", int.class);
        //创建对象
        String str="cdf";
        //通过对象调用方法---给方法传入对象然后执行方法
        //str.charAt(1)是等效的
        System.out.println(m.invoke(str,1));
    }
}
```

```java
public class ClassDemo6 {
    public static void main(String[] args) throws Exception {
        List<String> list=new ArrayList<>();
        list.add("abc");
        //泛型擦除---确定具体的类型后续
        //反射是在运行时期对泛型的类型进行改动和编译时期没有关系
        //反射跳过了泛型的类型检查   反射发生在运行时期
        Class<List> c= (Class<List>) list.getClass();
        Method m=c.getDeclaredMethod("add",Object.class);
        m.invoke(list,123);
        System.out.println(list);
    }
}
```

#### 6.反射的缺点

1. 打破了封装的原则
2. 跳过了泛型的类型检查

#### 7.案例：实现clone方法



