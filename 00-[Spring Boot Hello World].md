# [Spring Boot [Hello World\]](https://segmentfault.com/a/1190000008567731)





### 导读：

------

通过上篇文章， 我们已经了解到了 Spring Boot 作为一个Spring的脚手架， 其核心思想便是约定大于配置，通过一层层的封装让我们可以在最短的时间内搭建一个web项目，从繁琐的配置中走出来更加关注业务代码。这篇文章便以一个简单的Hello World 为例 带你走进spring Boot 的世。

### 快速上手：

------

**系统要求：**
尽管你可以在Java6或Java7环境下使用Spring Boot，通常我们建议你如果可能的话就使用Java8。
Spring Boot 默认使用了内嵌容器 支持开箱即用 。
下面让我们快速搭建一个最简单的Spring Boot 项目：
**第一步：**
创建一个maven项目
**第二步：**
引入pom配置：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.2.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

**第三步：**
创建一个类

```java
@RestController
@SpringBootApplication
public class SpringBootApplication {

    @GetMapping
    public String hello(){
        return "Hello World";
    }

    public static void main(String[] args) {
      //SpringApplication.run(xxxApplication.class,args) 固定格式
        SpringApplication.run(SpringBootApplication.class, args);
    }
}  
```

**第四步：**
运行这个main函数

查看控制台打印内容：

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.2.RELEASE)
 
 2017-03-04 15:00:19.379  INFO 6468 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
 
```

可以看到一个非常简单的Spring Boot 项目已经运行起来了， 查看打印信息会发现其运行在8080端口上，然后打开网址http://localhost:8080/ ‘Hello World’已经出现在浏览器窗口上。

```
Hello World   
```

### 结语:

------

Spring Boot 帮助我们做了大量的默认配置，使我们不必太多去关注这些细节，在下一篇文章中我会记录一下关于自定义配置的内容，Spring Boot 的配置文件 帮助我们快速开发的同时而不牺牲灵活性。

### 备注：

------

关于第三步注解的一些描述：

- @RestController:该注解是spring 4.0引入的。查看源码可知其包含了 @Controller 和 @ResponseBody 注解。我们可以将其看做对@Controller注解的增强与细分,常用来返回json格式的数据。
- @SpringBootApplication：该注解是Spring Boot 的自定义注解， 查看其源码会发现其包含了@Configuration
  @EnableAutoConfiguration @ComponentScan 这三个注解，其作用等价于：同时修饰了这三个注解，为了方便理解这里列举个简单的例子（这两个代码片段效果相同）：

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
    
```

- @GetMapping：查看其源码发现其被@RequestMapping注解修饰， 其作用等价与 RequestMapping(method =RequestMethod.GET)。

### 参考资料：

------

[Spring Boot文档](http://udn.yyuap.com/doc/Spring-Boot-Reference-Guide/I. Spring Boot Documentation/index.html)
[Spring Boot快速入门](http://blog.didispace.com/spring-boot-learning-1/)
[Spring Boot Controller](http://blog.csdn.net/catoop/article/details/50501676)