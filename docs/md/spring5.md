# 1、概述及IOC理论推导

## 简介

Spring是一个轻量级的控制反转IOC和面向切面AOP的容器，用来管理bean！

### 优点

1、开源免费

2、轻量级、非侵入式

3、IOC、AOP

4、对事务的支持，对框架的支持。。。

### 组成

组成Spring框架的每个模块都可以单独存在，每个模块的功能如下：

- 核心容器：主要组件是BeanFactory，是工厂模式的实现
- Spring上下文：是一个配置文件，向Spring框架提供上下文信息
- Spring AOP：为基于Spring的应用程序中的对象提供了事务管理服务
- SpringDAO：提供了有意义的异常层次结构
- Spring ORM：提供了ORM的对象关系工具
- Spring WEB：为基于web的应用程序提供了上下文
- Spring MCV：是一个全功能的构建web应用程序的MVC实现

## IOC

> **控制反转是一种通过描述（XML或者注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，其实现方法是依赖注入DI**

### IOC本质

- 原来需要在Service层手动new一个dao层的对象，如果有多个dao层对象，就得多new
- 现在可以通过Spring来管理bean，客户端调用时根据需求创建需要的对象

# 2、快速上手Spring

## IOC创建对象的方式

1、通过无参构造方法来创建

2、通过有参构造方法来创建

# 3、依赖注入DI

## 概念

- 依赖注入
- 依赖：Bean对象的创建依赖于容器
- 注入：Bean对象依赖的资源，由容器来设置和装配

## 注入方式

- 构造器注入
- Set方法注入(重点)，要求被注入的属性，必须有set方法，set方法的方法名由set+属性首字母大写
  - 常量注入
  - Bean注入
  - 数组注入
  - List注入
  - Map注入
  - set注入
  - Null注入
  - Properties注入
- p命名和c命名注入

## Bean的作用域

`<bean id="leo" class="cn.leodon.test" scope="singleton">`

- Singleton
- Prototype（原型）
- Request
- Session

# 4、Bean的自动装配

## 自动装配说明

- 自动装配是使用Spring满足Bean依赖的一种方法
- spring会在应用上下文中为**某个bean寻找其依赖的bean**
- 推荐不使用自动装配xml配置，而使用注解

## XML配置

- byName

  `<bean id=" " class=" " autowire="byName">`

  按名称自动装配

- byType

  按类型自动装配

## 使用注解进行自动装配

- 准备工作

  - 在Spring配置文件中引入context文件头

  - 开启属性注解支持

    `<context:annotation-config/>`

- @Autowired

  - 按类型自动装配，不支持id匹配。
  - 需要导入spring-aop的包
  - 将实体类中的set方法去掉，使用@Autowired注解需要引入的类，例：在User类中引入Cat与Dog类。

- @Qualifier

  - 根据类型自动装配，可以根据byName的方式自动装配
  - 不能单独使用

- @Resource

# 5、使用注解开发

## 说明

- 在Spring4之后，想要使用注解形式，必须得要引入aop的包
- 在配置文件当中，还得要引入一个context约束

## Bean的实现

1. 配置扫描哪些包下的注解

   `<context:component-scan base-package="com.leodon.pojo" />`

2. 在指定包下编写类，增加注解

   ```java
   @component('user')
   //相当于配置文件中<bean id="user" class="当前注解的类/">
   public class User{
   	public String name="leodon";
   }
   ```

3. 测试

   ```java
   public void test(){
   	ApplicationContext applicationContext=new ClassPathXmlApplicationContext("beans.xml");
   	User user=(User)applicationContext.getBean("user");
   }
   ```

## 属性注入

​	使用注解注入属性

1. 可以不用提供set方法，直接在注解名上添加@value("值")

   ```Java
   @Component("user")
   // 相当于配置文件中 <bean id="user" class="当前注解的类"/>
   public class User {
      @Value("秦疆")
      // 相当于配置文件中 <property name="name" value="秦疆"/>
      public String name;
   }
   ```

2. 如果提供了set方法，在set方法上添加@value("值")

   ```Java
   @Component("user")
   public class User {
   
      public String name;
   
      @Value("秦疆")
      public void setName(String name) {
          this.name = name;
     }
   }
   ```

## 衍生注解

**@Component三个衍生注解**

为了更好的进行分层，Spring可以使用其它三个注解，功能一样，目前使用哪一个功能都一样。

- @Controller：web层
- @Service：service层
- @Repository：dao层

写上这些注解，就相当于将这个类交给Spring管理装配了！

## 作用域

**@scope**

- singleton：默认的，Spring会采用单例模式创建这个对象。关闭工厂 ，所有的对象都会销毁。
- prototype：多例模式。关闭工厂 ，所有的对象不会销毁。内部的垃圾回收机制会回收

```Java
@Controller("user")@Scope("prototype")
public class User {   
    @Value("秦疆")   
    public String name;
}
```

## 小结

**XML与注解比较**

- XML可以适用任何场景 ，结构清晰，维护方便
- 注解不是自己提供的类使用不了，开发简单方便

**xml与注解整合开发** ：推荐最佳实践

- xml管理Bean
- 注解完成属性注入
- 使用过程中， 可以不用扫描，扫描是为了类上的注解

```xml
<context:annotation-config/>  
```

**使用上述代码进行配置：**

- 进行注解驱动注册，从而使注解生效
- 用于激活那些已经在spring容器里注册过的bean上面的注解，也就是显示的向Spring注册
- 如果不扫描包，就需要手动配置bean
- 如果不加注解驱动，则注入的值为null！

## 基于Java类进行配置

1. 编写一个实体类，Dog

   ```java
   @Component  //将这个类标注为Spring的一个组件，放到容器中！
   public class Dog {
      public String name = "dog";
   }
   ```

2. 新建一个config配置包，编写一个MyConfig配置类

   ```Java
   @Configuration  //代表这是一个配置类
   public class MyConfig {
   
      @Bean //通过方法注册一个bean，这里的返回值就Bean的类型，方法名就是bean的id！
      public Dog dog(){
          return new Dog();
     }
   
   }
   ```

3. 测试

   ```Java
   @Test
   public void test2(){
      ApplicationContext applicationContext =
              new AnnotationConfigApplicationContext(MyConfig.class);
      Dog dog = (Dog) applicationContext.getBean("dog");
      System.out.println(dog.name);
   }
   ```

   ​	**关于这种Java类的配置方式，我们在之后的SpringBoot 和 SpringCloud中还会大量看到，我们需要知道这些注解的作用即可！**

# 6、静态/动态代理模式

## 代理模式

​	代理模式：

- 静态代理
- 动态代理

学习aop之前 , 我们要先了解一下代理模式！

## 静态代理

**静态代理角色分析**

- 抽象角色 : 一般使用接口或者抽象类来实现
- 真实角色 : 被代理的角色
- 代理角色 : 代理真实角色 ; 代理真实角色后 , 一般会做一些附属的操作 .
- 客户  :  使用代理角色来进行一些操作 

**代码实现**

Rent.java 即抽象角色，一般为接口

```Java
//抽象角色：租房
public interface Rent {
   public void rent();
}
```

Host . java 即真实角色

```Java
//真实角色: 房东，房东要出租房子
public class Host implements Rent{
   public void rent() {
       System.out.println("房屋出租");
  }
}
```

Proxy . java 即代理角色

```Java
//代理角色：中介
public class Proxy implements Rent {

   private Host host;
   public Proxy() { }
   public Proxy(Host host) {
       this.host = host;
  }

   //租房
   public void rent(){
       seeHouse();
       host.rent();
       fare();
  }
   //看房
   public void seeHouse(){
       System.out.println("带房客看房");
  }
   //收中介费
   public void fare(){
       System.out.println("收中介费");
  }
}
```

Client . java 即客户

```Java
//客户类，一般客户都会去找代理！
public class Client {
   public static void main(String[] args) {
       //房东要租房
       Host host = new Host();
       //中介帮助房东
       Proxy proxy = new Proxy(host);

       //你去找中介！
       proxy.rent();
  }
}
```

分析：在这个过程中，你直接接触的就是中介，就如同现实生活中的样子，你看不到房东，但是你依旧租到了房东的房子通过代理，这就是所谓的代理模式，程序源自于生活，所以学编程的人，一般能够更加抽象的看待生活中发生的事情。

**静态代理的好处:**

- 可以使得我们的真实角色更加纯粹 . 不再去关注一些公共的事情 .
- 公共的业务由代理来完成 . 实现了业务的分工 ,
- 公共业务发生扩展时变得更加集中和方便 .

缺点 :

- 类多了 , 多了代理类 , 工作量变大了 . 开发效率降低 .

我们想要静态代理的好处，又不想要静态代理的缺点，所以 , 就有了动态代理 !

## 动态代理

- 动态代理的角色和静态代理的一样 .

- **动态代理的代理类是动态生成的 . 静态代理的代理类是我们提前写好的**

- 动态代理分为两类 : 一类是基于**接口动态代理** , 一类是基于**类的动态代理**

- - 基于接口的动态代理----JDK动态代理
  - 基于类的动态代理--cglib
  - 现在用的比较多的是 javasist 来生成动态代理 . 百度一下javasist
  - 我们这里使用JDK的原生代码来实现，其余的道理都是一样的！

**JDK的动态代理需要了解两个类**

核心 : InvocationHandler     和     Proxy

- 【InvocationHandler：调用处理程序，是一个接口】

```Java
Object invoke(Object proxy, 方法 method, Object[] args)；
//参数
//proxy - 调用该方法的代理实例
//method -所述方法对应于调用代理实例上的接口方法的实例。方法对象的声明类将是该方法声明的接口，它可以是代理类继承该方法的代理接口的超级接口。
//args -包含的方法调用传递代理实例的参数值的对象的阵列，或null如果接口方法没有参数。原始类型的参数包含在适当的原始包装器类的实例中，例如java.lang.Integer或java.lang.Boolean 。
```

- 【Proxy  : 代理】

```Java
//生成代理类
public Object getProxy(){
   return Proxy.newProxyInstance(this.getClass().getClassLoader(),
                                 rent.getClass().getInterfaces(),this);
}
```

**代码实现**

抽象角色和真实角色和之前的一样！

Rent . java 即抽象角色

```
//抽象角色：租房
public interface Rent {
   public void rent();
}
```

Host . java 即真实角色

```Java
//真实角色: 房东，房东要出租房子
public class Host implements Rent{
   public void rent() {
       System.out.println("房屋出租");
  }
}
```

ProxyInvocationHandler. java 即代理角色

```Java
public class ProxyInvocationHandler implements InvocationHandler {
   private Rent rent;

   public void setRent(Rent rent) {
       this.rent = rent;
  }

   //生成代理类，重点是第二个参数，获取要代理的抽象角色！之前都是一个角色，现在可以代理一类角色
   public Object getProxy(){
       return Proxy.newProxyInstance(this.getClass().getClassLoader(),
               rent.getClass().getInterfaces(),this);
  }

   // proxy : 代理类 method : 代理类的调用处理程序的方法对象.
   // 处理代理实例上的方法调用并返回结果
   @Override
   public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
       seeHouse();
       //核心：本质利用反射实现！
       Object result = method.invoke(rent, args);
       fare();
       return result;
  }

   //看房
   public void seeHouse(){
       System.out.println("带房客看房");
  }
   //收中介费
   public void fare(){
       System.out.println("收中介费");
  }

}
```

Client . java

```Java
//租客
public class Client {

   public static void main(String[] args) {
       //真实角色
       Host host = new Host();
       //代理实例的调用处理程序
       ProxyInvocationHandler pih = new ProxyInvocationHandler();
       pih.setRent(host); //将真实角色放置进去！
       Rent proxy = (Rent)pih.getProxy(); //动态生成对应的代理类！
       proxy.rent();
  }

}
```

核心：**一个动态代理 , 一般代理某一类业务 , 一个动态代理可以代理多个类，代理的是接口！、**

# 7、AOP

## 什么是AOP

AOP（Aspect Oriented Programming）意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

**提供声明式事务；允许用户自定义切面**

以下名词需要了解下：

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志 , 安全 , 缓存 , 事务等等 ....
- 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，它是一个类。
- 通知（Advice）：切面必须要完成的工作。即，它是类中的一个方法。
- 目标（Target）：被通知对象。
- 代理（Proxy）：向目标对象应用通知之后创建的对象。
- 切入点（PointCut）：切面通知 执行的 “地点”的定义。
- 连接点（JointPoint）：与切入点匹配的执行点。

![](../img/aop.png)

**即 Aop 在 不改变原有代码的情况下 , 去增加新的功能 .**

Spring的Aop就是将公共的业务 (日志 , 安全等) 和领域业务结合起来 , 当执行领域业务时 , 将会把公共业务加进来 . 实现公共业务的重复利用 . 领域业务更纯粹 , 程序猿专注领域业务 , 其本质**还是动态代理 .** 

## 使用Spring实现Aop

【重点】使用AOP织入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
   <groupId>org.aspectj</groupId>
   <artifactId>aspectjweaver</artifactId>
   <version>1.9.4</version>
</dependency>
```

1. **第一种方式：通过 Spring API 实现**

   首先编写我们的***业务接口***和***实现类***

   ```Java
   public interface UserService {
      public void add();
      public void delete();
      public void update();
      public void search();
   }
   public class UserServiceImpl implements UserService{
      @Override
      public void add() {
          System.out.println("增加用户");
     }
      @Override
      public void delete() {
          System.out.println("删除用户");
     }
      @Override
      public void update() {
          System.out.println("更新用户");
     }
      @Override
      public void search() {
          System.out.println("查询用户");
     }
   }
   ```

   然后去写我们的增强类 , 我们编写两个 , 一个前置增强 一个后置增强

   ```Java
   public class Log implements MethodBeforeAdvice {
   
      //method : 要执行的目标对象的方法
      //objects : 被调用的方法的参数
      //Object : 目标对象
      @Override
      public void before(Method method, Object[] objects, Object o) throws Throwable {
          System.out.println( o.getClass().getName() + "的" + method.getName() + "方法被执行了");
     }
   }
   public class AfterLog implements AfterReturningAdvice {
      //returnValue 返回值
      //method被调用的方法
      //args 被调用的方法的对象的参数
      //target 被调用的目标对象
      @Override
      public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
          System.out.println("执行了" + target.getClass().getName()
          +"的"+method.getName()+"方法,"
          +"返回值："+returnValue);
     }
   }
   ```

   最后去spring的文件中注册 , 并实现aop切入实现 , 注意导入约束 .

   ```Java
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:aop="http://www.springframework.org/schema/aop"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/aop
          http://www.springframework.org/schema/aop/spring-aop.xsd">
   
      <!--注册bean-->
      <bean id="userService" class="com.kuang.service.UserServiceImpl"/>
      <bean id="log" class="com.kuang.log.Log"/>
      <bean id="afterLog" class="com.kuang.log.AfterLog"/>
   
      <!--aop的配置-->
      <aop:config>
          <!--切入点 expression:表达式匹配要执行的方法-->
          <aop:pointcut id="pointcut" expression="execution(* com.kuang.service.UserServiceImpl.*(..))"/>
          <!--执行环绕; advice-ref执行方法 . pointcut-ref切入点-->
          <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
          <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
      </aop:config>
   
   </beans>
   ```

   测试

   ```Java
   public class MyTest {
      @Test
      public void test(){
          ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
          UserService userService = (UserService) context.getBean("userService");
          userService.search();
     }
   }
   ```

2. **第二种方式**：**自定义类来实现Aop**

   目标业务类不变依旧是userServiceImpl

   第一步 : 写我们自己的一个切入类

   ```
   public class DiyPointcut {
   
      public void before(){
          System.out.println("---------方法执行前---------");
     }
      public void after(){
          System.out.println("---------方法执行后---------");
     }
      
   }
   ```

   去spring中配置

   ```xml
   <!--第二种方式自定义实现-->
   <!--注册bean-->
   <bean id="diy" class="com.kuang.config.DiyPointcut"/>
   
   <!--aop的配置-->
   <aop:config>
      <!--第二种方式：使用AOP的标签实现-->
      <aop:aspect ref="diy">
          <aop:pointcut id="diyPonitcut" expression="execution(* com.kuang.service.UserServiceImpl.*(..))"/>
          <aop:before pointcut-ref="diyPonitcut" method="before"/>
          <aop:after pointcut-ref="diyPonitcut" method="after"/>
      </aop:aspect>
   </aop:config>
   ```

   测试：

   ```Java
   public class MyTest {
      @Test
      public void test(){
          ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
          UserService userService = (UserService) context.getBean("userService");
          userService.add();
     }
   }
   ```

3. **第三种方式**：**使用注解实现**

   第一步：编写一个注解实现的增强类

   ```Java
   package com.kuang.config;
   
   import org.aspectj.lang.ProceedingJoinPoint;
   import org.aspectj.lang.annotation.After;
   import org.aspectj.lang.annotation.Around;
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   
   @Aspect
   public class AnnotationPointcut {
      @Before("execution(* com.kuang.service.UserServiceImpl.*(..))")
      public void before(){
          System.out.println("---------方法执行前---------");
     }
   
      @After("execution(* com.kuang.service.UserServiceImpl.*(..))")
      public void after(){
          System.out.println("---------方法执行后---------");
     }
   
      @Around("execution(* com.kuang.service.UserServiceImpl.*(..))")
      public void around(ProceedingJoinPoint jp) throws Throwable {
          System.out.println("环绕前");
          System.out.println("签名:"+jp.getSignature());
          //执行目标方法proceed
          Object proceed = jp.proceed();
          System.out.println("环绕后");
          System.out.println(proceed);
     }
   }
   ```

   第二步：在Spring配置文件中，注册bean，并增加支持注解的配置

   ```
   <!--第三种方式:注解实现-->
   <bean id="annotationPointcut" class="com.kuang.config.AnnotationPointcut"/>
   <aop:aspectj-autoproxy/>
   ```

# 8、整合mybatis

> https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg==&amp;mid=2247484144&amp;idx=1&amp;sn=768f97da78a9ceae8321d101da3c480e&amp;scene=19#wechat_redirect