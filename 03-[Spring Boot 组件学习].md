# [Spring Boot [组件学习-Spring\]](https://segmentfault.com/a/1190000008585454)



## 导读：

------

在上篇文章的结尾提到了Spring Boot 提供了一系列的框架整合（Starter POMs)帮助我们提升开发效率,但是这并不意味着我们不需要学习这些框架，反而更需要去学习，通过学习这些框架可以使我们更好的去解决生产中遇到的问题。 在这篇文章中主要以Spring Framework作为起步点，带你快速了解Spring Framework (下面将以 Spring 代指 Spring Framework)。

## Spring Framework：

### Spring 是什么？

------

**简要介绍：**
Spring 框架是一个开源的 Java 平台，它为容易而快速的开发出耐用的 Java 应用程序提供了全面的基础设施。
Spring 框架最初是由 Rod Johnson 编写的，并且 2003 年 6 月首次在 Apache 2.0 许可下发布。

**详细介绍：**
Spring Framework 是一个开源的Java／Java EE全功能栈（full-stack）的应用程序框架，以Apache许可证形式发布，也有.NET平台上的移植版本。该框架基于 Expert One-on-One Java EE Design and Development（ISBN 0-7645-4385-7）一书中的代码，最初由Rod Johnson和Juergen Hoeller等开发。Spring Framework提供了一个简易的开发方式，这种开发方式，将避免那些可能致使底层代码变得繁杂混乱的大量的属性文件和帮助类。

引自：[维基百科](https://zh.wikipedia.org/wiki/Spring_Framework)

### Spring 有什么好处？

------

Spring 框架提供约 20 个模块，可以根据应用程序的要求来使用，从而提高开发效率。
查看更多介绍：
[Spring IoC有什么好处呢？ 知乎](https://www.zhihu.com/question/23277575)
[spring 好处与优点](http://www.cnblogs.com/hoobey/p/6032538.html)
[Spring的作用到底体现在哪里](https://www.oschina.net/question/97796_36643)

### Spring 怎么用？

------

**快速一览：**
Spring 的体系结构：[体系结构介绍描述](http://wiki.jikexueyuan.com/project/spring/architecture.html)
![图片描述](https://segmentfault.com/img/bVKbmM?w=408&h=354)

**快速上手Spring：**

经济学中有个概念叫做[2 8 定理](https://zh.wikipedia.org/wiki/帕雷托法则)，在我们这篇文章中我们也以这个定理作为指导思想，列举Spring中最常用的20%的配置与注解。
在Spring中最核心的两个概念是IOC 与 AOP，我们将分别以它两个为例列举出常用的注解，在开始之前我们先了解一下[Spring IOC 的概念](http://www.importnew.com/14751.html) 、 [AOP的概念](http://shouce.jb51.net/spring/aop.html)，了解完这些概念之后我们开始以一个简单的Hello World 为例：

**要求：**

- 将Spring导入项目

**1.Spring 上下文的配置类：**

```java
//自定义的bean类型
public class Hello {

    private String hello;
    
    /**省略get/set**/
}

//配置上下文
@Configuration
public class Configer {

    //注册Hello 示例
    @Bean
    public Hello getHello(){
        return new Hello();
    }
    
}
```

**2.获取Spring 上下文中的Bean:**

```java
public class MainApp {

    public static void main(String[] args) {
        //创建Spring 上下文对象
        ApplicationContext applicationContext =   new AnnotationConfigApplicationContext(Configer.class);
        //获取Spring 上下文对象中的 Hello的示例
        Hello hello = applicationContext.getBean(Hello .class);
        hello.setHello("Hello World");
        System.out.println(hello.getHello());
    }
}
```

**控制台信息：**

```
Hello World
```

**一.Spring IOC 中常用的注解：**
**注册上下文**
属性基本注解：

- **@Bean :用于表示创建的bean示例**

**类级别注解：**

- **@Configuration 配置上下文**
- **@Service用于标注业务层组件、**
- **@Controller用于标注控制层组件（如struts中的action）、**
- **@Repository用于标注数据访问组件，即DAO组件。**
- **@Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。 这四种注解仅仅是角色不同，但本质都一样。**

**用于自动装配**
字段及方法注解 ：

- **@Autowired 注解可以应用到 bean 属性的 setter 方法，非 setter 方法，构造函数和属性。**
- **@Resource 注解可以应用到 bean 属性的 setter 方法。**

**二.AOP中常用的注解:**
为列方便理解举一个简单的例子：

```java
@Component
@Aspect
public class MyAdvice {

    @Pointcut("execution(* cn.sunxyz.spring.aop.*Service.*(..))") // expression
    private void businessService() {
    }

    // 在一个方法执行之前，执行通知。
    @Before("businessService()")
    public void doBeforeTask() {
        System.out.println("doBeforeTask.");
    }

    // 在一个方法执行之后，不考虑其结果，执行通知。
    @After("businessService()")
    public void doAfterTask() {
        System.out.println("doAfterTask.");
    }

    // 在一个方法执行之后，只有在方法成功完成时，才能执行通知。
    @AfterReturning(pointcut = "businessService()", returning = "retVal")
    public void doAfterReturnningTask(JoinPoint joinPoint, Object retVal) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("doAfterReturnningTask " + methodName + " return with " + retVal);
    }

    // 在一个方法执行之后，只有在方法退出抛出异常时，才能执行通知
    @AfterThrowing(pointcut = "businessService()", throwing = "ex")
    public void doAfterThrowingTask(JoinPoint joinPoint, Exception ex) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("doAfterThrowingTask " + methodName + " occurs exception: " + ex);
    }

    // 在建议方法调用之前和之后，执行通知。
    @Around("businessService()")
    public Object doAroundTask(ProceedingJoinPoint jpoint) {
        Object result = null;
        String methodName = jpoint.getSignature().getName();
        // 执行目标方法
        try {
            // 前置通知
            System.out.println("The method " + methodName + " begins with " + Arrays.asList(jpoint.getArgs()));
            result = jpoint.proceed();
            // 返回通知
            System.out.println("The method " + methodName + " ends with " + Arrays.asList(jpoint.getArgs()));
        } catch (Throwable e) {
            // 异常通知
            System.out.println("The method " + methodName + " occurs expection : " + e);
            throw new RuntimeException(e);
        }
        // 后置通知
        System.out.println("The method " + methodName + " ends");
        return result;

    }
}
```

类级注解：

- @Aspect 声明这是一个切面对象
  方法级注解：
- @Pointcut 声明一个切点规则
- @Before 方法执行之前，执行通知。
- @After 方法执行之后，不考虑其结果，执行通知。
- @AfterReturning 方法执行之后，只有在方法成功完成时，才能执行通知
- @AfterThrowing 方法执行之后，只有在方法退出抛出异常时，才能执行通知
- @Around 在方法调用之前和之后，执行通知 等价于上面四个相加

想要了解更多详细信息 可以查看下面的学习资料。

## 学习资料：

------

官方文档：
[Spring Framework Reference Documentation -4.3.6.RELEASE](http://docs.spring.io/spring/docs/4.3.6.RELEASE/spring-framework-reference/html/)
学习教程：
[Spring 教程 - 极客学院 基于spring 4.1.6版](http://wiki.jikexueyuan.com/project/spring/)
[Spring 教程 - 简书 基于spring 4.1.6版](http://www.jianshu.com/p/bd87d5507f5e)
[Spring初学快速入门 - 一百教程网 基于spring 4.0.4版](http://www.yiibai.com/spring/spring-tutorial-for-beginners.html)

## 结语：

------

Spring 作为一个优秀的开发框架帮助我们提高了开发效率,而且其中的IOC 与 AOP的思想可以帮助我们将项目构建的更健壮，在下一篇文章中将简要的介绍一下Spring MVC 常用的注解。