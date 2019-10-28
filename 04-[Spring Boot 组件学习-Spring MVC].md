# [Spring Boot [组件学习-Spring MVC\]](https://segmentfault.com/a/1190000008622818)





## 导读：

在上篇文章中对Spring 核心容器等做了一个简单的介绍，希望可以帮助你对Spring 有一个简单的理解，这篇文章主要围绕Spring MVC 展开，在最短的时间内帮助你对Spring MVC有个简单的印象，在结尾附有学习资料。

## Spring MVC:

### 什么是MVC?

![MVC](https://segmentfault.com/img/bVuwov)

MVC(Model,View,Controller)是一种设计思想,可以帮助我们更好的组织软件的结构。
这个模式认为，程序不论简单或复杂，从结构上看，都可以分成三层。

- 最上面的一层，是直接面向最终用户的"视图层"（View）。它是提供给用户的操作界面，是程序的外壳。
- 最底下的一层，是核心的"数据层"（Model），也就是程序需要操作的数据或信息。
- 中间的一层，就是"控制层"（Controller），它负责根据用户从"视图层"输入的指令，选取"数据层"中的数据，然后对其进行相应的操作，产生最终结果。

这三层是紧密联系在一起的，但又是互相独立的，每一层内部的变化不影响其他层。每一层都对外提供接口（Interface），供上面一层调用。这样一来，软件就可以实现模块化，修改外观或者变更数据都不用修改其他层，大大方便了维护和升级。
引之 阮一峰的[《谈谈MVC模式》](http://www.ruanyifeng.com/blog/2007/11/mvc.html)

**了解更多（阅读其他）:**
[MVC - 维基百科](https://zh.wikipedia.org/wiki/MVC)
[如何理解Web应用程序的MVC模型？ - 知乎](https://www.zhihu.com/question/27897315)
[MVC，MVP 和 MVVM 的图示 - 阮一峰](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

### 它有哪些优点?

- 提高我们的开发效率
- Spring MVC作为Spring 自家的项目可以与Spring无缝结合
- 相比于Struts2更加安全
- 注解式声明,做到所见即所得

**了解更多（阅读其他）：**
[Spring mvc和SSH如何取舍？](https://www.zhihu.com/question/22014461)

### 快速上手：

以一个简单的UserController 为例演示对用户的CRUD操作：

```
@RestController
@RequestMapping("api/user")
public class UserController {

    @RequestMapping(method = RequestMethod.POST)
    public HttpResponse<String> save(User user) {
        return HttpResponse.OK;
    }

    @RequestMapping(value = "/{userId}", method = RequestMethod.DELETE)
    public HttpResponse<String> delete(@PathVariable Long userId) {
        return HttpResponse.OK;
    }

    @RequestMapping(value = "/userId", method = RequestMethod.PUT)
    public HttpResponse<String> update(@PathVariable("userId") User user, @RequestBody User news) {
        return HttpResponse.OK;
    }

    @RequestMapping(value = "/userId", method = RequestMethod.GET)
    public HttpResponse<User> get(@PathVariable("userId") User user) {
        return new HttpResponse<>(user);
    }

}
```

**注解说明：**

- @RestController ：它继承自@Controller注解。4.0之前的版本，Spring MVC的组件都使用@Controller来标识当前类是一个控制器servlet，相比于 @Controller 一般常用于开发REST服务，用于返回json数据。

- @RequestMapping ：@RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

  ```
  RequestMapping注解有六个属性，下面我们把她分成三类进行说明。
  1、 value， method；
  value：     指定请求的实际地址，指定的地址可以是URI Template 模式（后面将会说明）；
  method：  指定请求的method类型， GET、POST、PUT、DELETE等；
  
  2、 consumes，produces；
  consumes： 指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;
  produces:    指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；
  
  3、 params，headers；
  params： 指定request中必须包含某些参数值是，才让该方法处理。
  headers： 指定request中必须包含某些指定的header值，才能让该方法处理请求。
  ```

- @PathVariable :将路径值绑定在对象上 ，即 someUrl/{paramId}, 这时的paramId可通过 @Pathvariable注解绑定它传过来的值到方法的参数上。

- @RequestBody 接收 浏览器传来的json对象转化为实体对象

## 学习资料收集：

官方资料：
[Web MVC framework 4.2.4.RELEASE](http://docs.spring.io/spring-framework/docs/4.2.4.RELEASE/spring-framework-reference/html/mvc.html)
Github上的翻译：
[Spring MVC官方4.2.4.RELEASE版本的文档](https://github.com/linesh-simplicity/translation-spring-mvc-4-documentation/tree/master/publish)
国内博客：
[史上最全最强SpringMVC详细示例实战教程](http://www.cnblogs.com/sunniest/p/4555801.html)
[跟开涛学SpringMVC笔记](http://sishuok.com/forum/blogCategory/showByCategory.html?categories_id=101&user_id=2)
视频资料：
[尚硅谷佟刚SpringMVC 4.X视频教程](http://www.icoolxue.com/album/show/175)

## 结尾：

学习任何东西都是循循渐进的，这篇文章只是对Spring MVC 一些常用注解的使用，建议还是多看一些官方资料，或关注大牛的博客，从而帮助对它有更深的理解。

## 参考资料：

[@RequestParam @RequestBody @PathVariable 等参数绑定注解详解](http://blog.csdn.net/walkerjong/article/details/7946109/)
[@RequestMapping 用法详解之地址映射](http://blog.csdn.net/walkerjong/article/details/7994326)
[Spring4.0系列3-@RestController](http://wiselyman.iteye.com/blog/2002446)