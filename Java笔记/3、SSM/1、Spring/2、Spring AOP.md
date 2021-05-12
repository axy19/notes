[TOC]
# Spring AOP

## 一、引入

改造过后的EasyMall成功解决了耦合的问题，但是在很多地方仍然存在非该层应该实现的功能，造成了无法“高内聚”的现象，同时存在大量重复代码，开发效率低下。
此时可以通过代理设计模式，将这部分代码提取到代理者中，简化层中的代码。

```Java
 @Service
public class UserServiceImpl implements UserService {
	@Autowired
	private UserDao userDao;   		
	@Override
	public void registUser(User user) {
	try {
		System.out.println("校验权限。。。");
		System.out.println("开启事务。。。");
  	System.out.println("记录日志。。。");
		userDao.addUser(user);
		System.out.println("提交事务。。。");
	} catch (Exception e) {
		System.out.println("回滚事务");
		e.printStackTrace();
	}
}
			
@Override
public void upToVIP(User user) {
	try {
		System.out.println("校验权限。。。"
    System.out.println("开启事务。。。");
		System.out.println("记录日志。。。");
		userDao.updateUser(user);
		System.out.println("提交事务。。。");
	} catch (Exception e) {
		System.out.println("回滚事务");
		e.printStackTrace();
	}
}
			
@Override
public void removeUser(User user) {
	try {
			System.out.println("校验权限。。。");
			System.out.println("开启事务。。。");
			System.out.println("记录日志。。。");
			userDao.deleteUser(user.getId());
			System.out.println("提交事务。。。");
		} catch (Exception e) {
			System.out.println("回滚事务");
			e.printStackTrace();
		}
	}
}
```

可以用装饰者设计模式和继承完成操作，但是两者各有缺点。

## 二、代理设计模式

```Java
interface Star{
    public void qianming();
    public void chifan();
}

class FBB implements Star{
    public void qianming(){
        System.out.println("fbb签名..");
    }
    public void chifan(){
        System.out.println("fbb吃饭..");
    }
    public void shufa(){
        System.out.println("fbb写书法..");
    }
}
```

#### 1.静态代理

静态代理设计模式特点：
	优点：
		结构清晰 易于理解
	缺点：

​		如果被代理者有多个方法，则代理者也需要开发多个方法，其中往往存在大量重复代码，仍然存在代码重复。

 静态代理设计模式解决了软件分层过程中额外的功能代码侵入模块的问题，将额外的功能代码提取到了代理者中进行，但是静态代理实现的代理者中存在大量重复的代码，并没有解决代码重复问题。所以在真正开发中--包括spring的底层，基本不会使用静态代理。             

静态代理的实现：

```java
 /**
     * 静态代理
     *  优点：好理解
     *  缺点：所有方法都要实现 代码存在大量重复
     */
    @Test
    public void test01(){
        FBB fbb = new FBB();

        class ZuLi implements Star{
            @Override
            public void qianming() {
                System.out.println("你谁啊？");
                fbb.qianming();
                System.out.println("记录一下..");
            }
            @Override
            public void chifan() {
                System.out.println("你谁啊？");
                fbb.chifan();
                System.out.println("记录一下..");
            }
        }

        ZuLi proxy = new ZuLi();

        proxy.qianming();
        proxy.chifan();
    }
```

#### 2.动态代理---jdk内置的动态代理

在jdk中提供了动态代理实现的工具类，直接使用该工具类就可以创建出代理者，并且可以通过内置的回调函数指定代理在工作时的执行逻辑，从而实现基于jdk原生api的动态代理机制。

**java.lang.reflect** 
**类** **Proxy**
[java.lang.Object](mk:@MSITStore:C:\Users\admin\Desktop\JDK_API_1_6_zh_CN.CHM::/java/lang/Object.html)


static [Object](mk:@MSITStore:C:\Users\admin\Desktop\JDK_API_1_6_zh_CN.CHM::/java/lang/Object.html)

[**newProxyInstance**](mk:@MSITStore:C:\Users\admin\Desktop\JDK_API_1_6_zh_CN.CHM::/java/lang/reflect/Proxy.html#newProxyInstance(java.lang.ClassLoader, java.lang.Class[], java.lang.reflect.InvocationHandler))([ClassLoader](mk:@MSITStore:C:\Users\admin\Desktop\JDK_API_1_6_zh_CN.CHM::/java/lang/ClassLoader.html) loader, [Class](mk:@MSITStore:C:\Users\admin\Desktop\JDK_API_1_6_zh_CN.CHM::/java/lang/Class.html)<?>[] interfaces, [InvocationHandler](mk:@MSITStore:C:\Users\admin\Desktop\JDK_API_1_6_zh_CN.CHM::/java/lang/reflect/InvocationHandler.html) h) 
          返回一个指定接口的代理类实例，该接口可以将方法调用指派到指定的调用处理程序。

```java

    /**
     * 动态代理 - java动态代理
     *  优点：代理代码只需要写一遍就可以对多个方法进行代理
     *  缺点：基于接口实现代理，如果方法不属于任何接口，则无法代理
     */
    @Test
    public void test02(){
        //被代理者 - 范冰冰
        FBB fbb = new FBB();

        //代理者 - 助理
        Star proxy = (Star) Proxy.newProxyInstance(
                //类加载器
                FBB.class.getClassLoader(),
                //要实现的接口们
                FBB.class.getInterfaces(),
                //回调函数 - 代理者执行的逻辑
                new InvocationHandler() {
                    /**
                     * @param proxy 代理者 - 助理
                     * @param method 被调用的方法
                     * @param args 被调用的方法的参数
                     */
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("你谁啊？");
                        Object retuObj = method.invoke(fbb, args);
                        System.out.println("记录一下..");
                        return retuObj;
                    }
                }
        );

        //有事找助理，不能找范冰冰
        proxy.qianming();
        proxy.chifan();
        //proxy.shufa();
    }
```

invoke方法的使用

```Java
package cn.tedu;

import java.lang.reflect.Method;

class Person{
    public void rap(String str){
        System.out.println("唱跳rap.."+str);
    }
}

public class Test03 {
    public static void main(String[] args) throws Exception {
        Person cxk = new Person();
        //p.rap();

        Class clz = cxk.getClass();
        Method m = clz.getMethod("rap",String.class);
        m.invoke(cxk,"要要要切克闹，煎饼果子来一套");

    }
}

```

java动态代理的特点：

优点：

不需要像静态代理一样被代理方法都要实现一遍，而只需要在回调函数中进行处理就可以了，重复代码只需编写一次。

缺点：

java的动态代理是通过代理者实现和被代理者相同的接口来保证两者具有相同的方法的，如果被代理者想要被代理的方法不属于任何接口，则生成的代理者自然无法具有这个方法，也就无法实现对该方法的代理。

所以==java的动态代理机制是基于接口进行的，受制于要代理的方法是否有接口的支持。==

#### 3.动态代理 - 第三方包cglib实现的动态代理

CGLIB是第三方提供的动态代理的实现工具，不管有没有接口都可以实现动态代理。

CGLIB实现动态代理的原理是生成的动态代理是被代理者的子类，所以代理者具有和父类即被代理者相同的方法，从而实现代理，这种方式基于继承，不再受制于接口。

缺点：需要导入jar包

spring-core-3.2.3.RELEASE.jar

```java

    /**
     * 动态代理 - cglib动态代理
     *  优点：基于继承实现代理，无论方法是否属于接口都可以代理
     *  缺点：不是java原生的，需要导如第三方包才能使用
     */
    @Test
    public void test03(){
        FBB fbb = new FBB();
        //1.创建增强器
        Enhancer enhancer = new Enhancer();
        //2.指定要实现的接口们，不是必须的
        enhancer.setInterfaces(fbb.getClass().getInterfaces());
        //3.指定父类，必须的
        enhancer.setSuperclass(fbb.getClass());
        //4.指定回调函数
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy mp) throws Throwable {
                System.out.println("你谁啊？");
                Object retObj = method.invoke(fbb,args);
                System.out.println("记录一下..");
                return retObj;
            }
        });
        //5.创建代理者
        FBB proxy = (FBB) enhancer.create();

        //6.有事找代理，不能直接找FBB
        proxy.qianming();
        proxy.chifan();
        proxy.shufa();
    }

```

#### 4.案例

```Java
package cn.tedu;

import org.junit.Test;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface 动物{
    public void 吃();
    public void 叫();
}

class 狗 implements 动物{
    public void 吃(){
        System.out.println("狗在么么么的吃..");
    }
    public void 叫(){
        System.out.println("狗在汪汪汪的叫..");
    }
    public void 看家(){
        System.out.println("狗在看家..");
    }
}

public class Test01 {
    /**
     * 改造不喜欢的方法 - 代理设计模式 - 动态代理 - cglib动态代理
     */
    @Test
    public void test05(){
        狗 dog = new 狗();
        //1.创建增强器
        Enhancer enhancer = new Enhancer();
        //2.指定要实现接口们 - 不是必须的
        enhancer.setInterfaces(dog.getClass().getInterfaces());
        //3.指定要继承的父类 - 必须的
        enhancer.setSuperclass(dog.getClass());
        //4.指定回调函数
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy mp) throws Throwable {
                if("叫".equals(method.getName())){
                    System.out.println("狗在呵呵呵的叫..");
                    return null;
                }else{
                    Object retObj = method.invoke(dog,args);
                    return retObj;
                }
            }
        });
        //5.创建代理对象
        狗 proxy = (狗) enhancer.create();

        //6.有事找代理，不要找dog
        proxy.吃();
        proxy.叫();
        proxy.看家();
    }

    /**
     * 改造不喜欢的方法 - 代理设计模式 - 动态代理 - java动态代理
     */
    @Test
    public void test04(){
        狗 dog = new 狗();
        动物 proxy = (动物) Proxy.newProxyInstance(
            dog.getClass().getClassLoader(),
            dog.getClass().getInterfaces(),
            new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    if("叫".equals(method.getName())){
                        System.out.println("狗在嘻嘻嘻的叫...");
                        return null;
                    }else{
                        return method.invoke(dog,args);
                    }
                }
            }
        );
        proxy.吃();
        proxy.叫();
    }


    /**
     * 改造不喜欢的方法 - 代理设计模式 - 静态代理
     */
    @Test
    public void test03(){
        狗 dog = new 狗();

        class 代理狗 implements 动物{

            @Override
            public void 吃() {
                dog.吃();
            }

            @Override
            public void 叫() {
                System.out.println("狗在哈哈哈的叫..");
            }
        }

        代理狗 proxy = new 代理狗();
        proxy.吃();
        proxy.叫();
    }


    /**
     * 改造不喜欢的方法 - 装饰者设计模式
     */
    @Test
    public void test02(){
        class 装饰狗 implements 动物{
            private 动物 ani;
            public 装饰狗(动物 ani){
                this.ani = ani;
            }
            @Override
            public void 吃() {
                ani.吃();
            }
            @Override
            public void 叫() {
                System.out.println("狗在咩咩咩的叫..");
            }
        }
        狗 dog = new 狗();
        装饰狗 dog2 = new 装饰狗(dog);
        dog2.吃();
        dog2.叫();
    }


    /**
     * 改造不喜欢的方法 - 继承重写
     */
    @Test
    public void test01(){

        class 继承狗 extends 狗{
            @Override
            public void 叫() {
                System.out.println("狗在喵喵喵的叫..");
            }
        }

        //狗 dogx = new 狗();

        狗 dog = new 继承狗();
        dog.吃();
        dog.叫();
    }
}

```

## 三、Spring AOP详解

#### 1.Spring AOP的基本概念

* *连接点（**Joinpoint**）*：在程序执行过程中某个特定的点，比如某方法调用的时候或者处理异常的时候。在Spring  AOP中，一个连接点*总是*表示一个方法的执行。

   通俗讲：

  ​	层于层之间调用的过程中，目标层中可供调用的方法，就称之为连接点。

* 切入点（Pointcut）：匹配连接点的断言。通知和一个切入点表达式关联，并在满足这个切入点的连接点上运行（例如，当执行某个特定名称的方法时）。切入点表达式如何和连接点匹配是AOP的核心：Spring缺省使用Aspect切入点语法。 
  通俗讲：
  在连接点的基础上 增加上切入规则选择出需要进行增强的连接点 这些基于切入规则选出来的连接点就称之为切入点。

* 切面（Aspect）：一个关注点的模块化，这个关注点可能会横切多个对象。事务管理是J2EE应用中一个关于横切关注点的很好的例子。在Spring AOP中，切面可以使用基于模式或者基于@Aspect注解的方式来实现。 
  通俗讲：
  当spring拦截下切入点后将这些切入点交给处理类进行功能的增强，这个处理类就称之为切面。

* *通知（**Advice**）*：在切面的某个特定的连接点上执行的动作。其中包括了“around”、“before”和“after”等不同类型的通知（通知的类型将在后面部分进行讨论）。许多AOP框架（包括Spring）都是以*拦截器*做通知模型，并维护一个以连接点为中心的拦截器链。

  通俗讲：

  在spring底层的代理拦截下切入点后，将切入点交给切面类，切面类中就要有处理这些切入点的方法，这些方法就称之为通知（也叫增强方法）。针对于切入点执行的过程，通知还分为不同的类型，分别关注切入点在执行过程中的不同的时机。     

* 目标对象（Target Object）： 被一个或者多个切面所通知的对象。也被称做被通知（advised）对象。 既然Spring AOP是通过运行时代理实现的，这个对象永远是一个被代理（proxied）对象。 

  通俗讲：

  就是真正希望被访问到的对象。spring底层的动态代理对他进行了代理，具体能不能真的访问到目标对象，或在目标对象真正执行之前和之后是否做一些额外的操作，取决于切面。

* 织入

  就是把处理后的结果返回给目标对象的操作，叫做织入。
  
  ![](https://note.youdao.com/yws/api/personal/file/DDF3CA665ECA4433BB3AC51C4DD96CFE?method=download&shareKey=0902bb67782abe84296d67aed7db534c)

#### 2.Spring AOP的案例

* 导包：之前的6+1然后再2+3

  ![](https://note.youdao.com/yws/api/personal/file/08B45433154246ABA83ADB78FA0E5C76?method=download&shareKey=742351d100110c935ab0af30394f4775)

* 创建配置文件，并导入aop约束

* ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:aop="http://www.springframework.org/schema/aop"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
  ">
   <!--开启包扫描-->
      <context:component-scan base-package="service"></context:component-scan>
    <context:component-scan base-package="servlet"></context:component-scan>
      <context:component-scan base-package="aspect"></context:component-scan>
      <!--允许di注入-->
      <context:annotation-config />
  <beans>
  ```
  
* 创建基本的层级结构调用过程

  service层：

  ```
  public interface Userservice {
      public void addUser();
      public void updateUser();
  }
  ```

  ```Java
  @Service
  public class UserserviceImpl implements Userservice {
      @Override
      public void addUser() {
          System.out.println("添加用户。。。");
      }
      @Override
      public void updateUser() {
          System.out.println("更新用户。。。");
      }
  }
  ```

  web层

  ```Java
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Controller;
  import service.Userservice;
  @Controller
  public class UserServlet {
      @Autowired
      private Userservice userservive=null;
      public void addUser(){
          userservive.addUser();
      }
  
  }
  ```

* 创建一个切面类

  ```
  
  @Component
  public class FirstAspect {
  
      public void first(){
          System.out.println("first....");
      }
  }
  ```

* 定义通知

* ```
  public void first(){
          System.out.println("first....");
      }
  ```

* 配置切面、通知、切入点规则

* ```
   <!--切面配置-->
      <aop:config >
          <aop:aspect ref="firstAspect">
              <aop:before method="first" pointcut="within(service.UserserviceImpl)" />
          </aop:aspect>
      </aop:config>
  ```

* 执行方法，发现切面确实起作用

## 四、切点表达式

#### 1.within表达式

* 通过类名进行匹配粗粒度的切入点表达式


* within(包名.类名)：这个类中所有的连接点都会被表达式识破，成为切入点。

  ```
  <aop:config >
          <aop:aspect ref="firstAspect">
              <aop:before method="first" pointcut="within(service.UserserviceImpl)" />
          </aop:aspect>
  </aop:config>
  ```

* 在within表达式中可以使用*号匹配符，匹配指定包下所有的类，注意，只匹配当前包，不包括当前包的子孙包

  ```
  <!--切面配置-->
  <aop:config >
       <aop:aspect ref="firstAspect">
            <aop:before method="first" pointcut="within(service.*)" />
       </aop:aspect>
  </aop:config>
  ```

* 在within表达式中也可以用==.*==号匹配符，第一个代表一层子包，==不包含子孙包==，第二个代表包下所有的类

  ```
  <!--切面配置-->
      <aop:config >
          <aop:aspect ref="firstAspect">
              <aop:before method="first" pointcut="within(service.*.*)" />
          </aop:aspect>
      </aop:config>
  ```

* 在within表达式中也可以用==..*==号匹配符，匹配指定==包下及其子孙包下==的所有的类

  ```
  <!--切面配置-->
      <aop:config >
          <aop:aspect ref="firstAspect">
              <aop:before method="first" pointcut="within(service..*)" />
          </aop:aspect>
      </aop:config>
  ```

#### 2.execution()表达式

细粒度的切入点表达式，可以以方法为单位定义切入点规则

语法:execution(返回值类型 包名.类名.方法名(参数类型,参数类型…))

* 该切入点规则表示，切出指定包下指定类下指定名称指定参数指定返回值的方法。

  ```
   <!--切面配置-->
  <aop:config >
     <aop:aspect ref="firstAspect">
     <aop:before method="first" pointcut="execution(void service.UserServiceImpl.addUser())" />
     </aop:aspect>
  </aop:config>
  ```

* 该切入点规则表示，切出指定包下所有的类中的query方法，要求无参，但返回值类型不限。

  ```
  <!--切面配置-->
  <aop:config >
     <aop:aspect ref="firstAspect">
        <aop:before method="first" pointcut="execution(* service.*.query())" />
        </aop:aspect>
  </aop:config>
  ```

* 该切入点规则表示，切出指定包及其子孙包下所有的类中的query方法，要求无参，但返回值类型不限。

  ```
  <aop:pointcut expression="execution(* cn.tedu.service..*.query())" id="pc1"/>
  ```

* 该切入点规则表示，切出指定包及其子孙包下所有的类中的query方法，要求参数为int java.lang.String类型，但返回值类型不限。

  ```
  <aop:pointcut expression="execution(* cn.tedu.service..*.query(int,java.lang.String))" id="pc1"/>
  ```
  
* 该切入点规则表示，切出指定包及其子孙包下所有的类中的query方法，参数数量及类型不限，返回值类型不限。

  ```
  <aop:pointcut expression="execution(* cn.tedu.service..*.query(..))" id="pc1"/>
  ```

* 该切入点规则表示，切出指定包及其子孙包下所有的类中的任意方法，参数数量及类型不限，返回值类型不限。这种写法等价于within表达式的功能。

  ```
  <aop:pointcut expression="execution(* cn.tedu.service..*.*(..))" id="pc1"/>
  ```

## 五、Spring的五大通知类型

#### 1.前置通知

1. ==在目标方法（要调的方法）执行之前执行的通知。==

2. 前置通知方法，可以没有参数，也==可以额外接收一个JoinPoint，Spring会自动将该对象传入，代表当前的连接点==，通过该对象可以获取目标对象和目标方法相关的信息。

3. 注意，如果接收JoinPoint，==必须保证其为方法的第一个参数==，否则报错。

4. 配置方式：

   没有JoinPoint时：
   
   ```Java
   @Component
   public class FiastAspect {
    /*前置通知类型*/
       public void before(){
           System.out.println("before1.....");
       }
   }
   ```
   
   有JoinPoint时：
   
   ```Java
     /*前置通知类型*/
       public void before(JoinPoint jp){
           //获取当前拦截的是哪个类
           Object target = jp.getTarget();
           //获取当前拦截的是确切的哪一个方法。
           MethodSignature signature = (MethodSignature) jp.getSignature();
           Method method = signature.getMethod();
           //通过以上两个方法可以记录日志，查看权限等。
           System.out.println("before2...."+target+method);
       }
   ```
   
   配置文件：
   
   ```xml
   <aop:aspect ref="fiastAspect">
      <aop:before method="before" pointcut="execution(void service.UserServiceImpl.add())"></aop:before>
   </aop:aspect>
   ```


#### 2.环绕通知

* ==在目标方法执行之前和之后都可以执行额外代码的通知。==

* 在环绕通知中==必须显式的调用目标方法==，否则目标方法不会执行。

* 这个显式调用是==通过ProceedingJoinPoint来实现的==，可以在环绕通知中接收一个此类型的形参，spring容器会自动将该对象传入，==这个参数必须处在环绕通知的第一个形参位置。==

* ProceedingJoinPoint是JoinPoint的子类，要注意，只有环绕通知可以接受ProceedingJoinPoint，而其他通知只能接收JoinPoint。

* ==环绕通知需要返回返回值，否则真正调用者将拿不到返回值==，只能得到一个null。

* 环绕通知有 

  ​	控制目标方法是否执行

  ​	目标方法执行之前或之后执行额外代码

  ​	控制是否返回返回值

  ​	改变返回值

  的能力

* 环绕通知虽然有这样的能力，但一定要慎用，要小心不要破坏了软件分层的“高内聚 低耦合”的目标。

* 配置方式

  ```xml
   <aop:around method="around" pointcut="execution(void service.UserServiceImpl.add())"></aop:around>
  ```

  没有显示调用：

  ```Java
   /*环绕通知：不显示调用
      * 必须显示调用目标方法不然不会执行，此方式输出的结果直接是
      * around  before.....
      * around  after......*/
      public void around(){
          System.out.println("around  before....");
          System.out.println("around  after.....");
      }
  ```

  有显示调用：

  ```Java
    /*环绕通知：显示调用会执行被拦截下的方法
      * 同时需要返回返回值
      * */
      public Object around(ProceedingJoinPoint pjd) throws Throwable {
          System.out.println("around  before....");
          Object proceed = pjd.proceed();
          System.out.println("around  after.....");
          return proceed;
      }
  ```

#### 3.后置通知

* ==在目标方法执行之后执行的通知。==

  ```
   <!--后置对象的配置-->
  <aop:after-returning method="afterReturning" pointcut-ref="p1" returning="o"></aop:after-returning>
  //returning=“o”这个o要和参数列表中对应。
  ```

* 在后置通知中也==可以选择性的接收一个JoinPoint==来获取连接点的额外信息，==但是这个参数必须处在参数列表的第一个==。

* 在后置通知中，还可以==通过配置returning选项获取目标方法的返回值==

* ==一定要保证JoinPoint处在参数列表的第一位，否则抛异常==

  ```Java
   /*o的名字要与配置的returning等于的值对应*/
      public void afterReturning(JoinPoint jp,Object o){
          Object target = jp.getTarget();
          MethodSignature signature = (MethodSignature) jp.getSignature();
          Method method = signature.getMethod();
          System.out.println("afterReturning......"+method+target+o);
      }
  ```

#### 4.异常通知

* ==在目标方法抛出异常时执行的通知==
  配置方法：
  
  ```
  <aop:after-throwing method="afterThrow" pointcut-ref="p1"></aop:after-throwing>
  ```

* ==可以配置传入JoinPoint==获取目标对象和目标方法相关信息，==但必须处在参数列表第一位==
* 另外，还==可以配置参数==，让异常通知==接收到目标方法抛出的异常对象==

![](https://note.youdao.com/yws/api/personal/file/74BB9B85457B4B138A009C609794F1F6?method=download&shareKey=d4d94f0f2264bb23b85af8f60e9400c3)

#### 5.最终通知

==是在目标方法执行之后执行的通知。==和后置通知不同之处在于，后置通知是在方法正常返回后执行的通知，如果方法没有正常返回-例如抛出异常，则后置通知不会执行。而最终通知==无论如何都会在目标方法调用过后执行，即使目标方法没有正常的执行完成。==另外，后置通知可以通过配置得到==返回值==，而==最终通知无法得到==。

配置方式

```
<aop:after method="after" pointcut-ref="p1"></aop:after>
```

```Java
public void after(){
        System.out.println("after....");
    }
```

最终通知也==可以额外接收一个JoinPoint参数==，来获取目标对象和目标方法相关信息，但一定要保证==必须是第一个参数==。

```Java
public void after(JoinPoint jp){
        Object target = jp.getTarget();
        MethodSignature signature = (MethodSignature) jp.getSignature();
        Method method = signature.getMethod();
        System.out.println("after...."+method+target);
    }
```

## 六、五种通知的执行顺序

#### 1.在目标对象没有抛出异常时

```
前置通知*
*环绕通知的调用目标方法之前的代码*//取决于配置顺序
目标方法
*环绕通知的调用目标方法之后的代码*
*后置通知
*最终通知//取决于配置顺序
```

#### 2.在目标方法抛出异常的情况下

```
*前置通知*
*环绕通知的调用目标方法之前的代码*//取决于配置顺序
目标方法 抛出异常 
*异常通知*
*最终通知*//取决于配置顺序
```

#### 3.如果存在多个切面

多切面执行时，采用了责任链设计模式。

切面的配置顺序决定了切面的执行顺序，多个切面执行的过程，类似于方法调用的过程，在环绕通知的proceed()执行时，去执行下一个切面或如果没有下一个切面执行目标方法，从而达成了如下的执行过程：

目标方法没有异常时：

![](https://note.youdao.com/yws/api/personal/file/ECD2CBAE49504306B31863DCDF450A36?method=download&shareKey=5746959f590a58a5d85a6d6eebbb8b09)

目标方法抛出异常时：

![](https://note.youdao.com/yws/api/personal/file/3498257CC40546D99C41B928A1F78BC9?method=download&shareKey=8ab94992f656e2a842e6c18f3f878b83)

#### 4.五种异常的常见使用场景

| 前置通知 | 记录日志(方法将被调用)               |
| -------- | ------------------------------------ |
| 环绕通知 | 控制事务 权限控制                    |
| 后置通知 | 记录日志(方法已经成功调用)           |
| 异常通知 | 异常处理 控制事务                    |
| 最终通知 | 记录日志(方法已经调用，但不一定成功) |

## 七、Spring AOP的原理

Spring在创建bean时，除了创建目标对象bean之外，会根据aop的配置，生成目标对象的代理对象，将其存储，之后获取bean时得到的其实是代理对象，在代理对象中，根据配置的切入点规则，决定哪些方法不处理直接执行目标方法，哪些方法拦截后进行增强，需要增强的方法拦截后根据配置调用指定切面中的指定通知执行增强操作。

Spring自动为目标对象生成代理对象，默认情况下，如果目标对象实现过接口，则采用java的动态代理机制，如果目标对象没有实现过接口，则采用cglib动态代理。

开发者可以可以在spring中进行配置，要求无论目标对象是否实现过接口，都强制使用cglib动态代理。

![](https://note.youdao.com/yws/api/personal/file/2D98D53AF4E641E0BE0B0EFA1B292D1C?method=download&shareKey=2e79b16056cc384000b8f100f937590f)

## 八、AOP的注解方式实现

spring也支持注解方式实现AOP，相对于配置文件方式，注解配置更加的轻量级，配置、修改更加方便，是目前最流行的方式。

步骤：

1. 开启AOP注解配置方式

   ```xml
   <!--aop注解配置-->
   <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
   ```

2. 将指定的类标志为一个切面

   ```Java
   @Component
   @Aspect
   public class FirstAspect {
       public void around(){
           
       }
   }
   ```

3. 配置通知 制定切入点规则

   | 前置通知 | @Before         |
   | -------- | --------------- |
   | 环绕通知 | @Around         |
   | 后置通知 | @AfterReturning |
   | 异常通知 | @AfterThrowing  |
   | 最终通知 | @After          |

   ```java 
   @Around("execution(void  service.UserServiceImpl.add*(..))")
       public Object around(ProceedingJoinPoint pjp) throws Throwable {
           System.out.println("a.....");
           Object proceed = pjp.proceed();
           System.out.println("b....");
           return proceed;
       }
   ```

   ![](https://note.youdao.com/yws/api/personal/file/562373E06E594B76A06504349FF0F596?method=download&shareKey=a3cc539fe2a0701260f5452f728e3671)

4. 如果一个切面中多个通知重复使用同一个切入点表达式，则可以将该切入点表达式单独定义，后续引用，注意，在当前切面中通过注解定义的切入点只在当前切面中起作用，其他切面看不到。

   ```Java
    @Pointcut("execution(void  service.UserServiceImpl.add*(..))")
       public void mx(){}
       //引用前边的mx方法，就可以引用他的切点表达式，可以多出引用
       @Around("mx()")
       public Object around(ProceedingJoinPoint pjp) throws Throwable {
           System.out.println("a.....");
           Object proceed = pjp.proceed();
           System.out.println("b....");
           return proceed;
       }
   ```

5. 在后置通知的注解中，也可以额外配置一个returning属性，来指定一个参数名接受目标方法执行后的返回值

   ```java 
    @AfterReturning(value="mx()",returning = "str")
    public void afterReturning(String str ){
           System.out.println("afterReturning...."+str);
     }
   ```

6. 在异常通知的注解中，也可以额外配置一个throwing属性，来指定一个参数名接受目标方法抛出的异常对象

   ```Java
   @AfterThrowing(value="mx()",throwing = "e")
       public void afterThrowing(Throwable e){
           System.out.println("afterThrow...."+e);
       }
   ```

## 九、案例

#### 1.异常信息收集

在业务方法执行时，如果有异常抛出，则根据异常信息记录日志

切面类：

```java 
package cn.tedu.aspect;

import org.apache.log4j.Logger;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class ExceptionAspect {
	private Logger logger = Logger.getLogger(ExceptionAspect.class);
	
	@AfterThrowing(value="execution(* cn.tedu.service..*.*(..))",throwing="e")
	public void afterThrowing(JoinPoint jp,Throwable e) {
		//获取目标对象
		Object obj = jp.getTarget();
		//获取方法信息
		MethodSignature signature = (MethodSignature) jp.getSignature();
		//记录错误日志
		logger.error("在访问["+obj+"]的["+signature.getMethod().getName()+"]方法时抛出了异常["+e.getMessage()+"]");
	}
}

```

service层：

```Java
package cn.tedu.service;

public interface UserService {
    public void addUser();
}

```

```Java
package cn.tedu.service;

import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("增加用户");
        int i = 1/0;
    }
}

```

测试类

```Java
package cn.tedu.test;

import cn.tedu.web.UserServlet;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test01 {
    @Test
    public void test01(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserServlet userServlet = context.getBean(UserServlet.class);
        userServlet.addUser();
        ((ClassPathXmlApplicationContext)context).close();
    }
}

```

web层：

```Java
package cn.tedu.web;

import cn.tedu.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class UserServlet {

    @Autowired
    private UserService userService = null;

    public void addUser(){
        userService.addUser();
    }
}

```

log4j日志配置文件

```
###设置###
log4j.rootLogger=debug,stdout
###输出信息到控制抬###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=[%p] %d{yyyy-MM-ddHH:mm:ss,SSS} method:%l %m %n
###输出DEBUG级别以上的日志到=E://logs/error.log###
log4j.appender.D=org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File=D://logs/debug.log
log4j.appender.D.Append=true
log4j.appender.D.Threshold=DEBUG
log4j.appender.D.layout=org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern=[%p] %d{yyyy-MM-ddHH:mm:ss,SSS} method:%l %m %n
###输出ERROR级别以上的日志到=E://logs/error.log###
log4j.appender.E=org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File=D://logs/error.log
log4j.appender.E.Append=true
log4j.appender.E.Threshold=ERROR
log4j.appender.E.layout=org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern=[%p] %d{yyyy-MM-ddHH:mm:ss,SSS} method:%l %m %n
```

配置文件applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

-<beans xsi:schemaLocation=" http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd " xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context" xmlns="http://www.springframework.org/schema/beans">

	<!--配置包扫描-->
	<context:component-scan base-package="cn.tedu"/>
	<!--配置注解方式DI-->
	<context:annotation-config/>
	<!--注解方式AOP-->
	<aop:aspectj-autoproxy/>
</beans>
```

#### 2.统计业务执行的时间

统计所有业务方法执行时的耗时

配置文件applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xsi:schemaLocation=" http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd " xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context" xmlns="http://www.springframework.org/schema/beans">
	<!--配置包扫描-->
	<context:component-scan base-package="cn.tedu"/>
	<!--配置注解方式DI-->
	<context:annotation-config/>
	<!--注解方式AOP-->
	<aop:aspectj-autoproxy/>
</beans>
```

切面类

```Java
package cn.tedu.aspect;

import org.apache.log4j.Logger;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

@Component
@Aspect
public class UseTimeAspect {

    private static Logger logger = Logger.getLogger(UseTimeAspect.class);

    @Around("execution(* cn.tedu.service..*(..))")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        //开始时间
        long begin = System.currentTimeMillis();
        //--调用目标方法
        Object retObj = pjp.proceed();
        //结束时间
        long end = System.currentTimeMillis();

        //用时 = 结束时间 - 开始时间
        long useTime = end - begin;

        //记录日志
        Class clz = pjp.getTarget().getClass();
        MethodSignature signature = (MethodSignature) pjp.getSignature();
        Method method = signature.getMethod();
        logger.debug("["+clz+"]类中["+method+"]方法执行耗时["+useTime+"]毫秒");

        //--返回返回值
        return retObj;
    }
}

```

service层

```Java
package cn.tedu.service;

public interface UserService {
    public void addUser();
}

```

```Java
package cn.tedu.service;

import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("增加用户");
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

测试类 

```Java
package cn.tedu.test;

import cn.tedu.web.UserServlet;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test01 {
    @Test
    public void test01(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserServlet userServlet = context.getBean(UserServlet.class);
        userServlet.addUser();
        ((ClassPathXmlApplicationContext)context).close();
    }
}

```

web层：

```Java
package cn.tedu.web;

import cn.tedu.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class UserServlet {

    @Autowired
    private UserService userService = null;

    public void addUser(){
        userService.addUser();
    }
}

```

#### 3.通过aop进行权限控制

通过aop来实现权限控制

1. 通过自定义注解声明业务方法是否需要权限控制
2. 通过权限注解上的属性声明需要什么样的权限
3. 通过切面拦截业务方法，根据是否需要权限、是否具有权限，控制目标方法的执行

设计注解代表客户类型

```Java
package cn.tedu.anno;

import cn.tedu.enums.PrivEnum;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface PrivAnno {
    public PrivEnum[] value();
}

```

切面类

```Java
package cn.tedu.aspect;
import cn.tedu.anno.PrivAnno;
import cn.tedu.enums.PrivEnum;
import cn.tedu.web.UserServlet;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;
import java.util.Arrays;
@Component
@Aspect
public class PrivAspect {
    @Around("execution(* cn.tedu.service..*(..)) && @annotation(pa)")
    public Object around(ProceedingJoinPoint pjp, PrivAnno pa) throws Throwable {
        //当前方法可以接受的角色数组
        PrivEnum[] privs = pa.value();
        //当前用户角色
        PrivEnum userPriv = UserServlet.role;
        //检查当前用户角色是否在可接受角色数组中存在
        if(Arrays.asList(privs).contains(userPriv)){
            //有，权限检查通过，直接放行
            Object retObj = pjp.proceed();
            return retObj;
        }else{
            //每有，权限检查不通过，抛出异常
            throw new RuntimeException("权限不足！");
        }
    }


//    @Around("execution(* cn.tedu.service..*(..))")
//    public Object around(ProceedingJoinPoint pjp) throws Throwable {
//        //获取目标方法
//        MethodSignature signature = (MethodSignature) pjp.getSignature();
//        Method intf_method = signature.getMethod();
//        Class<?> clz = pjp.getTarget().getClass();
//        Method inst_method = clz.getMethod(intf_method.getName(),intf_method.getParameterTypes());
//        //检查目标方法是否有@PrivAnno
//        if(inst_method.isAnnotationPresent(PrivAnno.class)){
//            //有注解 - 获取当前注解
//            PrivAnno privAnno = inst_method.getAnnotation(PrivAnno.class);// @PrivAnno({PrivEnum.USER,PrivEnum.ADMIN})
//            //当前方法可以接受的角色数组
//            PrivEnum[] privs = privAnno.value();//{PrivEnum.USER,PrivEnum.ADMIN}
//            //当前用户角色
//            PrivEnum userPriv = UserServlet.role;//PrivEnum.VISITOR
//            //检查当前用户角色是否在可接受角色数组中存在
//            if(Arrays.asList(privs).contains(userPriv)){
//                //有，权限检查通过，直接放行
//                Object retObj = pjp.proceed();
//                return retObj;
//            }else{
//                //每有，权限检查不通过，抛出异常
//                throw new RuntimeException("权限不足！");
//            }
//        }else{
//            //没注解 - 不需要权限，直接放行
//            Object retObj = pjp.proceed();
//            return retObj;
//        }
//   }
}

```

创建枚举类，代表客户类型

```Java
package cn.tedu.enums;
public enum PrivEnum {
    VISITOR,USER,ADMIN
}
```

service层：

```Java
package cn.tedu.service;

public interface UserService {
    public void addUser();
    public void updateUser();
    public void queryUser();
    public void delUser();
}
```

```Java
package cn.tedu.service;

import cn.tedu.anno.PrivAnno;
import cn.tedu.enums.PrivEnum;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {

    @Override
    public void addUser() {
        System.out.println("增加用户");
    }

    @PrivAnno({PrivEnum.USER,PrivEnum.ADMIN})
    @Override
    public void updateUser() {
        System.out.println("修改用户");
    }

    @PrivAnno({PrivEnum.USER,PrivEnum.ADMIN})
    @Override
    public void queryUser() {
        System.out.println("查询用户");
    }

    @PrivAnno(PrivEnum.ADMIN)
    @Override
    public void delUser() {
        System.out.println("删除用户");
    }
}

```

测试类

```Java
package cn.tedu.test;

import cn.tedu.web.UserServlet;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test01 {
    @Test
    public void test01(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserServlet userServlet = context.getBean(UserServlet.class);
        userServlet.updateUser();
        ((ClassPathXmlApplicationContext)context).close();
    }
}

```

web层

```Java
package cn.tedu.web;

import cn.tedu.enums.PrivEnum;
import cn.tedu.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class UserServlet {

    public static PrivEnum role = PrivEnum.USER;

    @Autowired
    private UserService userService = null;

    public void addUser(){
        userService.addUser();
    }
    public void updateUser(){
        userService.updateUser();
    }
    public void queryUser(){
        userService.queryUser();
    }
    public void delUser(){
        userService.delUser();
    }
}

```

#### 4.实现事务控制

通过AOP实现事务控制

1. 开发事务注解，通过业务方法上是否有注解来标识方法是否需要事务
2. 在切面中通过判断目标方法是否具有事务注解决定是否执行事务
3. 通过事务管理器管理事务防止耦合

开发注解：

```Java
package cn.tedu.anno;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Trans {
}

```

切面类：

```Java
package cn.tedu.aspect;

import cn.tedu.anno.Trans;
import cn.tedu.utils.TransactionManager;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class TransAspect {
    @Around("execution(* cn.tedu.service..*(..)) && @annotation(ts)")
    public Object around(ProceedingJoinPoint pjp, Trans ts) throws Throwable {
        try {
            TransactionManager.startTran();
            Object retObj = pjp.proceed();
            TransactionManager.commitTran();
            return retObj;
        } catch (Exception e) {
            TransactionManager.rollbackTran();
            e.printStackTrace();
            throw new RuntimeException(e);
        } finally {
            TransactionManager.relase();
        }
    }
}

```

dao层

```Java
package cn.tedu.dao;

import cn.tedu.domain.User;

public interface UserDao {
    void addUser(User user);
    User queryUser(int id);
}

```

```Java
package cn.tedu.dao;

import cn.tedu.domain.User;
import cn.tedu.utils.JDBCUtils;
import cn.tedu.utils.TransactionManager;
import org.springframework.stereotype.Repository;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
    create database ssmdb;
    use ssmdb;
    create table user(id int primary key auto_increment,name varchar(20),age int);
    insert into user values (null,'aaa',19);
    insert into user values (null,'bbb',26);
    insert into user values (null,'ccc',22);
 */

@Repository
public class UserDaoImpl implements UserDao{
    @Override
    public void addUser(User user) {
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = TransactionManager.getConn();
            ps = conn.prepareStatement("insert into user values (null,?,?)");
            ps.setString(1, user.getName());
            ps.setInt(2,user.getAge());
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        } finally {
            JDBCUtils.close(rs,ps,null);
        }
    }

    @Override
    public User queryUser(int id) {
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            conn = JDBCUtils.getConn();
            ps = conn.prepareStatement("select * from user where id = ?");
            ps.setInt(1,id);
            rs = ps.executeQuery();
            if(rs.next()){
                User user = new User();
                user.setId(rs.getInt("id"));
                user.setName(rs.getString("name"));
                user.setAge(rs.getInt("age"));
                return user;
            }else{
                return null;
            }
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        } finally {
            JDBCUtils.close(rs,ps,conn);
        }
    }
}

```

user类

```Java
package cn.tedu.domain;

public class User {
    private int id;
    private String name;
    private int age;

    public User() {
    }

    public User(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

service层

```Java
package cn.tedu.service;

import cn.tedu.domain.User;

public interface UserService {
    public void addUser(User user);
    public User queryUser(int id);
}

```

```Java
package cn.tedu.service;

import cn.tedu.anno.Trans;
import cn.tedu.dao.UserDao;
import cn.tedu.domain.User;
import cn.tedu.utils.TransactionManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDao userDao = null;

    @Trans
    @Override
    public void addUser(User user) {
        userDao.addUser(user);
        int i = 1/0;
    }

    @Override
    public User queryUser(int id) {
        User user = userDao.queryUser(id);
        return user;
    }
}
```

JDBCUtil

```java 
package cn.tedu.utils;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import javax.sql.DataSource;
import java.sql.*;

public class JDBCUtils {
    private static DataSource dataSource = new ComboPooledDataSource();

    private JDBCUtils() {
    }

    public static Connection getConn() {
        try {
            return dataSource.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }

    public static void close(ResultSet rs, Statement stat, Connection conn){
        if(rs!=null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
                throw new RuntimeException(e);
            }finally {
                rs = null;
            }
        }
        if(stat!=null){
            try {
                stat.close();
            } catch (SQLException e) {
                e.printStackTrace();
                throw new RuntimeException(e);
            }finally {
                stat = null;
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
                throw new RuntimeException(e);
            }finally {
                conn = null;
            }
        }
    }
}

```

管理事务

```Java
package cn.tedu.utils;

import java.sql.Connection;
import java.sql.SQLException;

public class TransactionManager {

    //private static Connection conn = JDBCUtils.getConn();
    private static ThreadLocal<Connection> tl = new ThreadLocal<Connection>(){
        @Override
        protected Connection initialValue() {
            return JDBCUtils.getConn();
        }
    };

    /**
     * 开事务
     */
    public static void startTran(){
        try {
            tl.get().setAutoCommit(false);
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }

    /**
     * 提交事务
     */
    public static void commitTran(){
        try {
            tl.get().commit();
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }


    /**
     * 回滚事务
     */
    public static void rollbackTran(){
        try {
            tl.get().rollback();
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }

    /**
     * 获取连接
     * @return
     */
    public static Connection getConn(){
        return tl.get();
    }

    /**
     * 释放资源
     */
    public static void relase(){
        try {
            tl.get().close();
            tl.remove();
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }

}

```

web层

```Java
package cn.tedu.web;

import cn.tedu.domain.User;
import cn.tedu.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class UserServlet {

       @Autowired
    private UserService userService = null;

    public void addUser(User user){
        userService.addUser(user);
    }

    public User queryUser(int id){
        return userService.queryUser(id);
    }
}

```

测试类

```Java
package cn.tedu.test;

import cn.tedu.domain.User;
import cn.tedu.web.UserServlet;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test01 {
    @Test
    public void test02(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserServlet userServlet = context.getBean(UserServlet.class);
        User user = userServlet.queryUser(2);
        System.out.println(user);
        ((ClassPathXmlApplicationContext)context).close();
    }
    @Test
    public void test01(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserServlet userServlet = context.getBean(UserServlet.class);
        userServlet.addUser(new User(0,"ddd",28));
        ((ClassPathXmlApplicationContext)context).close();
    }
}

```

ThreadLocal:本地线程   ---线程本地变量 --本身是一种参数传递的机制 

![](https://note.youdao.com/yws/api/personal/file/26549254A9954E3C9FDE7124F356BA1E?method=download&shareKey=b0c12921bd571903615898d9d819462a)



## 十、Spring整合JDBC

#### 1.回顾 JDBC

* Java操作关系型数据的API

  导入相关数据库的驱动包后可以通过JDBC提供的接口来操作数据库。

* 实现JDBC的六个步骤

  注册数据库驱动

  获取数据库连接

  获取传输器对象

  传输sql执行获取结果集对象

  遍历结果集获取信息

  关闭资源

  ```java 
  package cn.tedu.jdbc;
  
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  
  
  /**
  	create database springdb;
  	use springdb;
  	create table stu(id int primary key auto_increment,name varchar(255),addr varchar(255));
  	insert into stu values (null,'aaa','bj'),(null,'bbb','sh'),(null,'ccc','gz'),(null,'ddd','sh'),(null,'eee','bj');
   */
  public class Demo01 {
  	public static void main(String[] args){
  		Connection conn = null;
  		PreparedStatement ps = null;
  		ResultSet rs = null;
  		try {
  			//1.注册数据库驱动
  			Class.forName("com.mysql.jdbc.Driver");
  			//2.获取数据库连接
  			conn = DriverManager.getConnection("jdbc:mysql:///springdb","root","root");
  			//3.获取传输器
  			ps = conn.prepareStatement("select * from stu where id < ?");
  			ps.setInt(1, 4);
  			//4.执行sql获取结果集
  			rs = ps.executeQuery();
  			//5.遍历结果集获取结果
  			while(rs.next()){
  				String name = rs.getString("name");
  				System.out.println(name);
  			}
  		} catch (Exception e) {
  			e.printStackTrace();
  		} finally {
  			//6.关闭资源
  			if(rs!=null){
  				try {
  					rs.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				} finally{
  					rs = null;
  				}
  			}
  			if(ps!=null){
  				try {
  					ps.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				} finally{
  					ps = null;
  				}
  			}
  			if(conn!=null){
  				try {
  					conn.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				} finally{
  					conn = null;
  				}
  			}
  		}
  		
  	}
  }
  
  ```

* 数据库连接池(数据源)

  C3P0连接池

  ```java 
  /**
  	create database springdb;
  	use springdb;
  	create table stu(id int primary key auto_increment,name varchar(255),addr varchar(255));
  	insert into stu values (null,'aaa','bj'),(null,'bbb','sh'),(null,'ccc','gz'),(null,'ddd','sh'),(null,'eee','bj');
   */
  public class Demo02 {
  	private static ComboPooledDataSource dataSource = new ComboPooledDataSource();
  	static{
  		try {
  			dataSource.setDriverClass("com.mysql.jdbc.Driver");
  			dataSource.setJdbcUrl("jdbc:mysql:///springdb");
  			dataSource.setUser("root");
  			dataSource.setPassword("root");
  		} catch (PropertyVetoException e) {
  			e.printStackTrace();
  			throw new RuntimeException(e);
  		}
  	}
  	
  	public static void main(String[] args){
  		Connection conn = null;
  		PreparedStatement ps = null;
  		ResultSet rs = null;
  		try {
  			//1.2.从数据源中获取连接
  			conn = dataSource.getConnection();
  			//3.获取传输器
  			ps = conn.prepareStatement("select * from stu where id < ?");
  			ps.setInt(1, 4);
  			//4.执行sql获取结果集
  			rs = ps.executeQuery();
  			//5.遍历结果集获取结果
  			while(rs.next()){
  				String name = rs.getString("name");
  				System.out.println(name);
  			}
  		} catch (Exception e) {
  			e.printStackTrace();
  		} finally {
  			//6.关闭资源
  			if(rs!=null){
  				try {
  					rs.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				} finally{
  					rs = null;
  				}
  			}
  			if(ps!=null){
  				try {
  					ps.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				} finally{
  					ps = null;
  				}
  			}
  			if(conn!=null){
  				try {
  					conn.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				} finally{
  					conn = null;
  				}
  			}
  		}
  		
  	}
  }
  
  ```


#### 2.Spring整合JDBC-管理数据源

1. 导入开发包

   ![](https://note.youdao.com/yws/api/personal/file/43A3F295E26A4F39B49561FFE82FC71C?method=download&shareKey=955857da8761cf2775bdf26be6d88c35)

2. 将数据源交与Spring管理

   ```xml
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
           <property name="jdbcUrl" value="jdbc:mysql:///ssm"></property>
           <property name="user" value="root"></property>
           <property name="password" value="123456"></property>
       </bean>
   ```

3. 通过spring获取数据源，获取连接，操作数据库

   ```Java
   package cn.tedu;
   
   import java.sql.Connection;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.SQLException;
   
   import javax.sql.DataSource;
   
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   /**
    * 通过spring管理数据源
    */
   public class Demo01 {
   	public static void main(String[] args) {
   		ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
   		DataSource dataSource = (DataSource) context.getBean("dataSource");
   		
   		Connection conn = null;
   		PreparedStatement ps = null;
   		ResultSet rs = null;
   		try {
   			conn = dataSource.getConnection();
   			ps = conn.prepareStatement("select * from user where id = ?");
   			ps.setInt(1, 2);
   			rs = ps.executeQuery();
   			while(rs.next()) {
   				String name = rs.getString("name");
   				System.out.println(name);
   			}
   		} catch (SQLException e) {
   			e.printStackTrace();
   		}finally {
   			if(rs!=null) {
   				try {
   					rs.close();
   				} catch (SQLException e) {
   					e.printStackTrace();
   				} finally {
   					rs = null;
   				}
   			}
   			if(ps!=null) {
   				try {
   					ps.close();
   				} catch (SQLException e) {
   					e.printStackTrace();
   				} finally {
   					ps = null;
   				}
   			}
   			if(conn!=null) {
   				try {
   					conn.close();
   				} catch (SQLException e) {
   					e.printStackTrace();
   				} finally {
   					conn = null;
   				}
   			}
   		}
   	}
   }
   
   ```

#### 3.Spring整合JDBC - JDBC模板类

使用模版类能够极大的简化原有JDBC的编程过程。

1. 在Spring中配置JDBC模板类

   ```xml
   <!--配置数据源-->
       <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
           <property name="jdbcUrl" value="jdbc:mysql:///ssm"></property>
           <property name="user" value="root"></property>
           <property name="password" value="123456"></property>
       </bean>
       <!--配置JDBC模板类-->
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <property name="dataSource" ref="dataSource"></property>
       </bean>
   ```

2. 使用JDBC实现增删改查

   ```Java
    private ApplicationContext context = null;
       private JdbcTemplate jdbcTemplate = null;
   
       /*在加载test测试之前执行*/
       @Before
       public void before() {
           context = new ClassPathXmlApplicationContext("applicationContext.xml");
           jdbcTemplate = (JdbcTemplate) context.getBean("jdbcTemplate");
       }
   
       /*在加载test测试之后执行*/
       @After
       public void after() {
           ((ClassPathXmlApplicationContext) context).close();
       }
   ```

   增加：

   ```Java
    @Test
       /*增添数据*/
       public void test01() {
           jdbcTemplate.update("insert into user values(null,?,?) ","abc",15);
       }
   ```

   更改

   ```Java
   @Test
       /*更改数据*/
       public void test02(){
           jdbcTemplate.update("update user set age=14 where id=?",1);
       }
   ```

   删除数据

   ```
   /*删除数据*/
       public void test03(){
           jdbcTemplate.update("delete from user where id=?",2);
       }
   ```

   查询数据

   ```Java
     @Test
       /*查询数据----queryForMap--只能查询结果集唯一的结果*/
       public void test04() {
           Map<String, Object> map = jdbcTemplate.queryForMap("select * from user where id=?", 1);
           System.out.println(map);
       }
   
       @Test
       /*查询数据----queryForList--可以查询多个结果，里边每一个数据是一个map集合*/
       public void test05() {
           List<Map<String, Object>> list = jdbcTemplate.queryForList("select * from user where id<?", 5);
           System.out.println(list);
       }
   
       @Test
       /*查询数据---queryForRowSet类似与返回的结果集的查询。*/
       public void test06() {
           SqlRowSet sqlRowSet = jdbcTemplate.queryForRowSet("select * from user where id<?", 2);
           while (sqlRowSet.next()) {
               User user = new User();
               user.setAge(sqlRowSet.getInt("age"));
               user.setId(sqlRowSet.getInt("id"));
               user.setName(sqlRowSet.getString("name"));
               System.out.println(user);
           }
       }
   ```

3. 使用RowMapper封装bean

   RowMapper接口定义了对象到列的映射关系，可以帮助我们在查询时自动封装bean。

   ```Java
    @Test
       /*查询数据--queryForObject,也只能查询一个结果，通过RowMapper手动添加转换器转换为bean对象*/
       public void test07() {
           User user = jdbcTemplate.queryForObject("select * from user where id=?",
                   new RowMapper<User>() {
   
                       @Override
                       public User mapRow(ResultSet resultSet, int i) throws SQLException {
                           User user = new User();
                           user.setName(resultSet.getString("name"));
                           user.setId(resultSet.getInt("id"));
                           user.setAge(resultSet.getInt("age"));
                           return user;
                       }
                   },
                   3);
           System.out.println(user);
       }
       @Test
       /*查询数据--query可以查询多个结果。通过RowMapper手动添加转换器转换为bean对象*/
       public void test08(){
           List<User> query = jdbcTemplate.query("select * from user where id<?", new RowMapper<User>() {
               @Override
               public User mapRow(ResultSet resultSet, int i) throws SQLException {
                   User user = new User();
                   user.setName(resultSet.getString("name"));
                   user.setId(resultSet.getInt("id"));
                   user.setAge(resultSet.getInt("age"));
                   return user;
               }
           }, 5);
           System.out.println(query);
       }
     
   ```

4. 使用BeanPropertyRowMapper自动进行映射

   BeanPropertyRowMapper内部可以使用指定类进行反射(内省)来获知类内部的属性信息，自动映射到表的列。

   使用它一定要注意，类的属性名要和对应表的列名必须对应的上，否则属性无法自动映射。

   BeanPropertyRowMapper底层通过反射(内省)来实现，相对于之前自己写的RowMapper效率比较低。

   ```Java
     @Test
       /*查询数据--queryForObject,也只能查询一个结果，通过BeanPropertyRowMapper<User>(User.class)自动转换为bean*/
       public void test09(){
           User user = jdbcTemplate.queryForObject("select * from user where id=?",
                   new BeanPropertyRowMapper<User>(User.class), 3);
           System.out.println(user);
   
       }
       @Test
       /*查询数据--query,可以查询多个结果，通过BeanPropertyRowMapper<User>(User.class)自动转换为bean*/
       public void test10(){
           List<User> query = jdbcTemplate.query("select * from user where id<?", new BeanPropertyRowMapper<User>(User.class), 5);
           System.out.println(query);
       }
   ```

#### 4.Spring整合JDBC--声明式事务处理

在之前的案例中，我们实现了自定义切面管理事务（步骤）：

* 开发事务管理注解
* 开发事务管理切面
* 开发事务管理器TransactionManager
* 在事务管理器中通过ThreadLocal保证每个线程各自使用各自的conn

事实上，Spring JDBC中提供了内置的事务处理机制 ，可以经过简单的配置完成事务管理，这称之为声明式事务处理。

实现步骤：

1. 创建项目。模拟MVC三层架构

2. 在配置文件中导入相关约束--tx

   ```Java
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/aop
          http://www.springframework.org/schema/aop/spring-aop.xsd
          http://www.springframework.org/schema/tx
          http://www.springframework.org/schema/tx/spring-tx.xsd
   ">
       
   </beans>
   ```

3. 配置事务管理器

   ```xml
   <!--配置任务管理器-->
       <bean id="transactionManager" 
             class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="dataSource"></property>
       </bean>
   ```

4. 配置事务切面

   ```
   advice-ref:引用那个通知
   ```

   ```xml
   <!--配置事务切面-->
       <aop:config>
           <aop:pointcut id="p1" expression="execution(* service.UserServiceImpl.*(..))"/>
           <aop:advisor advice-ref="txAdvice" pointcut-ref="p1"></aop:advisor>
       </aop:config>
   ```

5. 配置事务通知

   ```xml
   <!--配置事务通知-->
       <tx:advice id="txAdvice" transaction-manager="transactionManager">
           <tx:attributes>
               <!--REQUIRED代表开启事务-->
               <tx:method name="add*" propagation="REQUIRED"/>
               <tx:method name="del*" propagation="REQUIRED"/>
               <!--NEVER代表不使用事务-->
               <tx:method name="update*" propagation="NEVER"/>
           </tx:attributes>
       </tx:advice>
   ```

6. 配置关系图

   ![](https://note.youdao.com/yws/api/personal/file/A26236180158495FAE77E01FA1028251?method=download&shareKey=1af7c535427b5b014b9acee24fb8e9dc)

7. 事务管理策略

   异常的种类：

   java.lang.Throwable

   ​	|-Exception

   ​		|-Runtime Exception

   ​		|-其他Exception

   ​	|-Error

   SpringJDBC内置的事务策略，只在底层抛出的异常是运行时异常时，才会回滚，其他异常不回滚，留给用户手动处理。

   也可以在配置中指定在原有规则的基础上，哪些异常额外的回滚或不回滚：

   ```xml
    <!--REQUIRED代表开启事务-->
               <tx:method name="add*" propagation="REQUIRED" 
                          rollback-for="java.io.IOException"
                          no-rollback-for="java.lang.ArithmeticException"/>
   ```

   配置成如下形式，可以实现任意异常都自动回滚：

   ```xml
   <tx:method name="del*" propagation="REQUIRED"
                          rollback-for="java.lang.Throwable"/>
   ```

8. 注意：

   如果在一个业务逻辑中，需要有多步不同表的操作，此时应该在service层中完成对不同表的操作，以此保证多步操作处在同一个事务中，切勿将业务代码在web层中调用不同Service来实现，虽然正常情况下也可以完成功能，但一旦出问题，很可能只有部分操作被回滚，数据出问题。

   出现这种问题，不是事务管理机制的问题，而是开发者将业务逻辑错误的在web层中进行了实现，所以切记，web层只负责和用户交互和业务逻辑的调用，不进行任何业务逻辑的处理，任何业务逻辑都在service层中进行。

#### 5.Spring整合JDBC--注解方式事务处理

1. 开启事务注解配置

   ```xml
    <!--配置任务管理器-->
       <bean id="transactionManager"
             class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="dataSource"></property>
       </bean>
       <!--开启Spring的注解方式配置事务-->
      <tx:annotation-driven/>
   ```

2. 在方法上通过注解开启事务

   即可以标注在接口上，也可以标注在实现类上，理论上应该标注在接口上，实现面向接口编程，但实际开发中为了方便也有人写在实现类上。

   ```java 
    @Transactional
       public void addUser(User user);
   ```

   ```java 
     @Override
       @Transactional
       public void addUser(User user) {
           jt.update("insert  into user values(null,?,?)",user.getName(),user.getAge());
           int i=1/0;
       }
   ```

   也可以在类上使用此接口，此时类中所有方法都会有事务

   ```java 
   @Transactional
   public class UserDaoImpl implements UserDao{
   ```

   当在类上开启了事务后，可以此类的方法中使用如下方式，控制某个方法没有事务

   ```java 
     @Override
       @Transactional(propagation = Propagation.NOT_SUPPORTED)
       public void delUser(int id) {
           jt.update("update user set age=45 where id=?",id);
       }
   ```

   通过注解控制事务时，和配置文件方式控制事务相同的是，默认只有运行时异常会回滚，非运行异常不回滚，此时可以通过如下注解选项额外配置 哪些异常需要回滚，哪些不需要。

   ```java 
     @Override
       @Transactional(rollbackFor = Throwable.class,noRollbackFor = SQLException.class)
       public void addUser(User user) {
           jt.update("insert  into user values(null,?,?)",user.getName(),user.getAge());
           int i=1/0;
       }
   ```

