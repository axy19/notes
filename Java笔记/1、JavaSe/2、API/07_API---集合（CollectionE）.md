[TOC]



## API---集合（Collection<E>）

## 一、Collection<E>概述

1. 在Java.util包内；

2. 可以存储多个数据（对象）而且长度可变的容器

3. Collection<E>是集合的顶级接口

    <E>---泛型，用于指定集合中元素的类型，==由于泛型的指定集合中的元素类型只能是引用数据类型==

   int[] arr;---arr的类型是整型数组类型，arr的元素是int类型

   Collection<String> c;----c的类型是集合类型，集合的元素类型是String类型

   2 3  4 5 int ---Integer   Collection<Integer> c;==可以存储基本类型数据（要把基本类型数据转成对应的包装类对象）==

4. Collection<E>是集合的顶级接口

   子接口--List（列表）、Set（集）、Queue

## 二、List(列表)

==保证存入数据有序；支持下标进行操作；允许元素重复==

​     实现类：ArrayList、LinkedList、Vector、Stack

#### 1.ArrayList（顺序表）

底层由数组实现，内存连续，默认初始容量为10，默认底层根据右移运算来扩容是在原来的基础上增加一半。增删效率较低，查询效率较高。线程不安全的集合。

```java
import java.util.ArrayList;
import java.util.List;
public class ListDemo {
    public static void main(String[] args) {
        //创建列表集合对象
        List<String> list = new ArrayList<>();//向上造型创建对象 
        //往集合中添加元素
        // 保证存入数据有序
        list.add("a");
        list.add("d");
        list.add("x");
        list.add("1");
        System.out.println(list);//[a, d, x, 1]
        //插入元素 下标最大支持和与元素个数相同
        list.add(0,"f");
        System.out.println(list);//[f, a, d, x, 1]
        //根据元素进行删除 如果没有找到指定的元素就不会进行任何操作
        list.remove("d");
        System.out.println(list);//[f, a, x, 1]
        //根据下标删除元素  元素最大支持得值是元素个数-1
        list.remove(0);
        System.out.println(list);//[a, x, 1]
        //判断指定的数据是否包含在集合当中
        System.out.println(list.contains("a"));//true
        //根据指定下标获取元素
        System.out.println(list.get(0));//a
        //返回指定数据在列表集合中第一次出现的下标
        System.out.println(list.indexOf("x"));//1
        //判断是否是空列表
        System.out.println(list.isEmpty());//false
        //替换
        list.set(0,"abc");
        System.out.println(list);//[abc, x, 1]
        //返回元素个数
        System.out.println(list.size());//3
        //截取子列表 左包右不包
       System.out.println(list.subList(0,1));//[abc]
        //把列表转成数组,返回Object数组
        //强制转换异常，都是数组类型只是数组对象需要
        //String[] str=(String[])list.toArray();
        //方式一：
        Object[] os=list.toArray();
        for (Object o:os) {
            String str=(String) o;
        }
        //方式二：
        String[] ss=list.toArray(new String[6]);//填入要转成指定类型数组的对象
        System.out.println(ss);
        //清空集合
        list.clear();
        System.out.println(list);//[]
    }
}
```

练习：实现ArrayList（实现方法） 

```java
package listdemo;


import java.util.Arrays;

public class ListText {
    public static void main(String[] args) {
        ListArray list = new ListArray();
        list.add("x");
        list.add("s ");
        list.add("dc");
        list.add("w");
        list.remove(1);
        System.out.println(list.get(0));
        System.out.println(list);

    }
}

//实现ArrayList
class ListArray {
    //
    String[] data;
    //定义变量代表数组下标/元素个数
    int size = 0;

    public ListArray() {
        data = new String[10];
    }

    //指定数组容量
    public ListArray(int initialCapacity) {
        data = new String[initialCapacity];
    }

    //扩容方法
    public void grow() {
        //判断数组长度是否为0
        if (data.length <= 1) {
            data = Arrays.copyOf(data, data.length + 1);
        }
        data = Arrays.copyOf(data, data.length + (data.length >> 1));
    }

    //实现添加操作
    public void add(String str) {
        //判断数组是否需要扩容
        if (size >= data.length) {//
            grow();
        }
        //添加元素--元素个数+1
        data[size++] = str;
    }

    //下标越界
    public void out(int index) {
        if (index < 0 || index >= size) {
            throw new IllegalArgumentException("Index" + index);
        }
    }

    //插入
    public void add(int index, String str) {
        //判断下标是否越界
        if (index < 0 || index > size) {
            //反馈问题--抛出异常
            throw new IllegalArgumentException("Index" + index);//下标出问题
        }
        if (size >= data.length) {
            grow();
        }
        //实现数组插入--依次往后进行复制
       /* for(int i=size-1;i>=index;i--){
            //前面的数组元素赋值给后一位元素
            data[i+1]=data[i];
        }*/
        //数组复制实现插入
        System.arraycopy(data, index, data, index + 1, size - index);
        //移动结束之后要插入元素
        data[index] = str;
        //元素个数加1
        size++;
    }

    //删除
    public void remove(int index) {
        out(index);
      /*  //实现数组的删除
        for (int i =index; i <size-1 ; i++) {
            data[i]=data[i+1];
        }*/
        System.arraycopy(data, index + 1, data, index, size - index - 1);
        //元素个数减一
        size--;
    }

    //根据元素进行删除
    public void remove(String str) {
        //返回下标值
        int index = indexOf(str);
        //判断返回值是否是-1
        if (index != -1) {
            remove(index);
        }
    }

    //返回指定数据在集合中第一次出现的下标
    public int indexOf(String str) {
        //遍历元素依次比较、
        for (int i = 0; i < size; i++) {
            //依次进行比较判断是否有满足的元素
            if (str == data[i] || str != null && str.equals(data[i])) {
                return i;
            }
        }
        return -1;
    }

    //清空集合
    public void clear() {
        //元素个数置为0
        size = 0;
    }

    //是否包含
    public boolean contains(String str) {
        return indexOf(str) != -1;
    }

    //根据下标获取元素
    public String get(int index) {
        out(index);
        //返回数组元素
        return data[index];
    }

    //判断是否为空
    public boolean isEmpty() {
        return size == 0;
    }

    //返回元素个数
    public int size() {
        return size;
    }

    //返回子列表
    public ListArray subList(int fromIndex, int toIndex) {
        //下标越界
        out(fromIndex);
        out(toIndex);
        //保证fromIndex要小于等于toIndex
        if (fromIndex > toIndex) {
            throw new IllegalArgumentException("fromIndex" + fromIndex + ",toIndex" + toIndex);
        }
        //创建一个新列表
        ListArray list = new ListArray(toIndex - fromIndex);
        //原数组的元素复制到新列表的数组当中
        System.arraycopy(data, fromIndex, list.data, 0, toIndex - fromIndex);
        //保证新列表能遍历到元素
        list.size = toIndex - fromIndex;
        return list;
    }

    //输出集合内容
    @Override
    public String toString() {
        //用StringBuilder拼接
        StringBuilder sb = new StringBuilder("[");
        //遍历数组元素，进行拼接
        for (int i = 0; i < size; i++) {//元素个数
            if (i<size-1) {
                sb.append(data[i]).append(", ");
            }else{
                sb.append(data[i]);
            }

        }

        String str = sb.toString();

        return str += "]";

    }
}
```

####   2.LinkedList(链表)

底层基于节点（静态内部类）实现，通过节点之间的地址值的指向来维系节点。内存不连续，==不需要扩容==。增删效率较高，查询效率较低。线程不安全的集合。

![1574155624017](https://note.youdao.com/yws/api/personal/file/C7CD7C8C0C204352A4EE8FEF08C2327B?method=download&shareKey=be0c8aabbfebe85ca7daf61d55a92fa7)

练习：用内部类实现链表。（随意）

​         实现栈结构（先进后出  获取最后一个存放的元素，删除最后一个存放的元素）

思考：在查询和增删操作相同的场景下，选LinkedList还是ArrayList?

两个时间消耗的几乎相同，决定两个集合效率高低的是占用的空间，==因为linkedlist内存不连续，不需要扩容，占用的空间更少，所以选择LinkedList==

栈内存主要用于计算，java中所有的计算都是在栈中完成的。

#### 3.Vector:向量

   1.底层基于数组实现

   2.默认的初始容量为10，默认扩容是根据底层的三目运算为原来的二倍

​      如果指定了增量的大小 ，每次扩容就是在原来的基础上加上增量的值

  3.==最早的集合类==，在collection之前就出现了

  4.线程安全的集合

```Java
		public class VectorDemo{
		public static void main(String[]args){
		//创建Vector类对象---集合对象
		//如果没有设置增量，默认扩容是原来的两倍
		//如果指定了增量的大小，每次扩容是在原来的基础上加上增量的值
		Vector<String> v=new Vector<>(10,5);
		//添加元素
		/*for(inti=0;i<16;i++){
		v.add("a");
		}*/
		v.add("a");
		v.add("g");
		v.add("t");
		v.add("q");
		//输出集合容量
		//System.out.println(v.capacity());
		
		//调用elements方法返回最古老的迭代器
		//会默认把Vector对象身上的所有元素放到迭代器上
		Enumeration<String>e=v.elements();
		//迭代遍历
		while(e.hasMoreElements()){//判断是否还有元素
		//获取元素
		String str=e.nextElement();
		System.out.println(str);
		}
		}
		}

```

#### 4.Stack(栈结构)

  1.遵循先进后出（fifo）的原则

  2.==继承于Vector==（和Vector特点一致）

​    入栈/压栈---往栈中添加元素；  出栈/弹栈---从栈中获取元素

​     栈顶元素--最后一个存放的元素

​    栈底元素--第一个存放的元素

```java
import java.util.Stack;

public class StackDemo {
    public static void main(String[] args) {
        //创建集合对象--栈集合对象
        Stack<String> s=new Stack<>();
        //入栈
        s.push("a");
        s.push("b");
        s.push("s");
        //保证list接口特点能保证数据存放的顺序
        System.out.println(s);//[a, b, s]
        //判断是否是空栈
        System.out.println(s.empty());//false
        //获取栈顶元素并不删除
        System.out.println(s.peek());//s
        //返回指定元素在栈结构中的位置，从栈顶往下查找，从1开始计数
        System.out.println(s.search("s"));//1
        //获取栈顶元素并删除
        System.out.println(s.pop());//s
        System.out.println(s);//[a, b]
    }
}
```

练习：通过数组实现栈结构

## 三、迭代器（Iterator---遍历集合）

特点：

-  底层是通过指针的挪动来遍历集合元素
- ==遍历期间不能直接增删原集合的内容==
- Iterable 接口中定义了可以返回Iterator接口的方法iterator()
- 类实现了Iterable接口产生的对象就可以通过增强for循环遍历（增强for循环底层是迭代器实现的），增强for循环是jdk1.5的新特性

![1574218328312](https://note.youdao.com/yws/api/personal/file/683897D6C6554CDE841D80DFABCC11EF?method=download&shareKey=72ad97aa64634d5a3594948003dafc50)

```Java
		publicclassIteratorDemo{
		publicstaticvoidmain(String[]args){
		//创建集合对象
		List<String>list=newArrayList<>();
		list.add("abc");
		list.add("bc");
		list.add("ac");
		list.add("ab");
		//调用iterator方法返回新的迭代器
		Iterator<String>in=list.iterator();
		//迭代遍历
		while(in.hasNext()){
		//获取元素
		Stringstr=in.next();
		//通过迭代器的对象删除遍历已经获取的元素(集合元素)
		//可以正常删除---是根据在遍历期间改变标记值来间接删除原集合元素
		//in.remove();
		//通过集合对象删除
		//不能对原集合直接增删---遍历期间改变原集合，遍历结束时会根据标记值和原集合进行状态的对比
		list.remove(str);
		System.out.println(str);
		}
		System.out.println(list);
		}
		}

```

## 四、比较器：Comparator函数式接口

特点：

- 重写compare方法，指定比较规则
- 如果比较器已经指定了规则，比较器会根据底层的排序算法依次对集合中所有的元素进行排序
- 调用排序方法sort时，在sort方法中传入Comparator接口的实现类

```Java 
import java.util.ArrayList;
import java.util.List;

public class ComparatorDemo {
    public static void main(String[] args) {
        //创建列表对象
        List<String> list = new ArrayList<>();
        list.add("ass");
        list.add("er");
        list.add("bg");
        list.add("w");
        //调用sort方法对列表元素进行排序--按首字母升序
        //Comparator（函数式接口  比较器）---把集合对象身上的所以元素依次获取，自动进行移动
       /* list.sort(new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o1.charAt(0)-o2.charAt(0);
            }
        });*/
       //通过lambda表达式实现
        list.sort((str1,str2)->{return str2.charAt(0)-str1.charAt(0);});
        System.out.println(list);
    }
}
```

## 五、Set(散列)

存储的元素不能重复；无序（存入数据的顺序）；

​    实现类：HashSet、TreeSet

#### 1.HashSet

- 底层是由数组+链表实现数据存储；
- 如果改变加载因子越大，导致大量的元素对象存储在某些链上就导致链的长度过长，降低查询效率；如果加载因子越小，导致会进行频繁地扩容和rehash操作浪费大量的内存空间。
- ==如果某个链的长度过长，大于8个，在jdk1.8就会被扭转成一个二叉树表示，提高查询效率。==
- 不能保证迭代顺序

jdk1.7的：

![1574232187571](https://note.youdao.com/yws/api/personal/file/2E3854EE68FE489A976058F87FFCCDBC?method=download&shareKey=697caf76995a085fc242c43ef6720abb)

```java
import java.util.HashSet;

public class HashSetDemo {
    public static void main(String[] args) {
        //创建散列集合的对象
        HashSet<String> h = new HashSet<>();
        //添加元素
        h.add("a");
        h.add("r");
        h.add("d");
        h.add("g");
        h.add("we");
        h.add("b");
        h.add("aw");
        h.add("adx");
        //不能存储重复元素
        h.add("a");
        //输出的结果无序
        System.out.println(h);
    }
}

```

#### 2.TreeSet

- 默认会对存储的内容进行自然排序---升序；只有类实现了Comparable接口产生的对象并且要重写compareTo方法（指定排序规则），才能进行排序。

- 底层基于二叉树实现


```java
import java.util.TreeSet;

public class TreeSetDemo {
    public static void main(String[] args) {
     /*   TreeSet<String> t = new TreeSet<>();
        t.add("a");
        t.add("ab");
        t.add("abc");
        t.add("f");
        t.add("h");
        t.add("n");
        t.add("c");
        //会对存储的内容进行默认排序---升序---自然排序
        System.out.println(t);*/
        //按照分数的从大到小进行排序
        TreeSet<Person> t = new TreeSet<>();
        //添加元素---Person类的对象
        t.add(new Person("ae", 12, '女', 92));
        t.add(new Person("ac", 15, '男', 87));
        t.add(new Person("ad", 13, '男', 94));
        t.add(new Person("aa", 14, '女', 82));
        //根据分数排序
        System.out.println(t);
    }
}

class Person implements Comparable<Person>{
    String name;
    int age;
    char gender;
    //分数
    int score;

    public Person(String name, int age, char gender, int score) {
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.score = score;
    }
    //重写接口里的抽象方法
    //用于指定排序规则，按照对象的分数进行排序
    @Override
    public int compareTo(Person o) {
        return o.score-this.score;//前面的对象的分数于后边对象分数之差--降序
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", gender=" + gender +
                ", score=" + score +
                '}';
    }
}
```

## 六、Queue(队列结构)

 遵循先进先出的原则（FIFO）

 第一个存放到队列的元素---队头元素

最后一个存放到队列的元素---队尾元素

![1574237432266](https://note.youdao.com/yws/api/personal/file/540F8DB478524C1E8FF2B64313B1F059?method=download&shareKey=98874d43eee7237dcf5e25672c91c6a6)

```java
import java.util.LinkedList;
import java.util.Queue;

public class QueueDemo1 {
    public static void main(String[] args) {
        Queue<String> q=new LinkedList<>();
        //获取队头元素
        //没有队头元素就报错--NoSuchElementException
       // System.out.println(q.element());
        //没有队头元素---返回null值并没有报错
        System.out.println(q.peek());
    }
}
```

## 七、泛型 （参数化类型）

1.==泛型是jdk1.5的新特性==

2.遍历数组---由于元素类型不确定需要进行判断每个对象的类型，所以jdk1.5引入泛型  

3.泛型可以接受所有引用类型，当指定了泛型的类型后续操作都是这个类型，就不能接受其他类型。---泛型的擦除（发生在编译时期）

4.泛型的上下限（规定接受类型的范围）

泛型没有向上造型  （下边不对）

![1574241469555](https://note.youdao.com/yws/api/personal/file/111979B0E48542FEAAF759CBDDA3A845?method=download&shareKey=3ce19e3af2c6707f9e06029f50314521)

泛型的上限：

 <? extends Number>---可以接受的类型包括Number类以及子类

 <?  extends 类/接口>---可以接受当前的类或者接口类型的子类/实现类

![1574241646315](https://note.youdao.com/yws/api/personal/file/A6D041D902EA4080AED13438581CD478?method=download&shareKey=733f64773532703f32370862399bcd53)

泛型的下限：

<? super String>----可以接受最小范围为字符串类型

<? super 类/接口>---可以接受的当前类/接口以及父类/父接口

```java
publicclassTypeDemo2{
publicstaticvoidmain(String[]args){
//
List<Integer>list1=newArrayList<>();
list1.add(12);
list1.add(2);
list1.add(1);
list1.add(132);
//
List<Double>list2=newArrayList<>();
list2.add(2.3);
list2.add(12.3);
list2.add(23.3);
list2.add(2.03);
//
List<String>list3=newArrayList<>();
list3.add("abc");
list3.add("bc");
list3.add("ac");
list3.add("ab");
//调用方法
m(list1);//List<Number>list=newArrayList<Integer>();泛型没有向上造型
m(list2);
//m(list3);
}
//遍历数字集合对象
//<?extends Number>---可以接受的类型包括Number类以及子类
//<?exends类/接口>---可以接受当前的类/接口以及子类/实现类
//泛型的上限
publicstaticvoidm(List<?extendsNumber>list){
for(Objecti:list){
System.out.println(i);
}
}
//泛型下限
//接收最小范围为String
publicstaticvoidn(List<?superString>list){

}
}

```

无论是类还是接口编译之后都是.class文件

