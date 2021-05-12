

# API---Properties（Hashtable的子类）

#### 特点

1. 继承与Hashtable，映射类
2. 可以支持持久化（也就支持序列化）
3. 键和值只能是字符串类型
4. 可以把映射里的键和值写到一个.properties文件中
5. 后缀是.properties的文件如果出现中文就按u-16进行编码
6. 当做配置文件来使用   

```java
import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;

public class PropertiesDemo1 {
    public static void main(String[] args) throws IOException {
        //创建一个映射类对象
        Properties p=new Properties();
        //添加元素---键和值---String类型
        p.setProperty("姓名","lili");
        p.setProperty("gender","female");
        p.setProperty("age","10");
        System.out.println(p);
        //把映射里的键和值写入到其他文件中
        //第一个参数代表选择哪个输出流往外写数据  第二个参数代表说明解释键和值的含义
        //文件后缀只能是.properties
        p.store(new FileWriter("person.properties"),"xxxx");
/*        #xxxx
          #Mon Nov 25 14:52:51 CST 2019
         age=10
        name=lili
        gender=female*/

    }
}

```

```java
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

public class PropertiesDemo2 {
    public static void main(String[] args) throws IOException {
        //创建映射类对象
        Properties p=new Properties();
        //读取.properties文件里的内容
        p.load(new FileReader("person.properties"));
        /*//获取文件里的信息
        System.out.println(p.getProperty("name"));
        //只是根据键获取值
        System.out.println(p.getProperty("gender","女"));*/
        //展示列举所有键和值的情况
        p.list(System.out);
       /* //-- listing properties --
        age=10
        name=lili
        gender=female*/
    }
}
```

main方法的功能：

- main方法可以让类单独运行，类不能单独运行

- 程序的入口方法

- jvm调用主方法

  