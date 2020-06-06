---
title: SpringBoot初学
date: 2019-11-06 14:55:55
tags: Springboot
categories: SpringBoot
---

# 一、SpringBoot入门

## 1.SpringBoot简介

> 简化Spring应用开发的框架
>
> 相当于整个 Spring技术栈的大整合
>
> 是J2EE开发的一站式解决方案

## 2.微服务

2014，Martin fowler

微服务：架构风格

一个应用应该是一组小型服务；可以通过HTTP的方式进行互通

每一个功能元素都是最终都是一个可独立替换的独立升级的软件单元

[详细参照文档](https://www.jianshu.com/p/4821a29fa998)



## 3.环境约束

> -jdk1.8
>
> -maven3.6.1
>
> -idea 2018.3
>
> -SpringBoot 2

### 1.maven配置

给settings文件设置

```xml
<profiles>
  	<profile>    
     <id>jdk-1.8</id>    
	      	<activation>    
	           <activeByDefault>true</activeByDefault>    
	           <jdk>1.8</jdk>    
	        </activation>    
		  <properties>    
			  <maven.compiler.source>1.8</maven.compiler.source>    
			  <maven.compiler.target>1.8</maven.compiler.target>    
			  <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>    
		 </properties>    
	 </profile> 
```

### 2.idea设置

![1573025431384](/images/post/1573025431384.png)

## 4.SpringBoot HelloWorld

浏览器发送hello请求，服务器接收请求并处理，相应helloworld字符串。

### 1.创建一个maven工程（jar）

### 2.导入SpringBoot相关的依赖

```xml
 <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.0.RELEASE</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 3.编写一个主程序；启动SpringBoot应用

 ```java

/**
 * @SpringBootApplication 来标记他是一个主程序类。说明他是一个SpringBoot应用
 */
@SpringBootApplication
public class SpringBootMainApplication {

    public static void main(String[] args) {
        //让Spring应该启动
        SpringApplication.run(SpringBootMainApplication.class,args);
    }
}
 ```

### 4.编写Controller、Service

```java
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}
```

### 5.运行主程序测试

### 6.简化部署

#### 1.导入maven部署插件

```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.2.0.RELEASE</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

就可以将应用打成jar包，使用java -jar 的命令进行执行；

![1573027236921](/images/post/1573027216018.png)

![1573027261398](/images/post/1573027261398.png)

## 5.Hello World 探究

### 1.pom.xml

#### 1.父项目

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.2.0.RELEASE</version>
</parent>
```

#### 2.启动器

```xml 
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

spring-boot-starter-==web==

​	spring-boot-starter:SpringBoot场景启动器，帮我们导入了web模块正常运行所需要的组件。

SpringBoot将所有的功能场景都抽取出来，做成一个个的starters（启动器），只需要在项目里映入这些starter相关场景的所有依赖都会导入进来，要用什么功能就导入什么场景的启动器。



### 2主程序类，主入口类

```java
/**
 * @SpringBootApplication 来标记他是一个主程序类。说明他是一个SpringBoot应用
 */
@SpringBootApplication
public class SpringBootMainApplication {

    public static void main(String[] args) {
        //让Spring应该启动
        SpringApplication.run(SpringBootMainApplication.class,args);
    }
}
```

`@SpringBootApplication`:SpringBoot应用标注在某个类商，说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
@ConfigurationPropertiesScan
public @interface SpringBootApplication {
```

@**SpringBootConfiguration**:SpringBoot的配置类	

- 标注在某个类上，表示这个是一个SpringBoot的配置类；
- @Configuration：配置类上来标注这个注解；

​		配置类---配置文件；配置类也是容器的一个组件；@Component

@**EnableAutoConfiguration**：开启自动配置功能：

​	以前我们需要配置的东西，SpringBoot帮我们自动配置：@**EnableAutoConfiguration**告诉SpringBoot开启自动配置功能；这样自动配置才能生效。

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

@**AutoConfigurationPackage**:自动配置包

- @Import(AutoConfigurationPackages.Registrar.class)；
- Spring的底层注解@Import，给容器中导入一个组件；导入的组件由AutoConfigurationPackages.Registrar.class
- ==将主配置类（@SpringBootApplication标注的类）的所在包级下面所有自爆里面的所有组件扫描到Spring容器==
- @Import(AutoConfigurationPackages.Registrar.class)给容器中导入组件，AutoConfigurationPackages导入组件的选择器
- 将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中；会给容器中导入非常多的自动配置类（xxxAutoConfiguration）；就是给容器中导入这个场景所需要的所有组件，并配置好这些组件。

![1573029783305](/images/post/1573029783305.png)

有了自动配置类，免去了我们手动编写配置注入功能组件等工作。

 SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class，beanClassLoader);

==作用是SpringBoot在启动的时候从类路径下--META_INFO/spring.factories获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作。==

J2EE整体整合解决方案和自动配置都在spring-boot-2.2.0.RELEASE.jar

## 6.使用spring Initializer快速创建SpringBoot项目

IDE都支持使用Spring的项目创建向导快速创建一个SpringBoot项目

选择需要的模块，向导会联网创建SpringBoot项目

默认生成的SpringBoot项目

- 主程序已经生成好了，我们只需要编写自己的逻辑
- resources文件夹中的目录
  - static: 保存所有的静态资源（css、js等）
  - templates：保存所有的模板页面（SpringBoot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面），可以使用模板引擎（freemarker，thymeleaf）
  - application.properties:SpringBoot应用的默认配置文件，可以改一些默认值（如写server.port=8081可以直接修改端口号）

# 二、配置文件

## 1.配置文件

SpringBoot使用一个全局的配置文件

> application.properties
>
> application.yml

配置文件的作用：修改SpringBoot自动配置的默认值。

SpringBoot在底层将所有的东西都自动配置好了

YAML（YAML Ain't Markup Language）

- YAML A Markup Language 是一个标记语言
- YAML isn't Markup Language 不是一个标记语言

标记语言：

​	以前的配置文件大多数是xx.xml的文件。

​	YAML:以数据为中心，比JSON，XML更适合做配置文件

​	YAML：配置实例

```yaml
server:
  port: 8081
```

​	XML：

```xml
<server>
    <prot>8081</prot>
</server>
```

## 2.yaml语法：

### 1.基本语法

K: (有一个空格)V表示一对键值对（空格必须有）;

以**空格**的缩进来控制层级关系，只要是左对齐的一列数据，都是同一个层级的，如：

```yaml
server:
	port: 8081
	path: /hello
```

属性和值也是不区分的。

### 2.值的写法

#### 字面量：普通的值（数字，字符串，boolean）:

​	k: v:字面量直接来写。

​		字符串默认不用加上单引号或双引号。

​		"":双引号：不会转义字符串里面的特殊字符，特殊字符会作为本身祥表示的意思；

​				name:"张三\n 李四"--------输出 "张三换行李四"

​		'':	单引号：回转仪特殊字符，特殊字符最终只是一个普通的字符串数据；

​				name:'张三\n 李四'--------输出 "张三\n李四"		

#### 对象、Map（属性和值）也就是键值对

​		k: v:

​				对象还是k: v形式

```yaml
People:
	name: 张三
 	age: 20
```

行内写法:

```yaml
People: {name: zhangsan,age: 20}
```

#### 数组（list，set）:

用-值表示数组中的一个元素

```yaml
Pets: 
 - cat
 - dog
 - pig
```

行内写法：

```yaml
Pets: [cat,dog,pig]
```

### 3.配置文件的注入

**配置文件**

```yaml
people:
  name: zhangsan
  age: 12
  birth: 2019/12/12
  pet:
    name: xxx
    age: 1
  maps: {k1: v1,k2: v2}
  lists:
    - g1
    - g2
    - g3
```

**javaBean**

```java
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性
 * 和配置文件中相关的配置进行绑定；
 * prefix = "people"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能使用容器提供的@ConfigurationProperties功能；
 *
 */
@Component
@ConfigurationProperties(prefix = "people")
public class People {
    private String name;
    private Integer age;
    private Date birth;
    private Pet pet;
    private Map<String,Object> maps;
    private List<Object> lists;
    省略setter和getter方法；
}
```

我们可以导入配置文件处理器，以后编写就有提示了，

```xml
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
     <optional>true</optional>
</dependency>
```

**Test**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class Springboot02ConfigApplicationTests {

    @Autowired
    People people;
    @Test
    public void contextLoads() {
        System.out.println(people);
    }
}
```

![image-20191113201308520](/images/post/image-20191113201308520.png)

<font color="red">注:</font>如果 @Autowired出错，在Application启动类中加入@ComponentScan("自己的包名")即可。

### 4.@VALUE获取值和@ConfigurationProperties获取值对比

|               | @ConfigurationProperties |  @VALUE  |
| :-----------: | :----------------------: | :------: |
|     功能      |       支持批量注入       | 单个注入 |
| 松散语法绑定  |           支持           |  不支持  |
| SpEL（#{xx}） |          不支持          |   支持   |
|  JSR303校验   |           支持           |  不支持  |
| 复杂类型封装  |           支持           |  不支持  |

![image-20191120212249793](/images/post/image-20191120212249793.png)

![image-20191120212517330](/images/post/image-20191120212517330.png)

**如果我们只是在某个业务逻辑中需要获取一下配置文件的某个值，使用@Value**

如：

```java
@RestController
public class HelloController {

    @Value("${people.name}")
    private String name;

    @RequestMapping("/hello")
    public String hello(){
        return "Hello "+name;
    }
}
```

![image-20191120213350276](/images/post/image-20191120213350276.png)

**如果我们专门编写了一个JavaBean来和配置文件进行映射，就直接使用@ConfigurationProperties。**

### 5.@PropertySource和@ImportResource

@PropertySource：加载指定的配置文件

```java
//告诉SpringBoot配置文件要去类路径下的People.properties去找people
@PropertySource(value = "classpath:People.properties")
@Component
@ConfigurationProperties(prefix = "people")
//@Validated
public class People {
//    @Value("${people.name}")
//    @Email
    private String name;
    private Integer age;
    private Date birth;
    private Pet pet;
```

@ImportResource:导入Spring的配置文件，让配置文件中的内容生效。 

​	Springboot中没有Spring的配置文件，我们自己编写的配置文件，不会自动识别，所以如果想让Spring的配置文件生效加载进来，就需要把**@ImportResource**标注在主配置类上。

```java
@ComponentScan("com.cj")
@SpringBootApplication
@ImportResource(locations = {"classpath:beans.xml"})
public class Springboot02ConfigApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot02ConfigApplication.class, args);
    }

}
```

SpringBoot推荐给容器中添加组件的方式：**全注解模式**

- 配置类->Spring的配置文件

Spring的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="helloService" class="com.cj.service.HelloService"></bean>
</beans>
```

配置类

- 使用@Bean给容器中添加组件

```java
/**
 * @Configuration:告诉SpringBoot这个一个配置类，用来代替之前的Spring配置文件
 */
@Configuration
public class MyApplicationConfig {
    /**
     * @return 将方法的返回值添加到容器中，容器中组件的默认id为方法名
     */
    @Bean
    public HelloService helloService(){
        System.out.println("MyApplicationConfig配置类@Bean给容器中添加了组件");
        return new HelloService();
    }
}
```

## 3.application.properties写法

```properties
people.name=李四
people.age=18
people.birth=2019/11/13
people.lists=a,b,c
people.maps.key1=v1
people.maps.key2=v2
people.pet.name=dog
people.pet.age=1
```

![image-20191113203853099](/images/post/image-20191113203853099.png)

<font color="red">注:</font>如果出现中文乱码，按住ALT+Ctrl+S找到file encoding，将properties的编码设置为utf-8，勾选Transparent native-to-ascii conversion

![image-20191113204156535](/images/post/image-20191113204156535.png)

如果同时存在yaml和properties

![image-20191113204316810](/images/post/image-20191113204316810.png)

可以看出2个同时存在的时候，properties的优先级大于yaml，相同的key会被覆盖，剩余的会被合并。



## 4.配置文件占位符

### 1.随机数${xxx}

> random.int
>
> random.int[1-10]
>
> random.long
>
> random.int(10)
>
> random.value
>
> random.uuid

```properties
people.name=李四${random.uuid}
people.age=${random.int[4,5]}
people.birth=2019/11/13
people.lists=a,b,c
people.maps.key1=v1
people.maps.key2=v2
people.pet.name=${people.maps.key1}_dog_${people.love:Upet}
people.pet.age=1
```

特别注意的是：${people.love:Upet}->>>>>>people中没有定义love属性值，可以在其后面加“**:**”进行设置默认值。

## 5.Profile(多环境支持)

### 1.多Proflie文件

在编写主配置文件的时候，文件名可以是application-{profile}.properties/yml

若存在多个主配置文件，默认使用application.properties

### 2.yml支持多文档块方式

### 3.激活使用Profile模式

1、在主配置文件中指定要激活哪个配置文件--------使用spring.profiles.active={profile}

