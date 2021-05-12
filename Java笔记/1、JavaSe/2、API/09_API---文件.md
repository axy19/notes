

# API---文件

File就是代表文件或者文件夹（目录）的类  DOS磁盘操作系统

```java
import java.io.File;
import java.io.IOException;

public class FileDemo1 {
    public static void main(String[] args) throws IOException {
        //创建一个文件类的对象
        //创建对象过程中不会去检测路径是否存在，只是把路径名放到对象身上
        File file=new File("D:\\a.txt");//给定路径名时要注意\\
        //创建空文件，如果指定的路径位置下已经有了这个文件就会返回false
        //true为创建文件成功
        boolean b=file.createNewFile();//alt+回车--提示如何操作错误
        File file1=new File("D:\\z\\d");
        //创建文件夹--目录---支持创建多级文件夹
        boolean b1=file1.mkdirs();
        //可以删除有无内容的文件
        boolean b2=file.delete();
        //只能删除空目录（空文件夹）
        //删除不会放到回收站直接删除
        boolean b3=file1.delete();
        System.out.println(b);
        System.out.println(b1);
        System.out.println(b2);
        System.out.println(b3);
    }
}
```

```java
import java.io.File;

public class FileDemo2 {
    public static void main(String[] args) {
        //创建代表文件类的对象
        File file=new File("D:\\");
        //把当前目录下的所有信息放到一个新的文件数组中
        //数组元素都是文件类对象
        File[] fs=file.listFiles();
        //遍历数组
        for (File f:fs) {
            System.out.println(f);
        }
        //判断文件类是否是文件还是文件夹
        System.out.println(file.isDirectory());//判断是不是文件夹
        System.out.println(file.isFile());//判断是不是文件
    }
}
```

```java
import java.io.File;

public class FileDemo3 {
    public static void main(String[] args) {
        File file=new File("D:\\a\\1.txt");
        //返回文件名和后缀
        System.out.println(file.getName());//1.txt
        //返回文件所在文件夹的位置
        System.out.println(file.getParent());//D:\
        //设置文件的修改时间

    }
}

```

```java
import java.io.File;
import java.io.FileFilter;
import java.io.FilenameFilter;

public class FileDemo4 {
    public static void main(String[] args) {
        File file=new File("D:\\");
        //展示出当前目录下所有名称包含数字的信息
        //把过滤之后的内容放到数组当中---函数式接口
      /* File[] fs= file.listFiles(new FileFilter() {
            //重写抽象方法---指定过滤规则
            @Override
            public boolean accept(File pathname) {
                return pathname.getName().matches(".*\\d.*");
            }
        });*/
//       File[] fs1=file.listFiles(str->str.getName().matches(".*\\d.*"));
//       对文件名直接过滤---函数式接口
     /*   File[] fs=file.listFiles(new FilenameFilter() {
            //指定过滤规则--重写抽象方法
            @Override
            public boolean accept(File dir, String name) {
                return name.matches(".*\\d.*");
            }
        });*/
        File[] fs=file.listFiles((dir, name) -> name.matches(".*\\d.*"));
        for (File f:fs) {
            System.out.println(f);
        }
    }
}
```

```java
import java.io.File;

public class FileDemo5 {
    public static void main(String[] args) {
        File file=new File("D:\\a.txt");
        //重命名---传入文件类型的新对象（指定路径--指定新名字）
        //重命名是根据底层的剪切来完成的
        boolean b=file.renameTo(new File("d:\\1.txt"));
        System.out.println(b);
    }
}
```

练习：

 1.删除文件(通过递归的思路来完成)（文件下有多个文件）

```java
import java.io.File;
public class FileTest1 {
    public static void main(String[] args) {
        File file=new File("D:\\z");
       deleteFile(file);
    }
    //删除文件的递归方法
    public static void  deleteFile(File file) {
        
        if (file.isDirectory()) {
            //把当前文件夹下的内容列举出来
            File[] fs = file.listFiles();
            //遍历文件类的数组
            for (File f : fs) {//每个f都是文件类对象
                //再次把每个f传入到本方法当中，递归调用
                deleteFile(f);
            }
        }
        //删除空目录和文件
        file.delete();
    }
}
```

2.统计工作空间（idea的工作空间）中有多少.java文件和.class文件

```java
import java.io.File;
public class FileTest2 {
    static int count1=0;//java文件个数
    static int count2=0;//class文件个数
    public static void main(String[] args) {
        File file=new File("D:\\javaseCode");
        System.out.println(tj(file));

    }
    public static String tj(File file){
        if(file.isDirectory()){
            File[] fs=file.listFiles();
            for (File f:fs) {
                tj(f);
            }
        }
        if(file.getName().endsWith(".java")){
            count1++;
        }else if(file.getName().endsWith(".class")){
            count2++;
        }
        return ".java文件有"+count1+"个，.class文件有"+count2+"个";
    }
}
```

