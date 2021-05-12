[TOC]
# 映射(Map<K,V>)

## 一、概念

1. 可以存储多个有关系的一对数据（对象）的容器

2.  一个键（x）对应一个值(y)，值可以重复，映射由多个键和多个值组成   y=2*x+1;

3. 键是K，值是V，由于泛型的指定映射中的元素类型只能是引用类型

4. 因为映射存储的是一对对数据，为了方便表示与操作这一对对数据就可以把这些一对对数据抽取成一个实体类（Entry）,Entry产生的对象就是具体的一对数据（键值对），多个映射也是由多个键值对组成。

   ```java 
   import java.util.Collection;
   import java.util.HashMap;
   import java.util.Map;
   import java.util.Set;
   
   public class MapDemo1 {
       public static void main(String[] args) {
           //创建映射对象
           Map<String, Integer> m = new HashMap<>();
           //给映射对象添加元素
           m.put("a", 168);
           m.put("e", 152);
           m.put("t", 145);
           m.put("i", 156);
           m.put("bnb", 154);
           //键不能重复，如果存入的新的元素键发生重复，值会覆盖之前的值
           m.put("a",167);//a 167
           //值可以重复
           m.put("asd",156);
           //没有保证存入数据顺序--
           // 键决定了键值对存放的位置--键是由HashMap进行存储。
           // 存储完之后键的位置就确定了，随之在后边追加上对应的值。
           System.out.println(m);
           //判断映射中是否包含指定的键
           System.out.println(m.containsKey("a"));//true
           //判断映射中是否包含指定的值
           System.out.println(m.containsValue(155));//false
           //把映射里所有的键值对存放到Set集合
           Set<Map.Entry<String,Integer>> s=m.entrySet();
           //根据键获取值
           System.out.println(m.get("asd"));//156
           //把映射中所有的键存储到一个Set集合中
           Set<String>  ss=m.keySet();
           //根据键删除键值对
           m.remove("asd");
           System.out.println(m);
           //根据键和值都要匹配才能删除
           m.remove("a",168);
           System.out.println(m);
           //把所有的值存放的collection集合里
           Collection<Integer> c=m.values();
       }
   }
   ```

## 二、映射的遍历

- 获取所有的键再获取所有的值

- 获取所有的键值对再分别获取键和值（常用）

  ```java
  import java.util.HashMap;
  import java.util.Map;
  import java.util.Set;
  
  public class MapDemo2 {
      public static void main(String[] args) {
          //创建映射对象
          Map<String, Integer> m = new HashMap<>();
          //给映射对象添加元素
          m.put("a", 168);
          m.put("e", 152);
          m.put("t", 145);
          m.put("i", 156);
          m.put("bnb", 154);
          m.put("w", 167);
         /* //把映射中所有的键放到set集合中
          Set<String> s = m.keySet();
          //遍历set集合
          for (String str : s) {//增强for循环底层还是迭代遍历
              //通过键获取值
              System.out.println(str + "=" + m.get(str));
          }*/
         //把映射中所有的键值对放到Set集合中--Entry的每个对象（具体的键值对）
          Set<Map.Entry<String,Integer>> ss=m.entrySet();
          for (Map.Entry<String,Integer> e:ss) {//每个e代表Entry对象，具体的键值对
              System.out.println(e.getKey()+"="+e.getValue());
          }
          //常用
          for(Map.Entry<String,Integer> i:m.entrySet()){
              System.out.println(i.getKey()+"="+i.getValue());
          }
      }
  }
  ```

练习：给定一个字符串，统计输出每个字符出现的个数

```Java 
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

public class MapTest1 {
    public static void main(String[] args) {
        String str = "ahxnsaaxs";
        char[] c = str.toCharArray();
        int[] count = new int[c.length];
        Map<Character, Integer> m = new HashMap<>();
        outter:for (int i = 0; i < c.length; i++) {
            for (int j = 0; j < i; j++) {
                if (c[i] == c[j]) {
                    m.put(c[j], ++count[j]);
                    continue outter;
                }
            }
                m.put(c[i], ++count[i]);
                continue outter;
        }
       Set<Map.Entry<Character, Integer>> s = m.entrySet();
        for (Map.Entry<Character, Integer> e : s) {
            System.out.println(e);
        }
    }
}
```

实现类：HashMap,Hashtable(不符合驼峰规则)

## 三、HashMap

- 允许null值或者null键存储
- 底层是基于数组加链表实现数据存储
- 默认的初始容量为16（数组长度），默认的加载因子（已经使用的桶数/总桶数）为0.75
- 可以指定初始容量，如果初始容量在2^n<指定容量<= 2^(n+1)  之间，结果最终的数组长度是2^（n+1）
- 默认扩容是增加一倍 ，扩容之后进行rehash
- 链表长度大于八个，在jdk1.8就扭转成二叉树
- 异步式线程不安全

## 四、Hashtable

1. 最早的映射类
2. 键和值都不能是null
3. 默认初始容量为11，默认加载因子为0.75
4. 指定初始容量为多少结果就是多少
5. 默认的扩容是增加一倍再加1---11-->11+11+1=23--->47
6. 同步式线程安全

## 五、集合和映射

映射不是集合---java Collections Frame Work(java集合类框架    Map,Collection,Iterator,Arrays,Collections......)