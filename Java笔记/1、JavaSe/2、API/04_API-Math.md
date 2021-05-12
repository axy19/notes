# API---Math

Math:提供大量的基本的数学运算，属性和方法都是静态的（没有往外提供构造方法）

​            构造方法可以不向外提供，可以用private修饰

```java
public class MathDemo {
    public static void main(String[] args) {
        //绝对值的运算
        System.out.println(Math.abs(-10));//10
        //向上取整
        System.out.println(Math.ceil(1.2));//2.0
        //向下取整
        System.out.println(Math.floor(3.9));//3.0
        //四舍五入---结果是整数
        System.out.println(Math.round(2.5));//3
        //产生随机小数---大于等于0.0小于1.0的随机小数
        //产生：是根据底层的伪随机算法得来的
        System.out.println(Math.random());
        //产生一个从20-40之间的随机整数
        //
        System.out.println(20+(int)(Math.random()*20));
        //验证码
        char[] cs={'a','w','5','q','e','y'};
        //空串,用于拼接字符
        String str="";
        int index=0;
        for (int i = 0; i <4 ; i++) {
            index=(int)(Math.random()*6);
            str+=cs[index];
        }
        System.out.println(str);
    }
}

```

#### BigDecimal---用于提供小数的精确运算保证参数是字符串形式

```java
import java.math.BigDecimal;
public class BigDecimalDemo {
    //strictfp修饰方法--会把运算过程从64位提升到80位进行计算
    public strictfp static void main(String[] args) {
        //
        double d=2.1-1.9;//不是精确运算
        //BigDecimal支持小数的精确运算---参数必须是字符串形式
        BigDecimal b=new BigDecimal("2.1");
        BigDecimal b2=new BigDecimal("1.9");
        System.out.println(b.subtract(b2));//0.2
        System.out.println(d);//0.20000000000000018
    }
}
```

#### BigInteger---用于超大数之间的运算

```java
import java.math.BigInteger;

    public class BigIntegerDemo {
        public static void main(String[] args) {
            //支持超大数之间的运算
            BigInteger bi1=new BigInteger("4561286742876457856432563225623");
            BigInteger bi2=new BigInteger("475231234536623104563258963258633.2563688");
            //超大数之间的相乘
            System.out.println(bi1.multiply(bi2));
        }
}
```

