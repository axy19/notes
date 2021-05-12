[TOC]
# API---线程

## 一、基本概念

进程：cpu执行的任务分成多个小任务

线程：进程分成的多个小任务

进程=火车  线程=车厢  线程在进程之下行进

栈内存只对一个线程共享

==在某个时间，cpu的某个核中只能处理一个进程，一个进程里只能处理一个线程。底层是依赖于线程的轮换才产生多个任务之间的切换。==

多线程的好处：

线程整个工作职责只有与cpu进行交互和与硬件进行交互，如果线程和硬件进行交互时cpu 处于空闲状态，为了提高cpu的利用率，引入多线程（理论上可以提高百分之百）

![](https://note.youdao.com/yws/api/personal/file/FBF74E25C6764251A0C9EE180C44D7B4?method=download&shareKey=7a33c6d4613109e34c55fcedac97ccaa)

java是一门多线程的语言。

![](https://note.youdao.com/yws/api/personal/file/3C6D1D8B7DB246C0ABECF9FAEC2A20EB?method=download&shareKey=e2f5859a9a30e6b9663b8fdea06d7e02)

同步与异步：

- 同步：在某一时刻只能有一个资源被使用（加锁之后）
- 异步：在某一时刻可以被多个线程抢占同一资源  （没加锁之前）      
- ==同步的一定是安全的，不安全的一定是异步==     

## 二、开启多线程

#### 1.继承Thread类

Thread----代表线程的类，lang包

定义一个类继承Thread类，重写run方法（线程的任务描述），调用start方法开启线程。（继承只能单继承灵活性较低）

```java
public class ThreadDemo1 {
    public static void main(String[] args) {
        //创建一个代表线程类的对象
        TDemo1 td=new TDemo1();
        //调用父类thread类里的开启线程方法
        //线程不会回头执行，会接着上次切换过去的结果继续往下执行
        td.start();
//        td.run();
        //主方法执行的内容在底层是由一个主线程进行执行的
        for (int i =100; i >=0 ; i--) {
            System.out.println("main"+i);
        }
    }
}
//表示线程---描述线程中的任务，并且和thread类有关联
class TDemo1 extends  Thread{
    //当开启线程时，线程的任务就是run方法的内容
    @Override
    public void run() {//线程任务的描述
        for (int i = 0; i <100 ; i++) {
            System.out.println("i"+i);
        }
        //让线程休眠
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### 2.实现Runnable接口

实现Runnable接口，重写run方法（线程任务描述），通过Runnable接口的实现类对象来构建Thread类对象再调用start方法开启线程。（常用）

```java
public class RunnableDemo1 {
    public static void main(String[] args) {
        //创建代表线程类的对象
        TDemo2 td=new TDemo2();
        //由runnable类型的对象来构建Thread类对象
        //Thread类和TDemo2属于同类，Thread类的对象由同类对象构建而来---装饰者设计模式
        Thread t=new Thread(td);
        //开启线程
        t.start();
    }
}
//代表线程任务描述的类
class TDemo2 implements Runnable{
//任务描述去重写run方法
    @Override
    public void run() {
        for (int i = 0; i <10 ; i++) {
            System.out.println("i"+i);
        }
    }
}
```

#### 3.实现Callable接口

实现Callable接口，重写call方法（线程任务的描述），有返回值。（现阶段了解即可）

```java
import java.util.concurrent.*;

public class CallableDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //通过线程池返回执行服务器ExecutorService
        ExecutorService e=Executors.newCachedThreadPool();
        Future<Integer> f=e.submit(new CDemo());
        System.out.println(f.get());//20
    }
}
class CDemo implements Callable<Integer>{

    @Override
    public Integer call() throws Exception {
        return 20;
    }
}
```

![](https://note.youdao.com/yws/api/personal/file/14C75C38A76F476D80931D60FD1FED09?method=download&shareKey=43178167f8ed604557bd7fadc6e65cdb)

## 三、案例

#### 1.四个售票员同时卖100张门票。

```java
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;
public class ThreadTest1 {
    public static void main(String[] args) throws IOException {
        //创建Properties文件
        Properties p = new Properties();
        p.load(new FileReader("ticket.properties"));
        String value = p.getProperty("count");
        int count = Integer.parseInt(value);
        //创建代表票的类的对象
        Ticket t5 = new Ticket();
        t5.setTicket(count);
        //创建四个代表线程类的对象--四个售票员
        Seller s1 = new Seller(t5);
        Seller s2 = new Seller(t5);
        Seller s3 = new Seller(t5);
        Seller s4 = new Seller(t5);
        //创建四个线程对象
        Thread t1 = new Thread(s1, "a");
        Thread t2 = new Thread(s2, "b");
        Thread t3 = new Thread(s3, "c");
        Thread t4 = new Thread(s4, "d");
        //开启线程
        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }
}
//代表线程的类---去描述售票员卖票过程（线程的任务）
class Seller implements Runnable {
    //    int ticket=100;基本类型不行
    Ticket t;
    //有参构造--给属性 Ticket t初始化
    public Seller(Ticket t) {
        this.t = t;
    }
    //卖票的过程就是重写run方法
    @Override
    public void run() {
        //循环
        while (true) {//判断条件就是票卖完了
            //同步代码锁---代码区域不会出现抢占问题
            //synchronized (t) {//()代表的是锁对象---表示共享的线程对象的范围
            //synchronized (Seller.class){
            synchronized (Math.class){//String.class
                //判断true循环的条件
                if (t.getTicket() <= 0) {
                    break;
                }
                //票数减1
                t.setTicket(t.getTicket() - 1);
                //输出每次卖票的结果
                // Thread.currentThread().getName()得到现在正在执行的线程然后获得名字
                System.out.println(Thread.currentThread().getName()
                        + "卖了一张票，还剩" + t.getTicket() + "张");
                //休眠
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
//代表票的类
class Ticket {
    private int ticket;//代表票数
    public int getTicket() {
        return ticket;
    }
    public void setTicket(int ticket) {
        this.ticket = ticket;
    }
}
```

#### ==2.生成与消费模型。==

  每次生产的商品数量都要求是一个随机整数，保证每次剩余的商品数量不能超过1000。

  每次消费的商品数量也是一个随机整数，保证每次剩余的商品数量不能小于0。

  让生产和消费交替出现

| 生产 | 消费 | 剩余 |
| ---- | ---- | ---- |
| 400  |      | 400  |
|      | 300  | 100  |
| 500  |      | 600  |

```java
public class NotifyWaitDemo {
    public static void main(String[] args) {
        //创建代表商品类的对象
        Product p=new Product();
        new Thread(new Pruductor(p)).start();
        new Thread(new Pruductor(p)).start();
        new Thread(new Pruductor(p)).start();
        new Thread(new Consumer(p)).start();
        new Thread(new Consumer(p)).start();
        new Thread(new Consumer(p)).start();

    }
}

//代表线程生产的过程
class Pruductor implements Runnable {
    Product p;

    public Pruductor(Product p) {
        this.p = p;
    }

    @Override
    public void run() {
        while (true) {//保证一直可以生产
            synchronized (p) {
                while(p.flag==true)
                //让线程等待
                try {
                    p.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                //最大可以生产商品数量
                int max = 1000 - p.getCount();
                //随机生产的数量
                int count = (int) (Math.random() * (max + 1));
                //设置新的商品数量
                p.setCount(p.getCount() + count);
                //输出生产的结果
                System.out.println("生产了" + count + "个商品，还剩余" + p.getCount() + "个。。。");
                //唤醒线程
                p.notifyAll();
                p.flag=true;

            }
        }
    }
}

//表示线程消费的过程
class Consumer implements Runnable {
    Product p;

    public Consumer(Product p) {
        this.p = p;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (p) {
                while(p.flag==false)
                //让线程等待
                try {
                    p.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                int count = (int) (Math.random() * (p.getCount() + 1));
                p.setCount(p.getCount() - count);
                System.out.println("消费了" + count + "个商品，还剩余" + p.getCount() + "个。。。");
                p.notifyAll();
                p.flag=false;
            }
        }
    }
}
//代表商品
class Product {
    //属性---商品数量
    private int count;
    //标志位i---控制哪个线程对象等待
    boolean flag=true;
    public int getCount() {
        return count;
    }
    public void setCount(int count) {
        this.count = count;
    }
}
```

#### 3. ==等待唤醒机制==

-  根据wait(),notify(),标志位可以去控制线程对象之间的执行轨迹
- 结合锁才能使用
- wait()是等待机制，notify()唤醒，会唤醒正在等待的其他线程
- 当使用多个线程对象时要使用while判断标志位的情况，使用notifyAll唤醒
- notify()是随机唤醒一个，notifyAll()是唤醒全部

#### ==4.wait()和sleep()区别==

- sleep()一定要指定休眠的时间，到点自然醒。如果使用时出现了锁，休眠就会释放线程的cpu的执行权，但是不会释放锁对象。如果没有锁就还是释放执行权。定义在Thread类里的静态方法。
- wait()可以指定等待时间也可以不指定，如果指定时间就到点自然醒，如果没有指定时间就必须需要手动唤醒notify方法。如果使用时出现锁，等待时既会释放执行权也会释放锁对象，不加锁就释放执行权。定义在Object类里的方法。

## ==六、解决线程抢占问题--加锁==

==多线程之间存在相互抢占（抢占的是cpu的执行权），发生在代码的每一行，产生错误问题（重复，跳过，负数）导致多线程数据安全问题==

注意：图中的SOP代表输出语句。

![](https://note.youdao.com/yws/api/personal/file/12AB3CE1224F44009685EC7F825BE4C8?method=download&shareKey=57c724ddc9318d335b020cf4fd606525)

![](https://note.youdao.com/yws/api/personal/file/1E6774CEA2AF4AB98B02177BADC5E9C1?method=download&shareKey=4f0aaf587d6bddb423c77203fb65b62b)

![](https://note.youdao.com/yws/api/personal/file/D451BAADC38C4EA7B6FC0010296CDB0B?method=download&shareKey=6791bdc83ddc3c7c3b1656fb3cd3f015)

#### ==1.同步代码锁==

  synchronized (锁对象){}---锁住的区域不会被线程抢占

   锁对象：

  - 指定线程对象共享的范围，一般需要找都共享的内容（准备试衣服的四个人，外部不试衣服的人不是锁对象）
  
- 如果锁对象属于方法区资源，会锁住所有线程对象（因为指定方法区资源默认共享所有线程对象）
  
- 如果锁对象是this，只能锁住多个线程对象由同一个Runnable接口实现的类对象的构建的对象。
  
    ![](https://note.youdao.com/yws/api/personal/file/12AFD98DF5294ECBB011097FF9A172C1?method=download&shareKey=3a57547b51abcad761feff5547b9ff2f)
  

![](https://note.youdao.com/yws/api/personal/file/32CF273302BB432CAE5458DA010079A0?method=download&shareKey=60bc752f0f8acb2614becd26d825662f)

![](https://note.youdao.com/yws/api/personal/file/67433A0EA2A4463DB1A1011E6A9E7C42?method=download&shareKey=0ef959cbf61ed3926d5dd607808fd1f4)

#### 2.同步方法锁（默认有一个锁对象）

- 如果加载非静态的方法上锁对象就是this

- 如果加在静态方法上锁对象就是当前类.class（方法区资源）

- 将run方法前边加修饰符synchronized

  ```java
  public synchronized void  run() {}
  ```

## 七、线程的死锁

#### 1.死锁的产生

由于锁的嵌套产生死锁问题

![](https://note.youdao.com/yws/api/personal/file/BB3EDC6F48AC46358704C18AF2D6032A?method=download&shareKey=68d7370b270826fe97b044e99b3b4d7e)

#### 2.解决死锁问题的方式

- 尽量避免锁的嵌套
- 设定线程优先级，指定某个线程先执行

```java
public class DeadLockDemo {
    //定义类的对象
    private static Scann s=new Scann();
    private static Printer p=new Printer();
    public static void main(String[] args) {
        //第一个员工 先打印再扫描
        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (p){
                    p.print();
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (s){
                        s.scan();
                    }
                }
            }
        }).start();
        //第二个员工 先扫描再打印
        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (s){
                    s.scan();
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (p){
                        p.print();
                    }
                }
            }
        }).start();

    }
}
//代表扫描的类
class Scann{
    public void scan(){
        System.out.println("扫描。。。。 ");
    }
}
//代表打印的类
class Printer{
    public  void print(){
        System.out.println("打印。。。。");
    }
}                                                                         
```

#### 3.线程的优先级

- 线程的优先级从1-10，级别越大理论上可以抢占的资源概率越大。

- 如果线程之间的优先级之差大于了5，理论上可以抢占的资源概率就会稍微大一点。

- 线程创建出来会有默认的优先级（一般是相等的）

  ```java
  	t1.setPriority(1);
      t2.setPriority(3);
      t3.setPriority(5);//设置优先级
  ```

```java
public class PriorityDemo {
    public static void main(String[] args) {
        Thread t1=new Thread(new PDemo(),"a");
        Thread t2=new Thread(new PDemo(),"b");
        Thread t3=new Thread(new PDemo(),"c");
        t1.setPriority(1);
        t2.setPriority(3);
        t3.setPriority(5);
        t1.start();
        t2.start();
        t3.start();
    }
}
class PDemo implements  Runnable{
    @Override
    public void run() {
        for (int i = 0; i <10 ; i++) {
            System.out.println(Thread.currentThread().getName()+":"+i);
        }
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

## 九、守护线程

1. 被守护线程结束，其他的守护线程随之结束
2. 线程要么是守护线程，要么是被守护线程。
3. 被守护线程可以有多个，只有等到所有的被守护线程结束，其他的守护线程才结束。
4. gc--垃圾回收器是一个很大的线程

## 十、线程的状态

![](https://note.youdao.com/yws/api/personal/file/3CDD5B48EB3D43FD888086AAD7BEA3FD?method=download&shareKey=4dbc90861ab8a158d3e6118e5d4a5220)

##  十一、单例（实例对象）设计模式

1. 只能产生一个对象
2. 实现方式：
   1. 饿汉式---直接创建对象，没有线程安全问题，只要加载类就会创建对象
   2. 懒汉式---在方法中创建对象，需要时创建对象    ，线程安全问题
3. 去了解其他的实现形式

```java
public class SingleDemo {
    public static void main(String[] args) {
        //获取唯一的对象
       TaskManager t= TaskManager.getTm();

    }
}

/*//代表任务管理器的类---只能产生一个对象
//单例设计模式的实现形式---饿汉式  不管需不需要都会创建对象
class TaskManager {
    //私有构造方法
    private TaskManager() {

    }

    //创建本类的实例对象---属性
    private final static TaskManager tm = new TaskManager();

    //返回对象的方法
    public static TaskManager getTm() {
        return tm;
    }
    //
    public static int m(){return 1;}
}*/
//单例模式实现的方式---懒汉式
class TaskManager {
    //私有构造方法
    private TaskManager() {

    }

    //创建本类的实例对象---属性
    private static TaskManager tm=null ;

    //返回对象的方法
    public static TaskManager getTm() {
       //判断tm是否已经进行创建对象
        if(tm==null)//判断对象值是否为null
            tm=new TaskManager();//tm的值还为null，创建一个对象
        return tm;//如果不进判断表明tm已经有地址值，如果进判断就是返回刚创建的地址值
    }
    //
    public static int m(){return 1;}
}
```



































