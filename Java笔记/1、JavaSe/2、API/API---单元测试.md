# API---单元测试

1. 导测试库 

   ![](https://note.youdao.com/yws/api/personal/file/7AB2E43F1712458089B18463B0ED6BCB?method=download&shareKey=6959302ccd2c113f8545679d86748e9a)

2. 保证单元测试下的方法必须是三无方法（无参，无返回值，无static）

```java
public class JunitDemo1 {
    //导入测试库---有网就行
    //只是在测试执行方法内容---不是调用方法
    //不能有参数因为方法不是调用不能传参
    //不能有返回值，因为没有方法调用者
    //方法必须是非静态方法
    //支持单元测试的方法是三无方法
    //@Test
    public void m(){
        System.out.println(1.2);
    }
}

```

