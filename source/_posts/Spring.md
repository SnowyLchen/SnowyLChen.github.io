---
title: Spring
date: 2019-11-03 12:24:37
tags: 
- Spring
- ssm
categories: Spring
---

# 一、步骤：

## 1.添加依赖：

```java
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.1.6.RELEASE</version>
    </dependency>
```

## 2.配置文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <!--
        class:写java类的全限定类名，它通过全类名然后使用反射的技术进行创建的
        id：ID就是给这个对象在整个应用程序上下文中取个名字以便区分
        -->

    <bean class="spring.com.pojo.Gril" id="gril">

    </bean>
</beans>
```



## 3.类

```java
package spring.com.pojo;

public class Gril {
    private String name;

    private  String sex;


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }


}
//子类
package spring.com.pojo;

public class PrettyGril extends Gril{



    public  void show(){
        System.out.println("美丽女孩");
    }
}

```

## 4.测试类

```java
package spring;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import spring.com.pojo.Gril;
 @Test
    public void m1(){
        //首先获取上下文对象，spring里面声明的对象都需要通过上下文获取
        ApplicationContext apc=new ClassPathXmlApplicationContext("applicationContext.xml");
        //通过Gril获取获取girl
        Gril g = (Gril) apc.getBean("gril");
        System.out.println(g);
        ((PrettyGril)g).show();
    }
}

```

## 5.结果

```java
spring.com.pojo.PrettyGril@458c1321
美丽女孩

Process finished with exit cod0

```

## 6.总结

在测试类中不需要变代码，只需将配置文件中

```java
<bean class="spring.com.pojo.Gril" id="gril"></bean>
变成
<bean class="spring.com.pojo.PrettyGril" id="gril"></bean>
```

# 二、值的注入

## 1.值的注入

- 通过setter（）方法注入
  - 必须其字段有对应的setter方法才可以完成
  - 通过property子节点完成注入
- 通过构造器注入。使用<constructor-arg name="" value=""></constructor-arg>

## 2.bean元素探讨

属性探讨

- abstract ： 该bean无法被实例化（用来被继承）

- parent ： 指明其父类是谁，将会继承父bean中的所有内容，通过id进行指引。（配合abstract使用）

- ![1558773594444](/images/post/1558773594444.png)

- destroy-method : 指定这个bean在最后被销毁的时候一定执行的方法。适合于清理型工作。触发条件是这个bean确实被销毁才会发生。

  - 容器close（）会触发
  - refresh（） 会触发
  - destroy（）也可以触发（不推荐使用，已out）

- init-method : 指定bean 的初始化方法，适合一些准备型工作。

- name : 别名；（可以写多个）如：

- ![1558774472848](/images/post/1558774472848.png)

  - 注：Spring 默认为单例

- scope ： 指定范围。默认为 singleton

  - singleton ：单例 ,==Spring上下文==当中，只有一个实例
  - prototype ：原型  要一个就新给一个。

- lazy-init : 

  - true : 当Spring读取配置文件的是时候不会初始化bean，当我们使用的时候才会去初始化（默认构造函数）
    - 直接初始化
      - 会使得应该程序启动的慢一点，内存占用大一点。
      - 当我们使用bean的时候会快一点。
    - 延迟初始化
      - 会使得应该程序启动的快一点，内存占用小一点。
      - 当我们使用bean的时候会慢一点。

- depends-on ： 依赖的bean，如果一个bean的使用严重依赖于另一个bean的准备的话，就可以配置

  depends-on。

对于非字面值可以描述的值的注入问题

![1558776108541](/images/post/1558776108541.png)

通过ref指向另外一个bean的ID

关于在Spring的配置文件当中单独定义我们的别名。通过allas标签完成即可

Spring配置文件里面的bean是可以互相引用的（前提是被上下文所扫描到的）

------

3.Spring 各种值类型的注入方式

- 数组
- list
- set
- map

如果其对应值是简单是字面值，直接写property赋值就ok，如果是一个其他的类，则使用内部bean的方式注入

## 4.自动注入

- byType：按照数据类型注入
- byName：按照bean对应的pojo里面属性的名字来进行匹配
- byConstructor
  - 优先按照类型去匹配，如果匹配到一个，那么直接注入；
  - 如果匹配的不止一个 ，按照名字注入；
  - 如果一个都找不到。注入失败。
- default：默认；
- no ：不注入；

![1558779500102](/images/post/1558779500102.png)



------

# 三、常用注解

- component
- controller(SpringMVC)
- Service（业务层）
- repository(dao层)

------

# 四、零散的知识点

- 如何引入外部的文件

  ```java
  <context:property-placeholder location="classpath:beans.xml"/>
  ```

- 如何通过表达式引用外部properties的键值

  ```java
     <property name="url" value="${url}"/>
  ```

- 从一个配置文件引入多个Spring配置文件

  ```java
  <import resource="classpath:spring-*.xml"/>
  ```

- 配置扫描包

  ```xml
  <!--这个扫描方式是扫描所有子包-->
      <context:component-scan base-package="com.ProvideService">
          <!--除了Repository这个包-->
          <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
      </context:component-scan>
  ```

  

------

# 五、SpringAOP（面向切面编程）

## 1.xml版

需要导入AspectJ和AspectJ Weaver的jar包

如果不是Spring容器所管理的bean，那么织入行为是无效的。

![1558865480806](/images/post/1558865480806.png)



![1558865553339](/images/post/1558865553339.png)

![1558945717792](/images/post/1558945717792.png)

## 2.注解版

### (1) 在applicationContext.xml中

​	设置自动代理（< aop:aspectj-autoproxy />）

​	扫描包（<context:component-scan base-package="com" />）

### (2) 在advice中

#### 1> BeforeAdvice



```xml
package com.advice;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;
@Aspect
@Component  //注册成为一个组件
public class BeforeAdvice {


    @Before("execution(* com.ProvideService.BeforeAdviceAOP.BeforeMethod())")
    public void before(){

        System.out.println("我在方法之前执行");
    }
}
```

#### 2> AfterAdvice

```XML
package com.advice;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class AfterAdvice {


    @After("execution(* com.ProvideService.AfterAdviceAOP.AfterMethod())")
    public  void  After(){
        System.out.println("我在方法之后执行");
    }
}

```

#### 3> AfterReturnning

```xml
package com.advice;

import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;
@Aspect
@Component
public class AfterReturningAdvice {


    @AfterReturning(value ="execution(* com.ProvideService.AfterReturningAdviceAOP.afterReturningMethod())",returning = "returning")
    public void afterReturning(String returning){


        System.out.println("返回值之后。。。。"+returning);
    }
}
```

#### 4> AfterThrowing

```xml
package com.advice;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class AfterthrowingAdvice {

    @AfterThrowing(value = "execution(* com.ProvideService.AfterthrowingAdviceAOP.afterthrowMethod())",throwing = "throwingMsg")
    public void afterthrowing(JoinPoint joinPoint,String throwingMsg){

        System.out.println("异常之后....."+joinPoint.getSignature().getName());
        System.out.println("异常信息："+throwingMsg);
    }
}

```

### (3) 在Service中

#### 1> BeforeAdviceAOP

```xml
package com.ProvideService;

import org.springframework.stereotype.Component;

@Component
public class BeforeAdviceAOP {

    public void BeforeMethod(){

        System.out.println("BeforeMethod AOP");
    }
}

```

#### 2> AfterAdviceAOP

```xml
package com.ProvideService;

import org.springframework.stereotype.Component;

@Component
public class AfterAdviceAOP {


    public void AfterMethod(){

        System.out.println("AfterMethod AOP");
    }
}

```

#### 3> AfterReturningAdviceAOP

```xml
package com.ProvideService;

import org.springframework.stereotype.Component;

@Component
public class AfterReturningAdviceAOP {


    public String  afterReturningMethod(){

        System.out.println("afterReturningMethod AOP");
        return "我是afterReturningMethod方法";
    }
}

```

#### 4> AfterthrowingAdviceAOP

```xml
package com.ProvideService;

import org.springframework.stereotype.Component;

@Component
public class AfterthrowingAdviceAOP {

    public void afterthrowMethod(){
        System.out.println("我是异常");
        throw new RuntimeException("我有错误，出现异常");
    }
}


```

### (4) 测试类

```java
package spring;

import com.ProvideService.*;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import spring.com.pojo.Gril;
import spring.com.pojo.PrettyGril;

public class Test2 {
    @Test
    public void m1(){
        //首先获取上下文对象，spring里面声明的对象都需要通过上下文获取
        ApplicationContext apc=new ClassPathXmlApplicationContext("applicationContext.xml");
        BeforeAdviceAOP beforeAdviceAOP = apc.getBean("beforeAdviceAOP", BeforeAdviceAOP.class);
        beforeAdviceAOP.BeforeMethod();
    }
    @Test
    public void m2(){
        //首先获取上下文对象，spring里面声明的对象都需要通过上下文获取
        ApplicationContext apc=new ClassPathXmlApplicationContext("applicationContext.xml");
        AfterAdviceAOP afterAdviceAOP = apc.getBean("afterAdviceAOP", AfterAdviceAOP.class);
        afterAdviceAOP.AfterMethod();
    }
    @Test
    public void m3(){
        //首先获取上下文对象，spring里面声明的对象都需要通过上下文获取
        ApplicationContext apc=new ClassPathXmlApplicationContext("applicationContext.xml");
        AfterReturningAdviceAOP afterReturningAdviceAOP = apc.getBean("afterReturningAdviceAOP", AfterReturningAdviceAOP.class);
        afterReturningAdviceAOP.afterReturningMethod();
    }
    @Test
    public void m4(){
        //首先获取上下文对象，spring里面声明的对象都需要通过上下文获取
        ApplicationContext apc=new ClassPathXmlApplicationContext("applicationContext.xml");
        AfterthrowingAdviceAOP afterthrowingAdviceAOP = apc.getBean("afterthrowingAdviceAOP", AfterthrowingAdviceAOP.class);
        afterthrowingAdviceAOP.afterthrowMethod();
    }

}


```

## 注：(SpringAOP顺序)

- 设置自动代理
- 扫描包（通过context）
- 写建议（Advice）
  - @Aspect
  - @Component
- 写要切的类及其要切的方法（AdviceAOP）
  - @Component
- 测试即可。
- 想要同类型建议按顺序执行
  - 写多个建议，每个建议中写@Order(num)注解
  - 执行顺序
    - Before(从1-2-3顺序执行)
      - @Order(1)
      - @Order(2)
      - @Order(3)
    - After(从3-2-1顺序执行)
      - @Order(3)
      - @Order(2)
      - @Order(1)