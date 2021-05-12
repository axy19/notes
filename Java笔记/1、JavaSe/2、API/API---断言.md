# API---断言

概念：对结果进行预测，如果预测成功就执行结束，如果预测失败就报错。

开启断言

![](https://note.youdao.com/yws/api/personal/file/8777FD099C00462AA457F2FC79D6A90E?method=download&shareKey=391758849d5b61ebbcefe8e1fb577405)

![](https://note.youdao.com/yws/api/personal/file/3FD07EA70D15400088BAFB5473474A39?method=download&shareKey=6afd2e656768d2b36d3e9bb7f1860d5c)

```java
import java.util.Scanner;
public class AssertDemo {
    public static void main(String[] args) {
        System.out.println("请输入小于10的整数");
        int n=new Scanner(System.in).nextInt();
        n=n>>1;
        //断言
        //预测代码执行的结果
        //如果预测成功断言结束
        //如果预测结果失败就会报错（先开启断言  看看在哪个类里开启断言）
        assert n<5:"失败的原因是输入的整数大于10";
        System.out.println(n);
    }
}
//输出结果
/*请输入小于10的整数
20
Exception in thread "main" java.lang.AssertionError: 失败的原因是输入的整数大于10
	at assertdemo.AssertDemo.main(AssertDemo.java:14)*/
```

