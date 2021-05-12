[TOC]
# API---时间

#### 一、Date---代表时间和日期的类

```java
import java.util.Date;

public class DateDemo1 {
    public static void main(String[] args) {
        //创建一个代表时间和日期类的对象
        Date d=new Date();
        //Mon Nov 18 11:39:15 CST 2019
        //CST--China Standard Time--中国标准时区--上海
        System.out.println(d);//输出时间和日期
        //Mon（星期） Nov（月） 18（日） 11:39:15（时间） CST 2019（年）
        //指定日期---在指定的年月基础上加上1900年1月
        //代码上有黑线--代表过时了--表示在后边的迭代版本中会去除
        //注解--警告压制
        @SuppressWarnings("depercation")
        Date d1=new Date(2008,8,8);
        System.out.println(d1);//Tue Sep 08 00:00:00 CST 3908
        Date d2=new Date(2008-1900,8-1,8);
        System.out.println(d2);//Fri Aug 08 00:00:00 CST 2008
    }
}
```

####   二、SimpleDateFormat---代表支持时间和字符串可以互转的类

 	字符串转日期（parse 解析）日期转字符串（format 格式化）

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateDemo2 {
    public static void main(String[] args) throws ParseException {
        //1.字符串转日期
        String str = "2012-12-12 12:12:12";
        //创建SimpleDateFomat对象
        //需要先给定字符串中时间和日期的格式,线程不安全
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //把字符串转换成日期
        //ParseException---解析异常
        Date d = sdf.parse(str);
        System.out.println(d);//Wed Dec 12 12:12:12 CST 2012
        //2.日期转字符串
        //创建对象--指定转换的格式
        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
        //日期转字符串
        System.out.println( sdf1.format(d));//2012年12月12日 12时12分12秒
    }
}
```

#### 三、Calendar--代表日历的类

```java
import java.util.Calendar;
import java.util.Date;

public class CalendarDemo1 {
    public static void main(String[] args) {
        //创建日历对象
        Calendar c= Calendar.getInstance();
        //指定时间
        Date d=new Date(2008-1900,8-1,8);
        c.setTime(d);
        System.out.println(c.get(Calendar.DAY_OF_MONTH));//8
        System.out.println(c.get(Calendar.DAY_OF_WEEK));//6

    }
}
```

练习：

  1.每个月的第三周的周六股市开盘，设置任意日期，如果设置的日期还没到第三周的周六就提示交易未开始，如果就是第三周的周六就提示交易正在进行，如果日期在第三周的周六就提示交易已结束。

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class CalendarTest1 {
    public static void main(String[] args) throws ParseException {
        SimpleDateFormat sdf1=new SimpleDateFormat("yyyy-MM-dd");
        String str="2019-11-16";
        Date d=sdf1.parse(str);
        Calendar c=Calendar.getInstance();
        c.setTime(d);
        System.out.println(c.get(Calendar.WEEK_OF_MONTH));
        System.out.println(c.get(Calendar.DAY_OF_WEEK));
        if(c.get(Calendar.WEEK_OF_MONTH)==3&&c.get(Calendar.DAY_OF_WEEK)<7||c.get(Calendar.WEEK_OF_MONTH)<3){
            System.out.println("交易未开始。。。。");
        }else if(c.get(Calendar.WEEK_OF_MONTH)==3&&c.get(Calendar.DAY_OF_WEEK)==7){
            System.out.println("交易正在进行中。。。");
        }else{
            System.out.println("交易已结束。。。。");
        }
    }
}
```

