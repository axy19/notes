[TOC]
# Spring框架

## 一、框架

#### 1.框架的基本概念

程序的架子，形成了程序的基本骨架，便捷解决程序的通用问题，可以用来快速搭建程序，框架通常和具体业务无关，可以套用在不同的项目中。

#### 2.常用框架

* SSH:

   		​		 Struts2  

  ​		 Spring

  ​		Hibernate

* SSM

  ​		SpringMVC

  ​		Spring

  ​        MyBatis

## 二、Spring框架概述

#### 1.基本概念

* Spring是service层的框架，可以整合很多其他框架技术。
* 主要技术：IOC(控制反转，依赖注入)  AOP(面向切面编程)。

#### 2.下载

​	maven下载

​	手工下载：打开Spring官网：https://spring.io，点击PROJECTS

* 提示

  spring推荐使用maven方式使用spring包，因此虽然官方提供了spring离线包的下载方式，但下载过程较为繁琐。

  在后续课程学习maven后我们将会使用maven加载Spring相关包，此节仅供参考。

* 打开Spring官网：https://spring.io，点击PROJECTS

* 点击SPRING FRAMEWORK

* 点击GitHub图标

* 找到Access to Binaries，点击Spring Framework Artifacts

* 点击Spring Artifactory

* 点击libs-release-local

* 点击org/，点击springframework，点击spring

* 就可以选择你想要的版本进行下载

* 解压下载好的压缩包

  其中包含着Spring的依赖包

  dist:包含全部--下载dist的压缩包即可。

## 三、Spring IOC---作用：低耦合

#### 1. IOC概念

所谓的IOC称之为控制反转，简单来说就是将对象的创建的权利及对象的生命周期的管理过程交由Spring容器来处理，从此在开发过程中不再需要关注对象的创建和生命周期的管理，而是在需要时由Spring框架提供，这个由spring框架管理对象创建和生命周期的机制称之为控制反转。而在创建对象的过程中Spring可以依据配置对对象的属性进行设置，这个过称之为依赖注入,也即DI。

==IOC+接口可以实现软件分层时的彻底解耦，最终实现高内聚低耦合中的低耦合==

#### 2.入门案例

* 创建项目

* 导入相关的包6（6个spring的包）+1（其他包）

  ![](https://note.youdao.com/yws/api/personal/file/3D6A2143B45A4CE2AC27F0E1CAE76F12?method=download&shareKey=c640cbabb2ea2326aa77cdcaeaaf0397)

* 创建配置文件---文件名一般为applicationContext.xml（别人都这么写）

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <beans xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://www.springframework.org/schema/beans">
  
  <bean class="domain.Person" id="person"/>
  
  </beans>
  ```

* 创建bean类，并配置到配置文件中。

  ```Java
  package domain;
  
  public class Person {
      public void eat(){
          System.out.println("吃。。。。");
      }
      public void say(){
          System.out.println("说。。。。");
      }
  }
  ```

* 写代码操作bean

  ```Java
  import domain.Person;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class Test01 {
  /*传统方式创建并管理bean*/
    /*  public static void main(String[] args) {
          Person p=new Person();
          p.eat();
          p.say();
          p=null;
      }*/
  /*Spring IOC 方式常见并管理bean*/
      public static void main(String[] args) {
          //1.初始化Spring容器
          ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
          //2.通过Spring容器获取bean操作bean
          Person person = (Person) context.getBean("person");
          person.eat();
          person.say();
          //3.关闭Spring
          ((ClassPathXmlApplicationContext)context).close();
      }
  }
  ```

#### 3. Spring IOC 的原理

在初始化一个Spring容器时，Spring会去解析指定的xml文件，当解析到其中的<bean>标签时，会根据该标签中的class属性指定的类的全路径名，通过反射创建该类的对象，并将该对象存入内置的Map中管理。其中键就是该标签的id值，值就是该对象。

之后，当通过getBean方法来从容器中获取对象时，其实就是根据传入的条件在内置的Map中寻找是否有匹配的键值，如果有则将该键值对中保存的对象返回，如果没有匹配到则抛出异常。

由此可以推测而知：

* 默认情况下，多次获取同一个id的bean，得到的将是同一个对象。
* 不可以配置多个id相同的bean
* 可以配置多个id不同但class相同的bean

#### 4.获取Bean的方式

通过context.getBean()方法获取bean时，可以通过如下两种方式获取：

​	传入id值

​	传入class类型

通过class方式获取bean时，如果同一个类配置过多个bean，则在获取时因为无法确定到底要获取哪个bean会抛出异常。

而id是唯一的，不存在这样的问题，所以建议大家尽量使用id获取bean。

```Java
		/**
		 * 获取对象的方式
		 *  通过id获取bean
		 *      如果找不到，抛异常NoSuchBeanDefinitionException
		 *      如果找到唯一的，返回对象
		 *      因为id不重复，不可能找到多个
		 *  通过class获取bean
		 *      如果找不到,抛出异常NoSuchBeanDefinitionException
		 *      如果找到唯一，返回对象
		 *      如果找到多个，抛出异常NoUniqueBeanDefinitionException
		 */
		@Test
		public void  test04(){
		    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
		
		    //获取对象方式1：通过id获取
		    //Person p = (Person)context.getBean("person");
		    //p.eat();
		    //p.say();
		    //获取对象方式2：通过class获取
		    Person p = context.getBean(Person.class);
		    p.eat();
		    p.say();
		
		    ((ClassPathXmlApplicationContext)context).close();
		}
		 

```

SpringIOC在通过class获取bean时，如果找不到该类型的bean还会去检查是否存在该类型的子孙类型的bean，

思想中的多态的特性。

```Java
@Test
public void test02() {
	//1.初始化spring容器
	ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
	//2.获取Bean
	JavaTeacher jt = (JavaTeacher) context.getBean(Teacher.class);
	System.out.println(jt);
}

```

#### 5.别名标签

在 Spring中提供了别名标签<alias>可以为配置的<bean>起一个别名，要注意的是这仅仅是对指定的<bean>起的一个额外的名字，并不会额外的创建对象存入map。

<alias name="要起别名的bean的id"  alias="要指定的别名"/>

```Java
/**
     * 别名标签
     *  可以通过别名标签为bean的id起一个别名，此后除了可以通过别名指代id
     */
    @Test
    public void test05(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //Person p = (Person) context.getBean("person");
    Person p = (Person) context.getBean("pers");
    System.out.println(p);
    ((ClassPathXmlApplicationContext)context).close();
}
```

#### 6.SpringIOC创建对象的方式

##### 1.通过类的无参构造方法创建对象

在入门案例中使用的就是这种方式。

当用最普通方式配置一个<bean>时,默认就是采用类的无参构造创建对象。

在Spring容器初始化时，通过<bean>上配置的class属性反射得到字节码对象，通过newInstance()创建对象

```
Class c=Class.forName("类的全路径名称");
Object obj=c.newInstance();
```

这种方式下spring创建对象，要求==类必须有无参的构造，否则无法通过反射创建对象，会抛出异常。==

```
public class Person {
    public Person(){
        System.out.println("Person被创建了..");
    }
}
```

```Java
/**
 * SpringIOC创建对象方式1 - 反射创建对象
 *  bean必须有无参构造才可以
 */
@Test
public void test01() throws Exception {
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    Person p = (Person) context.getBean("person");
    System.out.println(p);
    ((ClassPathXmlApplicationContext)context).close();

}
```

##### 2.没有无参构造

通过工厂模式创建对象，

```Java
public class Person {
    public Person(String name) {
        System.out.println(name+"person创建了。。。。");
    }
}
```

###### 2.1 静态工厂创建对象---工厂里的方法是静态方法

* 工厂类

```java 
package factory;

import domain.Person;

public class PersonstaticFactory {
    private PersonsaticFactory(){}
    public static Person getPerson(){
        return new Person("aaa");
    }
}
```

* 配置文件

```xml
<!-- 静态工厂配置-->
 <bean id="person" class="factory.PersonstaticFactory" factory-method="getPerson"></bean>
```

* 测试

```Java
@Test
/*静态工厂*/
public void test01(){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
    Person p= (Person) context.getBean("person");
    System.out.println(p);
    ((ClassPathXmlApplicationContext)context).close();
}
```

###### 2.2 实例工厂创建对象---工厂里的方法不是静态的

* 工厂类

```Java
package factory;

import domain.Person;

public class PersonslFactory {
    public Person getPerson(){
        return new Person("abc");
    }
}
```

* 配置文件

```xml
<!--实例工厂设置-->
<bean id="slperson" class="factory.PersonslFactory"></bean>
<bean id="person" factory-bean="slperson" factory-method="getPerson"></bean>
```

* 测试

```Java
@Test
/*实例工厂*/
public void test02(){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
    Person p= (Person) context.getBean("person");
    System.out.println(p);
    ((ClassPathXmlApplicationContext)context).close();
}
```

###### 2.3 Spring工厂创建对象---Spring提供的

* ==工厂类--必须实现FactoryBean==<Person>

```Java
package factory;

import domain.Person;
import org.springframework.beans.factory.FactoryBean;

public class PersonSpringFactory implements FactoryBean<Person> {
    /*Spring工厂创建对象调用的方法*/
    @Override
    public Person getObject() throws Exception {
        return new Person("as");
    }
    /*获取当前工厂产生的对象的类型的方法*/
    @Override
    public Class<?> getObjectType() {
        return null;
    }

    /*Spring工厂生产对象时是否采用单例模式
    * 如果返回true，则在Spring中该对象只能创建一次后续反复使用
    * 如果返回false，则每次获取该bean都重新创建对象*/
    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

* 配置文件

```xml
<!--Spring工厂配置-->
<bean id="person" class="factory.PersonSpringFactory"></bean>
```

* 测试

```java 
/*spring工厂*/
@Test
public void test03(){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
    Person person = (Person) context.getBean("person");
    System.out.println(person);
    Person person1 = (Person) context.getBean("person");
    System.out.println(person);
    ((ClassPathXmlApplicationContext)context).close();
}
```

###### 2.4.总结

​	三种工厂的作用其实是等价的，用其中一个解决问题即可。

​	反射用的最多。

​	默认情况下创建的对象都是单例的。

#### 7.单例多例

##### 1.基本概念

单例：一个bean只有一个对象，之后无论获取多少次都是同一个对象bean。

多例：每次获取时都重新创建一个新的对象。购物车，账户余额等。

##### 2.生命周期

###### 2.1 单例模式下的生命周期

bean在单例模式下，spring容器启动时解析xml发现该bean标签后，直接创建该bean的对象存入内部map中保存，此后无论调用多少次getBean()获取该bean都是从map中获取该对象返回，一直是一个对象。此对象一直被Spring容器持有，直到容器退出时，随着容器的退出对象被销毁。

```Java
@Test
public void test01(){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
    Person p= (Person) context.getBean("person");
    System.out.println(p);
    Person p1= (Person) context.getBean("person");
    System.out.println(p1);
    ((ClassPathXmlApplicationContext)context).close();
}
```

```
<!--设置是单例还是多例模式-->
<bean id="person" class="domain.Person" scope="singleton"></bean>
```

###### 2.2 多例模式下的生命周期

bean在多例模式下，spring容器启动时解析xml发现该bean标签后，只是将该bean进行管理，并不会创建对象，此后每次使用 getBean()获取该bean时，spring都会重新创建该对象返回，每次都是一个新的对象。这个对象spring容器并不会持有，什么时候销毁取决于使用该对象的用户自己什么时候销毁该对象。

```
@Test
public void test01(){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
    Person p= (Person) context.getBean("person");
    System.out.println(p);
    Person p1= (Person) context.getBean("person");
    System.out.println(p1);
    ((ClassPathXmlApplicationContext)context).close();
}
```

```
<!--设置是单例还是多例模式-->
<bean id="person" class="domain.Person" scope="prototype"></bean>
```

#### 8.懒加载机制

##### 1.概念

Spring默认会在容器初始化的过程中，解析xml，并将单例的bean创建并保存到map中，这样的机制在bean比较少时问题不大，但一旦bean非常多时，spring需要在启动的过程中花费大量的时间来创建bean 花费大量的空间存储bean，但这些bean可能很久都用不上，这种在启动时在时间和空间上的浪费显得非常的不值得。

所以Spring提供了懒加载机制。所谓的懒加载机制就是可以规定指定的bean不在启动时立即创建，而是在后续第一次用到时才创建，从而减轻在启动过程中对时间和内存的消耗。

==懒加载机制只对单例bean有作用，对于多例bean设置懒加载没有意义。==

##### 2.如何设置懒加载机制

lazy-init设置是否为懒加载机制

为true：表示为懒加载机制

为false：表示不为懒加载机制

为default：表示的方式和beans中的default-lazy-init设置的值匹配。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       default-lazy-init="true"
>
    
    <bean id="person" class="domain.Person" lazy-init="true"></bean>
</beans>
```

#### 9.配置初始化和销毁方法

在Spring中如果某个bean在初始化之后 或 销毁之前要做一些 额外操作可以为该bean配置初始化和销毁的方法 ，在这些方法中完成要功能。

```Java
package domain;

public class Person {
    public void init(){
        System.out.println("person 初始化");
    }
    public void test01(){
        System.out.println("person  test...");
    }
    public void destory(){
        System.out.println("person 销毁。。");
    }
}
```

测试

```Java
@Test
public void test01(){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
    Person person = (Person) context.getBean("person");
    ((ClassPathXmlApplicationContext)context).close();
}
```

配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<bean id="person" class="domain.Person" init-method="init" destroy-method="destory"></bean>
</beans>
```

#### 10.Spring中关键方法的执行顺序

在Spring创建bean对象时，先创建对象(通过无参构造或工厂)，之后立即调用init方法来执行初始化操作，之后此bean就可以拿来调用其它普通方法,而在对象销毁之前，spring容器调用其destory方法来执行销毁操作。

#### 11.Spring IOC DI

##### 1.基本概念

在创建对象的过程中Spring可以依据配置对对象的属性进行设置，这个过称之为依赖注入,也即DI。

##### 2.DI实现

类属性和bean属性

类属性就是看的类，所有的成员变量，bean属性是指类的成员变量中有get或者set方法成员变量。

###### 2.1 基于setter注入

通常的javabean属性都会私有化，而对外暴露setXxx()getXxx()方法，此时spring可以通过这样的setXxx()方法将属性的值注入对象。

```Java
package domain;

import java.util.List;
import java.util.Map;
import java.util.Properties;
import java.util.Set;

public class Person {
    private int age;
    private String name;
    private List<String> list;
    private Set<String> set;
    private Map<String,String> map;
    private Properties properties;
    private Cat cat;

    public Cat getCat() {
        return cat;
    }

    public void setCat(Cat cat) {
        this.cat = cat;
    }

    public Properties getProperties() {
        return properties;
    }

    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List<String> getList() {
        return list;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    public Set<String> getSet() {
        return set;
    }

    public void setSet(Set<String> set) {
        this.set = set;
    }

    public Map<String, String> getMap() {
        return map;
    }

    public void setMap(Map<String, String> map) {
        this.map = map;
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", list=" + list +
                ", set=" + set +
                ", map=" + map +
                ", properties=" + properties +
                ", cat=" + cat +
                '}';
    }
}

```

```Java
package domain;

public class Cat {
    private String name;
    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Cat{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

* 基本数据类型和String类型

​		---看的是bean属性即get,set方法后边的名字。getName--->name

配置文件

```xml
<bean id="person" class="domain.Person">
    <property name="name" value="sss"></property>
    <property name="age" value="15"></property>
</bean>
```

* 集合类型，map类型，set类型，Properties类型

```xml
<property name="list">
   <list>
      <value>v1</value>
      <value>v2</value>
      <value>v3</value>
   </list>
</property>
<property name="map">
	 <map>
       <entry key="1" value="1"></entry>
       <entry key="2" value="2"></entry>
       <entry key="3" value="3"></entry>
   </map>
</property>
<property name="set">
   <set>
       <value>s1</value>
       <value>s2</value>
       <value>s3</value>
   </set>
</property>
<property name="properties">
    <props>
       <prop key="p1">v1</prop>
       <prop key="p2">v2</prop>
       <prop key="p3">v3</prop>
    </props>
</property>
```
* 自定义Bean类型的处理

  * 手动装配

    (![img](https://note.youdao.com/yws/api/personal/file/0C37458F182E466282F17CE76D746426?method=download&shareKey=360a2ec8de3491e0bd36b02350dd6caf)
  
  ```xml
  <bean id="cat" class="domain.Cat">
          <property name="age" value="5"></property>
          <property name="name" value="cat2"></property>
  </bean>
  <bean id="person" class="domain.Person">
      <property name="cat" ref="cat"></property>
  </bean>
  ```
  
  * autowire 实现自动装配
  
    在Spring的set方式实现的注入过程中，支持自动装配机制，所谓自动装配机制，会根据要设置的javabean属性的名字或类型到spring中自动寻找对应id或类型的<bean>进行设置，从而省去依次配置的过程，简化了配置。

```
autowire 实现自动装配
	byName
		会自动找和当前bean属性名一样的id的bean进行注入
		找不到就不注入
		找到唯一的就注入
		不可能找到多个
	byType
		会自动找和当前bean属性类型一致的bean进行注入
		找不到就不注入
		找到唯一的就注入
		找到多个抛出异常
```

```xml
<bean id="cat" class="domain.Cat">
        <property name="age" value="5"></property>
        <property name="name" value="cat2"></property>
</bean>
<bean id="person" class="domain.Person" autowire="byName">
</bean>  
```

```java 
/*set方式实现di注入*/
    @Test
    public void test01(){
        ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
        Person p= (Person) context.getBean("person");
        System.out.println(p);
        ((ClassPathXmlApplicationContext)context).close();
    }
```

###### 2.2 基于构造方法的注入

对象属性设置的另一种方式是在对象创建的过程中通过构造方法传入并设置对象的属性。

spring也可以通过这样的构造方法实现属性的注入。

```java 
public class Person {
    private String name;
    private int age;
    private Cat cat;
    public Person() {
    }

    public Person(String name, int age, Cat cat) {
        this.name = name;
        this.age = age;
        this.cat = cat;
    }
    
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", cat=" + cat +
                '}';
    }
}
```

```Java
package domain;

public class Cat {
    private int age;
    private String name;

    public Cat() {
    }

    public Cat(int age, String name) {
        this.age = age;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Cat{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}

```

配置文件

```xml
 <bean id="cat" class="domain.Cat">
        <constructor-arg index="0" value="12" ></constructor-arg>
        <constructor-arg index="1" value="md"></constructor-arg>
    </bean>
    <bean id="person" class="domain.Person">
        <!--
           index 构造器中第几个参数
           name 构造器中叫什么名字的参数
           type 构造器中什么类型的参数
           value 给什么值
           ref 给什么值，通过引用
       -->
        <constructor-arg index="0" value="naa"></constructor-arg>
        <constructor-arg index="1" value="12"></constructor-arg>
        <constructor-arg index="2" ref="cat"></constructor-arg>
    </bean>
```

测试：

```Java
@Test
 public void test01(){
        ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
        Person p= (Person) context.getBean("person");
        System.out.println(p);
        ((ClassPathXmlApplicationContext)context).close();
    }
```

## 四、注解回顾

#### 1.注解概念

注释：给人看的提示信息。人看了提示信息了解程序的内容
			java中注释的格式：//  /**/  /** */

注解：给程序看的提示信息，注解是jdk5.0提供的新特性，可以用来实现轻量化配置，具有广泛的应用场景。

#### 2.sun提供了jdk内置注解

​	@Override：声明重写父类方法的注解，要求编译器帮我们检查是否成功的覆盖，如果没有成功覆盖父类方法，编译器将会进行报错提示。

​	@Deprecated:声明方法被过时，不再建议使用，要求编译器在编译的过程中对于这样的方法的调用提出警告，提示方法过时

​	@SuppressWarnings(""):压制警告，提示编译器，在编译的过程中对指定类型的警告不再提示

#### 3.自定义注解开发

##### 3.1 开发一个注解类

开发一个注解类的过程，非常类似于开发一个接口，只不过需要通过@interface关键字来声明

##### 3.2 使用元注解修饰注解的声明

所谓的元注解是用来修饰注解声明的注解，可以控制被修饰的注解的特性。

* @Target控制注解使用的位置。

  用来声明被修饰的注解可以用在什么位置。

  可以在@Target的属性中设置ElementType类型的数组来指定可以使用的位置。

  如果不用此元注解修饰，默认注解可以用在任意位置

* @Retention用来声明被修饰的注解会被保留到什么阶段。

  可以在该注解的属性中通过RetentionPolicy类型的值来指定注解被保留到何时。		

  RetentionPolicy.SOURCE

  ​	此注解将会被保留到源码阶段.java中，在编译过程中被删除。

  ​	这种类型的注解通常是给编译器看的。

  RetentionPolicy.CLASS

  ​	此注解将会被保留在源码阶段 和 编译阶段 ，.java和.class中，在类加载的过程中被删除。

  ​	这种类型的注解通常是给类加载器看的。

  RetentionPolicy.RUNTIME

  ​	此注解将会被保留在源码阶段 、编译阶段 和 运行阶段，.java .class和内存中的字节码中都会存在。

  ​	这种类型的注解通常用来在运行阶段进行反射，控制程序运行过程。

  ==只有RUNTIME级别的注解才可以通过反射技术进行反射。==	

* @Documented 用来声明被修饰注解是否要被文档提取工具提取到文档中。

  默认不提取

* @Inherited被修饰的注解是否具有继承性

  默认没有继承性

##### 3.3 为注解增加属性

1. 注解类中还可以声明属性。
2. 为注解类声明属性的过程非常类似于为接口定义方法。
3. 要求：注解中的所有的属性==必须是public的==，可以显式声明，也可以不声明，不声明默认就是public的。
4. 注解中的属性==只能==是八种基本数据类型 String类型 Class类型 枚举类型其他注解类型及以上类型的一维数组。
5. 注解中声明的属性需要在使用注解时为其赋值,赋值的方式就是使用注解时在注解后跟一对小括号在其中通过属性名=属性值的方式 指定属性的值
6. 也可以在声明注解时在注解的属性后通过default关键字 声明属性的默认值 声明过默认值的属性 可以在使用注解时不赋值则默认采用默认值也可以手动赋值 覆盖默认值
7. 如果属性是 一维数组类型 在传入的数组中只有一个则包括数组的大括号可以省略
8. 如果注解的属性只有一个需要赋值且该属性的名称叫做value 则在使用注解时 value= 可以不写

##### 3.4 反射注解

###### 3.4.1 反射注解的原理

RetentionPolicy.RUNTIME级别的注解会保留到运行其，可以通过反射技术获取，从而可以根据是否有注解 或 注解属性值的不同控制程序按照不同方式运行。

以下反射相关的类型中都提供了反射注解的方法：

类 Class<T>、类 Method、类 Field、类 Constructor<T>、类 Package

| boolean                                                      | [**isAnnotationPresent**](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/Class.html#isAnnotationPresent(java.lang.Class))([Class](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/Class.html)<? extends [Annotation](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/annotation/Annotation.html)> annotationClass)        如果指定类型的注释存在于此元素上，则返回  true，否则返回 false。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <A extends [Annotation](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/annotation/Annotation.html)>    A | [**getAnnotation**](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/Class.html#getAnnotation(java.lang.Class))([Class](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/Class.html)<A> annotationClass)        如果存在该元素的指定类型的注释，则返回这些注释，否则返回  null。 |
| [Annotation](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/annotation/Annotation.html)[] | [**getAnnotations**](mk:@MSITStore:C:\Users\Public\Desktop\开班软件\JDK_API_1_6_zh_CN.CHM::/java/lang/Class.html#getAnnotations())()        返回此元素上存在的所有注释。 |

###### 3.4.2 反射注解案例

```
package cn.tedu.test;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface level{
	String value();
}

@level("刑警")
class Police{
}

public class AnnoTest02 {
	public static void main(String[] args) {
		System.out.println("敬了个礼，您好，您超速了，罚款200元。。。");
		if(Police.class.isAnnotationPresent(level.class)){
			level anno = Police.class.getAnnotation(level.class);
			if("协警".equals(anno.value())){
				System.out.println("哥们少罚点，50块得了~~");
			}else if("交警".equals(anno.value())){
				System.out.println("哥们抽根烟，这是200块，收好我走人~~");
			}else if("刑警".equals(anno.value())){
				System.out.println("赶紧交钱走人，别查出 别的事。。。");
			}else{
				System.out.println("xxx");
			}
		}else{
			System.out.println("打一顿，扭送警察局。。");
		}
	}
}

```

#### 4.主要作用

* 实现轻量化配置，配置发生在代码当中

* 配置

  配置文件方式

  注解方式

## 五、注解实现 Spring IOC

Spring除了默认的使用xml配置文件的方式实现配置之外，也支持使用注解方式实现配置，这种方式效率更高，配置信息清晰，代码在哪对应的配置就在哪，方便开发阶段修改，推荐使用。

所谓注解就是给程序看的提示信息，很多时候都用来作为轻量级配置的方式。

#### 1.反射方式创建bean的实现步骤

1. 导包 7+1

   ![](https://note.youdao.com/yws/api/personal/file/8BF7CEA7D186407AB6C7A07A52A3712F?method=download&shareKey=1a3015a03587f78cbf806c25213d6349)

2. 写配置文件额外引入context约束

   开启包扫描

   在配置文件中，开启包扫描，指定spring自动扫描哪些包下的类。只有在指定的扫描包下的类上的IOC注解才会生效。

   名称空间--仅仅是一个名字

   ![](https://note.youdao.com/yws/api/personal/file/A3FA3AB57A324392B90026A0FC9B4695?method=download&shareKey=fbf3be74e6991345ab26b5dbd969566a)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
   ">
       <!-- 开启包扫描
    		如果有多个可以写开启包扫描的语句
   		也可以在同一个语句中，用逗号隔开每个包。-->
       <context:component-scan base-package="domain"></context:component-scan>
       <!-- 
                   <bean id="person" class="cn.tedu.beans.Person"></bean>        
                   <bean id="cat" class="cn.tedu.beans.Cat"></bean>        
                   <bean id="dog" class="cn.tedu.beans.Dog"></bean>        
            -->
   
   </beans>
   ```

3. 在配置的包中的类上使用@Component注解，则这个类会自动被注册为bean，使用当前类的class为<bean>的class，通常情况下使用类名首字母小写为<bean>id。

   ```
   package cn.tedu.beans;
   import org.springframework.stereotype.Component;
   
   @Component
   public class Person{
   }
   ```

4. id自动推断的规则

   如果手动指定id，则采用指定的id，不再自动推断。

   如果没有指定id，则自动推断id

   ​	如果第二个字母小写，则首字母变小写作为id

   ​	如果第二个字母大写，则首字母保持不变作为id

#### 2.注解方式工厂配置bean

Spring默认通过反射创建bean，如果某些bean没有无参构造器或创建过程非常复杂，则无法通过简单的反射创建bean，此时可以通过指定创建bean的工厂，令SpringIOC通过工厂来创建bean，从而进行注册。

可以通过配置文件方式配置bean工厂，同样也可以通过注解配置bean工厂。

1.配置工厂类

工厂类必须放在包扫描目录下，且被@Component注解修饰，要可以扫描到bean的类，但这个类可以不加注解

2.配置工厂类中生产bean的方法

工厂中生产bean的方法要被@Bean修饰

则此方法会被SpringIOC调用，并将返回的对象注册为Spring的bean，默认自动推断id(使用方法名自动推断，推断规则和之前一致)，也可以通过value属性手工指定id。

```
@Component
public class Pesron5Factory {
    @Bean("per5")
    public Person5 getInstance(){
        return new Person5("xx");
    }
}
```

## 六、注解实现DI

1. 导入开发包7+1

2. 编写配置文件，并导入context约束

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans 
           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context-3.2.xsd
           ">
   </beans>
   ```

3. 配置开启注解实现DI选项

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:util="http://www.springframework.org/schema/util"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans 
           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context-3.2.xsd
           http://www.springframework.org/schema/util
           http://www.springframework.org/schema/util/spring-util-3.2.xsd
           ">
           <!-- 开启IOC包扫描 -->
           <context:component-scan base-package="cn.tedu.domain"/>
           <!-- 开启注解配置DI -->
           <context:annotation-config/>
   </beans>
   ```

4. 注解方式注入spring内置支持的类型数据 - 非集合类型

   spring中可以通过@Value注解来实现spring内置支持的类型的属性的注入。

   ```Java
   @Component
   public class Student {
           @Value("zs")
           private String name;
           
           @Value("19")
           private int age;
           
           @Override
           public String toString() {
                   return "Student [name=" + name + ", age=" + age + "]";
           }
   }
   ```

   这种方式可以实现spring内置支持类型的注入，但是这种方式将注入的值写死在了代码中，后续如果希望改变注入的值，必须来修改源代码，此时可以将这些值配置到一个properties配置文件中，再在spring中进行引入。

   my.properties配置文件

   ```
   name=kks
   age=15
   ```

   在Spring中引入properties配置文件（xml配置文件）

   ```
    <context:property-placeholder location="my.properties"></context:property-placeholder>
   ```

   有properties配置文件时：

   ```java
   @Component
   public class Student {
           @Value("${name}")
           private String name;
           
           @Value("${age}")
           private int age;
           
           @Override
           public String toString() {
                   return "Student [name=" + name + ", age=" + age + "]";
           }
   }
   ```

5. 注解方式注入spring内置支持的类型数据 - 集合类型

   引入util名称空间，通过适当的util标签注册数据

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:util="http://www.springframework.org/schema/util"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans 
           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context-3.2.xsd
           http://www.springframework.org/schema/util
           http://www.springframework.org/schema/util/spring-util-3.2.xsd
           ">
           <!-- 开启IOC包扫描 -->
           <context:component-scan base-package="cn.tedu.domain"/>
           <!-- 开启注解配置DI -->
           <context:annotation-config></context:annotation-config>
           <!-- 引入Properties文件 -->
           <context:property-placeholder location="classpath:/stu.properties"/>
           
           <!-- 配置集合数据 -->
           <util:list id="l1">
                   <value>aaa</value>
                   <value>bbb</value>
                   <value>ccc</value>
           </util:list>
           <util:set id="s1">
                   <value>111</value>
                   <value>222</value>
                   <value>333</value>
           </util:set>
           <util:map id="m1">
                   <entry key="k1" value="v1"></entry>
                   <entry key="k2" value="v2"></entry>
                   <entry key="k3" value="v3"></entry>
           </util:map>
           <util:properties id="p1">
                   <prop key="p1">v1</prop>
                   <prop key="p2">v2</prop>
                   <prop key="p3">v3</prop>
           </util:properties>
   </beans>
   ```

   再在类的属性中通过@Value注入赋值

   ```java 
   @Component
   public class Student {
           @Value("${name}")
           private String name;
           
           @Value("${age}")
           private int age;
           
           @Value("#{@l1}")
           private List<String> list;
           
           @Value("#{@s1}")
           private Set<String> set;
           
           @Value("#{@m1}")
           private Map<String,String> map;
           
           @Value("#{@p1}")
           private Properties prop;
           
           @Override
           public String toString() {
                   return "Student [name=" + name + ", age=" + age + ", list=" + list
                                   + ", set=" + set + ", map=" + map + ", prop=" + prop + "]";
           }
           
   }
   ```

6. 使用注解注入自定义bean类型数据：

   在bean中的属性上通过@Autowired实现自定义bean类型的属性注入

   ```JAVA 
   package cn.tedu.domain;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.beans.factory.annotation.Qualifier;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.stereotype.Component;
   
   import javax.annotation.Resource;
   import java.util.List;
   import java.util.Map;
   import java.util.Properties;
   import java.util.Set;
   
   @Component
   public class Hero {
       @Value("${name}")
       private String name;
       @Value("${age}")
       private int age;
       @Value("#{@l1}")
       private List<String> list;
       @Value("#{@s1}")
       private Set<String> set;
       @Value("#{@m1}")
       private Map<String,String> map;
       @Value("#{@p1}")
       private Properties props;
   
       /**
        * 如果配置@Autowired但没有配置@Qualifier
        *      先按类型查找，找到唯一的一个直接注入
        *          找不到或者找到多个,开始按id查找
        *          根据当前属性的名称寻找相同id的bean，如果找到就注入，找不到就抛异常
        *  如果同时配置@Autowired和@Qualifier
        *      不再按照类型查找，直接按指定id查找，找到就注入，找不到直接抛异常
        */
   
       @Autowired
       @Qualifier("JMDog")
       private Dog dog;
   
       @Override
       public String toString() {
           return "Hero{" +
                   "name='" + name + '\'' +
                   ", age=" + age +
                   ", list=" + list +
                   ", set=" + set +
                   ", map=" + map +
                   ", props=" + props +
                   ", dog=" + dog +
                   '}';
       }
   }
   
   ```

## 七、其他注解

#### 1.@Scope(value="prototype")

配置修饰的类的bean是单例还是多例，如果不配置默认为单例

```Java
package cn.tedu.domain;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
public class Teacher {
}
```

#### 2.@Lazy

配置修饰的类的bean采用懒加载机制

```Java
package cn.tedu.domain;

import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Component;

@Component
@Lazy
public class Teacher {
        public Teacher() {
                System.out.println("teacher construct..");
        }
}

```

#### 3.@PostConstruct  @PreDestroy

@PostConstruct ：在bean对应的类中 修饰某个方法 将该方法声明为初始化方法，对象创建之后立即执行。

@PreDestroy：在bean对应的类中 修饰某个方法 将该方法声明为销毁的方法，对象销毁之前调用的方法。

```Java
package cn.tedu.beans;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import org.springframework.stereotype.Component;

@Component
public class Dog {
	public Dog() {
		System.out.println("Dog...被创建出来了...");
	}
	
	@PostConstruct
	public void init(){
		System.out.println("Dog的初始化方法。。。");
	}
	
	@PreDestroy
	public void destory(){
		System.out.println("Dog的销毁方法。。。");
	}
}
```

#### 4.@Controller @Service @Repository @Component

这四个注解的功能是完全相同的，都是用来修饰类，将类声明为Spring管理的bean的。

其中@Component一般认为是通用的注解

而@Controller用在软件分层中的控制层，一般用在web层

而@Service用在软件分层中的业务访问层，一般用在service层

而@Repository用在软件分层中的数据访问层，一般用在dao层

## 八、案例：Spring IOC实现easymall分层

dao层

```
public interface UserDao {
    public void addUser();
    public void delUser();
}
```

```
import org.springframework.stereotype.Repository;

@Repository
public class MySqlUserDao implements UserDao {
    @Override
    public void addUser() {
        System.out.println("添加用户。。");
    }

    @Override
    public void delUser() {
        System.out.println("删除用户");
    }
}

```

service层

```
public interface UserService {
    public void addUser();
    public void delUser();
}

```

```Java
import dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;

    @Override
    public void addUser() {
        userDao.addUser();
    }

    @Override
    public void delUser() {
        userDao.delUser();
    }
}

```

web层

```Java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import service.UserService;

@Controller
public class UserServlet {
    @Autowired
    private UserService userService;
    public void add(){
        userService.addUser();
    }
    public void del(){
        userService.delUser();
    }
}

```

配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
">
    <!--开启包扫苗-->
    <context:component-scan base-package="service"></context:component-scan>
    <context:component-scan base-package="dao"></context:component-scan>
    <context:component-scan base-package="web"></context:component-scan>
    <!--允许di注入-->
    <context:annotation-config />
</beans>
```

