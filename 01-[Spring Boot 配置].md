# [Spring Boot [配置-上篇\]](https://segmentfault.com/a/1190000008568594)





### 导读：

------

在上篇文章学习到了如何搭建一个简单的Spring Boot的项目，这篇文章我们主要围绕Spring Boot的外部化文件展开，

### 快速开始：外部化配置

------

首先是一个典型的Spring Boot的项目结构如下

```yaml
com
 +- example
     +- myproject
         +- Application.java
         |
         +- domain
         |   +- Customer.java
         |   +- CustomerRepository.java
         |
         +- service
         |   +- CustomerService.java
         |
         +- web
             +- CustomerController.java                 
```

然后我们所有的资源文件都统一放在resources目录下，结构如下:

```yaml
static
 +- css
 |   +- style.css
 |   +- common.css
 +- js
 |   +- index.js
 +- img
     +- img.png
     
templates
 +- index.html
 |   
 +- pages
 |   +- page.html
```

**我们所有的配置文件都默认放在在resources目录下**

#### 1.自定义banner:

我们只需要在Spring Boot工程的/src/main/resources目录下创建一个banner.txt文件，然后将ASCII字符画复制进去，就能替换默认的banner了。这是一个自定义的banner.txt文件

```
${AnsiColor.BRIGHT_GREEN}
  ______                _                  ______
 / _____)              (_)                (____  \                  _
( (____   ____    ____  _  ____    ____    ____)  )  ___    ___   _| |_
 \____ \ |  _ \  / ___)| ||  _ \  / _  |  |  __  (  / _ \  / _ \ (_   _)
 _____) )| |_| || |    | || | | |( (_| |  | |__)  )| |_| || |_| |  | |_
(______/ |  __/ |_|    |_||_| |_| \___ |  |______/  \___/  \___/    \__)
         |_|                     (_____|

${AnsiColor.BRIGHT_RED}
Application Version: ${application.version}${application.formatted-version}
Spring Boot Version: ${spring-boot.version}${spring-boot.formatted-version}
```

在banner.txt中可以使用如下的变量：

- ${AnsiColor.BRIGHT_RED}：设置控制台中输出内容的颜色
- ${application.version}：用来获取MANIFEST.MF文件中的版本号
- ${application.formatted-version}：格式化后的${application.version}版本信息
- ${spring-boot.version}：Spring Boot的版本号
- ${spring-boot.formatted-version}：格式化后的${spring-boot.version}版本信息

打印效果图：
![图片描述](https://segmentfault.com/img/bVJ7aP?w=735&h=215)

**备注：**
banner的字符画的生成工具：

- http://patorjk.com/software/taag
- [http://www.network-science.de...](http://www.network-science.de/ascii/)
- [http://www.degraeve.com/img2t...](http://www.degraeve.com/img2txt.php)

#### 2.YAML文件

**什么是YAML:**
YAML是一个可读性高，用来表达数据序列的格式。YAML参考了其他多种语言，包括：C语言、Python、Perl，并从XML、电子邮件的数据格式（RFC 2822）中获得灵感。Clark Evans在2001年首次发表了这种语言[1]，另外Ingy döt Net与Oren Ben-Kiki也是这语言的共同设计者[2]。目前已经有数种编程语言或脚本语言支持（或者说解析）这种语言。引自[维基百科](https://zh.wikipedia.org/wiki/YAML)
**为什么使用YML:**
在以往的项目中一写关于数据库之类的配置通常都会写在Properties中，YAML的作用与Properties类似都是用来配置一些属性值的，但是YAML相比于Properties要更加简洁，在项目中推荐使用YAML代替Properties，**默认名称为application.yml**。
**Spring Boot 中的yml:**
Spring框架提供两个便利的类用于加载YAML文档，YamlPropertiesFactoryBean会将YAML作为Properties来加载，YamlMapFactoryBean会将YAML作为Map来加载。
示例：

```yaml
environments:
    dev:
        url: http://dev.bar.com
        name: Developer Setup
    prod:
        url: http://foo.bar.com
        name: My Cool App
```

上面的YAML文档会被转化到下面的属性中：

```properties
environments.dev.url=http://dev.bar.com
environments.dev.name=Developer Setup
environments.prod.url=http://foo.bar.com
environments.prod.name=My Cool App
```

**使用：**
YAML列表被表示成使用[index]间接引用作为属性keys的形式，例如下面的YAML：

```yaml
my:
   servers:
       - dev.bar.com
       - foo.bar.com
```

将会转化到下面的属性中:

```properties
my.servers[0]=dev.bar.com
my.servers[1]=foo.bar.com
```

使用Spring DataBinder工具绑定那样的属性（这是**@ConfigurationProperties**做的事），你需要确定目标bean中有个java.util.List或Set类型的属性，并且需要提供一个setter或使用可变的值初始化它，比如，下面的代码将绑定上面的属性：

```java
@Component
@ConfigurationProperties(prefix="my")
public class Config {
    private List<String> servers = new ArrayList<String>();
    public List<String> getServers() {
        return this.servers;
    }
}
```

一个配置数据库连接的示例：

```
spring:
  datasource:
    platform: mysql
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&failOverReadOnly=false&useSSL=false
    username: root
    password: root
```

**数据的松散绑定：**

```
@Component
@ConfigurationProperties(prefix="person")
public class ConnectionSettings {
    private String firstName;
}
```

下面的属性名都能用于上面的@ConfigurationProperties类：

- person.firstName 标准驼峰规则
- person.first-name 虚线表示，推荐用于.properties和.yml文件中
- PERSON_FIRST_NAME 大写形式，使用系统环境变量时推荐

了解更多的 **[常见应用属性](http://udn.yyuap.com/doc/Spring-Boot-Reference-Guide/X. Appendices/A. Common application properties.html)**
了解更多 Spring Boot 中的[yml使用](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

#### 3.日志格式化

Spring Boot在所有内部日志中使用Commons Logging，但是默认配置也提供了对常用日志的支持，如：Java Util Logging，Log4J, Log4J2和Logback。每种Logger都可以通过配置使用控制台或者文件输出日志内容。
Spring Boot默认的日志输出格式如下：

```
2014-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2014-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2014-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'h
```

默认情况下，Spring Boot只会将日志记录到控制台而不会写进日志文件。可以通过'logging.level.*=LEVEL'（'LEVEL'是TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF中的一个）设置的日志级别。如果除了输出到控制台你还想写入到日志文件，那你需要设置logging.file或logging.path属性。

下面是一个使用YML配置打印路径及级别的例子：

```
logging:
  level:
    org.hibernate: ERROR
    org.springframework : DEBUG
      
  path: /logs
  file: myapp.log
```

**日志管理**
[Spring Boot 日志记录 SLF4J](http://blog.csdn.net/catoop/article/details/50501676)
[Spring Boot日志管理](http://blog.didispace.com/springbootlog/)

### 结语：

------

在这篇文章中对Spring Boot 中常用的外置化配置做了一个简要的介绍，在下一篇文章将会介绍关于 使用配置类。

### 参考文档：

------

[新年彩蛋：Spring Boot自定义Banner](http://blog.didispace.com/spring-boot-banner/)
[《Spring Boot参考指南》-配置类](http://udn.yyuap.com/doc/Spring-Boot-Reference-Guide/III. Using Spring Boot/15. Configuration classes.html)
[Spring boot(2)-配置详解](http://www.liuhaihua.cn/archives/421177.html)







# [Spring Boot [配置-下篇\]](https://segmentfault.com/a/1190000008581657)





### 导读：

------

在上篇文章中我们了解到Spring Boot 的一些常用的外部化配置，在本篇中我们将会继续对类的配置进行了解

### 一个简单的例子[错误页面定制]:

------

在 Spring Boot 中的约定大于配置 与 自动装配使我们可以不必去像以往一样配置各个框架之间的依赖与注入，但是有时Spring Boot 提供给我们的默认配置并不能完全满足我们的需求，因此个性化的配置或者叫自定义装配内容便为我们提供了这种便利。
这里我们以一个简单的在Spring Boot 中自定义错误页面的例子做为展示：

```java
@Configuration
public class ErrorPageConfig {

    @Bean
    public EmbeddedServletContainerCustomizer embeddedServletContainerCustomizer(){
        return new MyCustomizer();
    }

    private static class MyCustomizer implements EmbeddedServletContainerCustomizer {

        @Override
        public void customize(ConfigurableEmbeddedServletContainer container) {
            container.addErrorPages(new ErrorPage(HttpStatus.FORBIDDEN, "/403"));
            container.addErrorPages(new ErrorPage(HttpStatus.NOT_FOUND, "/404"));
        }

    }

}
```

EmbeddedServletContainerCustomizer 正如名字一样 - 嵌入式Servlet容器定制器，我们通过对其方法的覆盖重新达到了自定义错误页面的效果。
当然 如果使用的是Spring MVC 的话我们也可以使用 Spring MVC 提供的@ExceptionHandler方法和@ControllerAdvice。ErrorController进行异常的捕捉与错误信息页面的定制。

**备注：**
@Configuration：表示这个类可以使用 Spring IoC 容器作为 bean 定义的来源。
@Bean ：告诉 Spring，这是一个bean对象，该对象应该被注册为在 Spring 应用程序上下文中的 bean。

### 不同框架的不同配置:

------

这里以Spring Security 作为演示：

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private CustomAuthenticationProvider customAuthenticationProvider;
    

    @Override  
    protected void configure(AuthenticationManagerBuilder auth)  
            throws Exception {   
        auth.authenticationProvider(customAuthenticationProvider);
    }  

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/reg", "/login", "/css/**", "/js/**", "/img/**", "/music/**", "/plugins/**", "/upload/**", "/api/**").permitAll()
                .anyRequest().authenticated()
                .and()
            .formLogin()
                .loginPage("/login")
                .defaultSuccessUrl("/")
                .permitAll()
                .and()
            .logout()
                .permitAll();
//      默认情况下，CSRF保护已启用。你必须配置包含_csrf令牌的所有的网页来工作。
//      你可以随时禁用CSRF保护。如果在代码中配置： 解决post请无法提交
      http
      .csrf().disable();
      //in a frame because it set 'X-Frame-Options' to 'DENY'.
          http
      .headers()
         .frameOptions()
            .sameOrigin();

    }

}
```

在一般情况下我们要对Spring Boot 集成的一些框架做些自定义配置时， 可以去实现 对应框架的XXXConfigurerAdapter，然后通过EnableXXX注解进行修饰 之后用 Configuration 注解修饰，最后对其方法进行覆盖从而到达自定义配置的目的，当然具体的配置还是需求去翻阅对应的文档说明来了解。

### 更多Spring Boot项目提供的框架整合（Starter POMs）：

------

Starter POMs是可以包含到应用中的一个方便的依赖关系描述符集合。你可以获取所有Spring及相关技术的一站式服务，而不需要翻阅示例代码，拷贝粘贴大量的依赖描述符。例如，如果你想使用Spring和JPA进行数据库访问，只需要在你的项目中包含spring-boot-starter-data-jpa依赖，然后你就可以开始了。

该starters包含很多你搭建项目，快速运行所需的依赖，并提供一致的，管理的传递依赖集。

**名字有什么含义：**所有的starters遵循一个相似的命名模式：spring-boot-starter-*，在这里*是一种特殊类型的应用程序。该命名结构旨在帮你找到需要的starter。很多IDEs集成的Maven允许你通过名称搜索依赖。例如，使用相应的Eclipse或STS插件，你可以简单地在POM编辑器中点击ctrl-space，然后输入"spring-boot-starter"可以获取一个完整列表。

下面的应用程序starters是Spring Boot在org.springframework.boot组下提供的：

- spring-boot-starter 核心 POM，包含自动配置支持、日志库和对 YAML 配置文件的支持。
- spring-boot-starter-amqp 通过 spring-rabbit 支持 AMQP。
- spring-boot-starter-aop 包含 spring-aop 和 AspectJ 来支持面向切面编程（AOP）。
- spring-boot-starter-batch 支持 Spring Batch，包含 HSQLDB。
- spring-boot-starter-data-jpa 包含 spring-data-jpa、spring-orm 和 Hibernate 来支持 JPA。
- spring-boot-starter-data-mongodb 包含 spring-data-mongodb 来支持 MongoDB。
- spring-boot-starter-data-rest 通过 spring-data-rest-webmvc 支持以 REST 方式暴露 Spring Data 仓库。
- spring-boot-starter-jdbc 支持使用 JDBC 访问数据库。
- spring-boot-starter-security 包含 spring-security。
- spring-boot-starter-test 包含常用的测试所需的依赖，如 JUnit、Hamcrest、Mockito 和 spring-test 等。
- spring-boot-starter-velocity 支持使用 Velocity 作为模板引擎。
- spring-boot-starter-web 支持 Web 应用开发，包含 Tomcat 和 spring-mvc。
- spring-boot-starter-websocket 支持使用 Tomcat 开发 WebSocket 应用。
- spring-boot-starter-ws 支持 Spring Web Services。
- spring-boot-starter-actuator 添加适用于生产环境的功能，如性能指标和监测等功能。
- spring-boot-starter-remote-shell 添加远程 SSH 支持。
- spring-boot-starter-jetty 使用 Jetty 而不是默认的 Tomcat 作为应用服务器。
- spring-boot-starter-log4j 添加 Log4j 的支持。
- spring-boot-starter-logging 使用 Spring Boot 默认的日志框架 Logback。
- spring-boot-starter-tomcat 使用 Spring Boot 默认的 Tomcat 作为应用服务器。

### 结语：

------

关于Spring Boot 常用的一些配置方式做了一个简要的介绍，从这些配置中我们也可以看到Spring Boot 为我们提供的各种默认配置的好处，当我们不需要这些默认值时，我们有两种常用的覆盖方式：
1.通过 application.yml文件进行定义。
2.通过实现对应的ConfigurerAdapter,并托管给Spring 容器来进行定义。