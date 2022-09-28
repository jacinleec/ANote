在学完Servlet后紧接着就需要进入Spring的大家庭了，我一开始并没有选择Spring 框架来进行学习，因为配置相当麻烦，所以现在这个轻量级框架Spring Boot.

Spring Boot总体还是 **MVC的设计模式** ，这一点和Python Django很相似，所以对于Spring Boot学习我的进度也是相当快的。主要是由于之前有Web开发基础了，同时也有一些Servlet基础。

本节主要讲述了Spring Boot配置与开发情况等，基本和Servlet技术也差不多。（毕竟人家是框架， **底层逻辑设计还是差不多的（有时间得钻研钻研源码了）** ，可惜不能用JSP了）

个人觉得 **Java Spring Boot比较多的是注解** ，而且刚开始时候配maven环境可能会有些麻烦（maven是一个包管理，一般使用阿里镜像源）不过这一点在IDEA 终极版上面直接创建Spirng 项目即可。（偷偷地说， **Python Django 框架还是比Spring Boot对新手要友好很多很多** ！）

### 目录

* [Sping Boot简介](https://editor.csdn.net/md/?articleId=126853204#Sping_Boot_9)
* [开发第一个Sping Boot程序](https://editor.csdn.net/md/?articleId=126853204#Sping_Boot_15)

  * [创建Maven工程](https://editor.csdn.net/md/?articleId=126853204#Maven_16)
  * [项目构建](https://editor.csdn.net/md/?articleId=126853204#_56)
* [Spring Boot基础配置](https://editor.csdn.net/md/?articleId=126853204#Spring_Boot_128)
* [@Spring BootApplication](https://editor.csdn.net/md/?articleId=126853204#Spring_BootApplication_129)
* [定制banner](https://editor.csdn.net/md/?articleId=126853204#banner_157)
* [Web容器配置](https://editor.csdn.net/md/?articleId=126853204#Web_196)
* [Tomcat配置](https://editor.csdn.net/md/?articleId=126853204#Tomcat_197)
* [HTTPS配置](https://editor.csdn.net/md/?articleId=126853204#HTTPS_205)
* [Properties配置](https://editor.csdn.net/md/?articleId=126853204#Properties_208)
* [整合视图层技术](https://editor.csdn.net/md/?articleId=126853204#_212)
* [整合Thymeleaf](https://editor.csdn.net/md/?articleId=126853204#Thymeleaf_214)
* [整合FreeMarker](https://editor.csdn.net/md/?articleId=126853204#FreeMarker_316)
* [小结](https://editor.csdn.net/md/?articleId=126853204#_336)
* [整合Web开发](https://editor.csdn.net/md/?articleId=126853204#Web_339)
* [返回JSON数据](https://editor.csdn.net/md/?articleId=126853204#JSON_340)
* [自定义转换器](https://editor.csdn.net/md/?articleId=126853204#_361)
* [使用Gson](https://editor.csdn.net/md/?articleId=126853204#Gson_363)
* [使用fastjson](https://editor.csdn.net/md/?articleId=126853204#fastjson_384)
* [静态资源访问](https://editor.csdn.net/md/?articleId=126853204#_431)
* [文件上传](https://editor.csdn.net/md/?articleId=126853204#_435)
* [CORS支持](https://editor.csdn.net/md/?articleId=126853204#CORS_477)
* [路径映射](https://editor.csdn.net/md/?articleId=126853204#_549)
* [配置AOP](https://editor.csdn.net/md/?articleId=126853204#AOP_565)
* [自定义首页与favicon](https://editor.csdn.net/md/?articleId=126853204#favicon_636)

# Sping Boot简介

Sping作为一个轻量级容器，得到了广泛的应用，但是其配置相当麻烦，在和第三方框架进行整合的时候代码量巨大，并且整合的代码大多是重复的。为了开发者快速上手Sping,利用Sping框框快速搭建JavaEE,Sping Boot应运而生了。

Sping Boot项目可以采用传统的方案打成war包，然后部署到Tomcat中运行。也可以直接打成jar包，这样可以通过java -jar命令启动项目了。

# 开发第一个Sping Boot程序

## 创建Maven工程

可以使用命令行创建Maven工程， **关于maven安装这里就不再赘述了** ，注意请配好maven镜像源（ **百度谷歌，注意镜像源url需要更新，有些老的博客没更新** ），然后使用cmd运行下面的命令（ **管理员操作** ），否则会报错。

**这些都比较麻烦了，但确实能了解MAVEN创建的基础，一般开发者开始还是直接创建Spring Boot项目，见下文项目构建后半部分。**

 **多提几句吧** ，maven是一个包管理，什么意思呢？在我们之前做一些java开发的时候或者servlet开发的时候，需要在IDEA导入相应的包，否则就不行。如何更好地方便管理包呢？maven就诞生了，只需要在项目pom.xml中添加依赖就可以完成包添加了，非常方便。**所以一开始使用maven，就是为了在后面引入spring-boot-web框架依赖。**

```bash
# cmd 命令创建maven项目
mvn archetype:generate -DgroupId=cn.luxh.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

命令解释：

```bash
-DgroupID :组织ID（项目包名）
-DartifactID: ArtifactID 项目名称或者模块名称
-DarchetypeArtifactId ：项目骨架
-DinteractiveMode：是否使用交互模式
```

创建好后可以直接用IDEA打开。
**同时也可以使用IDEA创建maven，这里就不再赘述了。**

记得改maven位置

![在这里插入图片描述](https://img-blog.csdnimg.cn/712f6187f22e4820a53f2213a88ee838.png)
这样就表示创建成功了。
在IDEA创建Maven工程的时候同样遇到了很多麻烦，例如需要将setting.xml改（注意这里默认的用户settings，而不是个人的）
![在这里插入图片描述](https://img-blog.csdnimg.cn/3dd90ea95a77454693b2ef9db5283425.png)
注意settings.xml中的mirror的url可能需要修改，否则无法加载：
**2022年版本的url**

```cpp
 	<mirror>
          <id>nexus-aliyun</id>
          <mirrorOf>*</mirrorOf>
          <name>Nexus aliyun</name>
          <url>https://maven.aliyun.com/repository/public</url>
       </mirror> 
```

## 项目构建

在pom.xml:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.0.RELEASE</version>
  </parent>
...
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
```

spring-boot-starter-parent是一个特殊的Starter，提供了一些Maven默认配置，也提供了依赖管理，可以使开发者在引入依赖的时候不必输入版本号，方便依赖管理。

编写启动类：
如果导入包出现红色的时候，记得清缓存：
![在这里插入图片描述](https://img-blog.csdnimg.cn/62c513d848ea4818865ec4bdcd07fa25.png)
 **启动类** ：

```java
@ComponentScan
//配置包扫描才能将HelloController注册到Spring MVC容器中
@EnableAutoConfiguration 
//注解表示开启自动化配置，，开启后会自动进行Sping 和Sping MVC的配置
public class App {
    public static void main(String[] args) {
    	//通过run方法启动项目，第一个参数传入App.class告诉Sping哪个是主要组件，第二个参数是运行时输入的其他参数。
        SpringApplication.run(App.class,args);
    }
}

```

**Sping MVC中的控制器**
注意注解 都没有分号的！！！

```java
@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello() {
        return "hello spring boot!";
    }
}

```

项目启动两种办法：

```java
mvn spring-boot:run
```

或者直接在App main方法直接运行：
![在这里插入图片描述](https://img-blog.csdnimg.cn/0e3da30aae954f65a8fdcbee00f0614b.png)
不过比较无语的是，不能像运行Servlet一样直接跳转浏览器，需要自己手动在浏览器输入地址，因为内嵌Tomcat，所以端口自然就是8080了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/870289dfd2fa4dafb882fbbe5fe54f48.png)
打包启动（项目部署）
spring boot可以直接打成jar包运行。（后续将项目部署再讲）
**注意IDEA（终极版）可以直接创建一个Spring Boot项目**
![在这里插入图片描述](https://img-blog.csdnimg.cn/cd53fe2646004117bb6b447f23455014.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8532b465c4514fb994d35e8dae39b00d.png)
这是目前使用IDEA直接创建Spring Boot项目：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a027987c47aa45309bff80d34467e249.png)

# Spring Boot基础配置

## @Spring BootApplication

根据前面的理解，此注解是加在项目启动类上的，实际上是一个组合注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
*@SpringBootConfiguration
*@EnableAutoConfiguration
*@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```

核心注解就是三个（已用*标注）
第一个定义如下：

```java
@Configuration
public @interface SpringBootConfiguration
```

原来就是@Configuration，开发者可以在这个类配置Bean.

## 定制banner

Spring Boot在启动时候会打印一个banner，例如：
![在这里插入图片描述](https://img-blog.csdnimg.cn/9baec6fb92e640e88e952e9e83fc29c9.png)
这个banner是可以定制的，在resources目录下建一个banner,txt文件，在这个文件中写入打印出来。
一般都是使用这个banner(至于为什么你猜？这里就分享一波了）

```java
${AnsiColor.BRIGHT_YELLOW}
////////////////////////////////////////////////////////////////////
//                          _ooOoo_                               //
//                         o8888888o                              //
//                         88" . "88                              //
//                         (| ^_^ |)                              //
//                         O\  =  /O                              //
//                      ____/`---'\____                           //
//                    .'  \\|     |//  `.                         //
//                   /  \\|||  :  |||//  \                        //
//                  /  _||||| -:- |||||-  \                       //
//                  |   | \\\  -  /// |   |                       //
//                  | \_|  ''\---/''  |   |                       //
//                  \  .-\__  `-`  ___/-. /                       //
//                ___`. .'  /--.--\  `. . ___                     //
//              ."" '<  `.___\_<|>_/___.'  >'"".                  //
//            | | :  `- \`.;`\ _ /`;.`/ - ` : | |                 //
//            \  \ `-.   \_ __\ /__ _/   .-` /  /                 //
//      ========`-.____`-.___\_____/___.-`____.-'========         //
//                           `=---='                              //
//      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        //
//            佛祖保佑       永不宕机      永无BUG                　　//
////////////////////////////////////////////////////////////////////
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/027d3c8f8a7544c49d1bb59598157df5.png)
当然了，也可以关闭bannerMode
进入到启动类的main方法,这样就没有了。

```java
 SpringApplicationBuilder builder = new SpringApplicationBuilder(DemoApplication.class);
        builder.bannerMode(Banner.Mode.OFF).run(args);
```

## Web容器配置

### Tomcat配置

Spring Boot项目中，内置了Tomcat,Jetty,Netty等容器。当开发者添加了spring-boot-starter-web依赖后，默认使用Tomcat作为Web容器，如果对Tomcat做进一步配置，可以在application.properties配置（resoures）：

```java
server.port = 8081 # 配置了端口号
server.error.path =/error # 配置了当项目出错时候跳转页面
server.servlet.context-path=/chpter02 # 表示项目名称，不配置时默认为/，如果配置了需要加上路径
```

### HTTPS配置

由于HTTPS具有良好的安全性，在开发中得到了广泛的应用。对于个人开发者来说，一个HTTPS证书还是有点贵，虽然一些云服务厂商提供了免费的HTTPS证书。不过在jdk中提供了一个Java数字证书管理工具keytool，在\jdk\bin目录。（初学者了解就可以了，不用深究）

## Properties配置

虽然Spring Boot采用了大量的自动化配置，但是对开发者来说，不可避免地会有一些需要自己手动配置，承载这些自定义的文件就是resources/application.properties(同样也可以使用YAML)
目前新版本中不存在优先级说法了，（我也没找到多个properties）

# 整合视图层技术

在目前企业级开发中，前后端分离是趋势，但是视图层技术还有一席之地。Spring Boot对视图层提供了很好的支持，官方推荐使用的模板引擎是Thymeleaf，不过像FreeMarker也支持，JSP技术这里不推荐使用了。

## 整合Thymeleaf

支持HTML原型。
添加依赖

```java
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

配置控制器
Book实体类，然后在Controller返回ModelAndView，代码如下：

```java
package com.example.demo.test;

import org.springframework.stereotype.Controller;

public class Book {
    private   Integer id;
    private   String name;
    private  String author;

    public  Integer getId() {
        return id;
    }
    public  String getName() {
        return name;
    }
    public String getAuthor() {
        return author;
    }

    public void setId(Integer id) {
        this.id = id;
    }
    public void setName(String name) {
        this.name = name;
    }
    public  void setAuthor(String author) {
        this.author = author;
    }
}

```

```java
@Controller
public class BookController {
    @GetMapping("/books")
    public ModelAndView books() {
        List<Book> books = new ArrayList<>();
        Book b1 = new Book();
        b1.setId(1);b1.setName("B1");b1.setAuthor("JA");
        Book b2 = new Book();
        b2.setId(2);b2.setName("B2");b2.setAuthor("MA");

        books.add(b1);
        books.add(b2);

        ModelAndView mv = new ModelAndView();
        mv.addObject("books",books);
        mv.setViewName("books");
        return mv;
    }
}

```

注意创建返回ModelAndView，设置视图名为books,返回数据为所创建的List集合。
创建视图：注意要导入Thymeleaf名称空间。

```html
<html lang="en"  xmlns:th ="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
  <table border="1" >
      <tr>
          <td>ID</td>
          <td>NAME</td>
          <td>AUTHOR</td>
      </tr>
      <tr th:each = "book:${books}">
          <td th:text="${book.id}"></td>
          <td th:text="${book.name}"></td>
          <td th:text="${book.author}"></td>
      </tr>
  </table>
</body>
</html>
```

这样就可以在8080/books访问了，但是我遇到了这个问题
![在这里插入图片描述](https://img-blog.csdnimg.cn/53e1459c40c349efb123a42bc9474a58.png)
后来解决后了解到，在我创建Spring Boot项目时候，没有把这个文件夹放在之前创建的application中
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca2285b38c2948919fd277658abb6814.png)

## 整合FreeMarker

FreeMarker是一个古老的模板引擎，可以用在Web环境或者非Web环境中。与Thymeleaf不同，FreeMarker需要经过解析才能在浏览器展示出来。其不仅用来配置HTML页面模板，也可以作为电子邮件模板等。Spring Boot对FreeMarker整合提供了支持。

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
```

配置控制器和上述一样，这里不再重复。
创建视图：

```html
<td>${book.id}</td>
        <td>${book.name}</td>
        <td>${book.author}</td>
```

可以发现的是，和上述Thy模板一样

## 小结

用到其他模板技术时候，整合方式和上述两种类似。如果开发者使用的是目前流行的前后端分离技术，那么在开发过程中不需要整合视图层技术，后端直接提供接口即可。

# 整合Web开发

## 返回JSON数据

JSON是目前主流的前后端数据传输方式。Spring MVC使用消息转换器HttpMessageConverter对JSON的转换提供了很好的支持。需要添加Web依赖。Web依赖中默认加入了jackson-databind作为JSON处理器，此时不需要添加额外的JSON处理器
使用之前的例子，

```java
@RestController
public class BookController {
  
    @GetMapping("/book")
    public Book book() {
        Book b1 = new Book();
        b1.setId(3);
        b1.setName("MK");
        b1.setAuthor("JJ");
        return b1;
    }
}
```

输入8080/book 则可以返回一个JSON数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/6f23478cdd7f4a62bcd8f0f8b2536000.png)
这是Spring Boot自带的处理方式，如果采用这种方式，那么对于字段忽视等都可以通过注解来解决。

## 自定义转换器

常见的JSON处理器除了jackson-databind之外，还有Gson和fastjson。

### 使用Gson

Gson是Google一个开源的JSON解析框架。需要加入Gson依赖，同时要**去除默认的jackson-databind**

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>com.fasterxml.jackson.core</groupId>
                    <artifactId>jackson-databind</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>
```

Gson依赖添加成功后，可以像使用jackson-databind那样直接使用Gson.如果要对日期数据进行格式化，需要自定义HttpMessageConverter。

### 使用fastjson

fastjson 是阿里巴巴的一个开源JSON解析框架，是目前JSON解析速度最快的开源框架。这个也可以集成到Spring Boot 中，不同于Gson fastjson继承之后并不能立马使用，需要开发者提供相应的HttpMessageConverter后才能使用，集成fastjson步骤如下：
首先除去jackson-databind依赖，引入其依赖：

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>com.fasterxml.jackson.core</groupId>
                    <artifactId>jackson-databind</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.36</version>
        </dependency>
```

配置fastjson的HttpMessageConverter:

```java
@Configuration
public class MyFastJsonConfig {
    @Bean
    FastJsonHttpMessageConverter fastJsonHttpMessageConverter () {
        FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter();
        FastJsonConfig config = new FastJsonConfig();
        config.setDateFormat("yyyy-MM-dd");
        config.setCharset(Charset.forName("UTF-8"));
        config.setSerializerFeatures(
                SerializerFeature.WriteClassName,
                SerializerFeature.WriteMapNullValue,
                SerializerFeature.PrettyFormat,
                SerializerFeature.WriteNullListAsEmpty,
                SerializerFeature.WriteNullStringAsEmpty
        );
        converter.setFastJsonConfig(config);
        return converter;
    }
}
```

自定义MyFastJsonConfig,完成了对FastJsonHttpMessageConverter Bean 的提供。
BookControl和上节一样，输入8080/book
![在这里插入图片描述](https://img-blog.csdnimg.cn/302b654795724f25b15658161a90a422.png)

## 静态资源访问

在Spring MVC中，对于静态资源需要开发者手动配置静态资源过滤。Spring Boot对此提供了自动化配置，可以简化静态资源过滤配置。
一般使用IDEA 创建Spring Boot的时候，默认会创建classpath:/static目录，静态资源一般就放在这个目录下面就可以。
（一般使用默认就可以了）

## 文件上传

文件上传一共涉及两个组件，分别是CommonsMultipartResovler,StandardServletMultipartResolver。
下面来实现一共单文件上传：
resources/static建立upload.html

```html
<form action="/upload" method="post" enctype="multipart/form-data">
      <input type="file" name="uploadFile" value ="选择文件">
      <input type="submit" value="上传">
  </form>
```

这是一个简单的文件上传页面，上传接口是/upload，注意请求方法是post,enctype是multypart/form-data
接着创建文件上传处理接口：

```java
@RestController
public class FileUploadController {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd/");
    @PostMapping("/upload")
    public String upload(MultipartFile uploadFile, HttpServletRequest req) {
        String realpath = req.getSession().getServletContext().getRealPath("/uploadFile/");
        String format = sdf.format(new Date());
        File folder = new File(realpath + format);
        if(!folder.isDirectory()) {
            folder.mkdirs();
        }
        String oldName = uploadFile.getOriginalFilename();
        String newName = UUID.randomUUID().toString()+oldName.substring(oldName.lastIndexOf("."),oldName.length());
        try{
            uploadFile.transferTo(new File(folder,newName));
            String filePath = req.getScheme()+"://" +req.getServerName()+":"+req.getServerPort()+"/uploadFile/"+format+newName;
            return filePath;
        }catch (IOException ex) {
            ex.printStackTrace();
        }
        return "Fail!";
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a437762818f4480993efb22d012a47af.png)
注意浏览器地址不要写错了，特别是要加上Html。

## CORS支持

CORS是W3C制定的一种跨域资源共享技术标准，其目的就是为了解决前端的跨域请求。最常见的前端跨域请求解决方案是JSONP，但是其只支持GET请求，这是很大的缺陷。CORS则支持多种HTTP请求方法。
首先添加Web依赖。
添加一个BookController控制器：
提供了2个接口，分别是添加接口，另一个是删除接口。同时我这里使用的是注解配置跨域
CrossOrigin中的value表示支持的域，maxAge表示探测的有效期，默认是1800s.allowedHeaders表示允许的请求，*表示所有的请求头都被允许。

```java
@RestController
@RequestMapping("/book")
public class BookController {
    @PostMapping("/")
    @CrossOrigin(
            value ="http://localhost:8081",
            maxAge = 1800,
            allowedHeaders = "*"
    )
    public String addBook(String name) {
        return "receive:" +name;
    }
    @DeleteMapping("/{id}")
    @CrossOrigin(
            value = "http://localhost:8081",
            maxAge = 1800,
            allowedHeaders = "*"
    )
    public  String deleteBookByID(@PathVariable Long id) {
        return String.valueOf(id);
    }
}
```

进入测试的时候，需要先引入JQuery，注意的是不知道为什么我chrome是直接打开而不是下载的，那么我把链接放到IDM即可下载，或者你可以复制全屏也可以。 [下载链接](https://jquery.com/download/)

```html
<script src ="jquery.js"></script>

<div id = "contentDiv"></div>
<div id = "deleteResult"></div>

<input type="button" value="submit" onclick="getData()"><br>
<input type="button" value="delete" onclick="deleteData()"><br>
<script>
    function  deleteData() {
        $.ajax(
            {
                url:'http://localhost:8081/book/99',
                type: 'delete',
                success : function (msg) {
                    $("#deleteResult").html(msg);
                }
            }
        )
    }

    function getData() {
        $.ajax(
            {
                url:'http://localhost:8081/book/',
                type: 'post',
                data : {name : '三国'},
                success : function (msg) {
                    $("#contentDiv").html(msg);
                }
            }
        )
    }

</script>
```

两个普通Ajax都发送一个跨域请求：注意修改端口8081
![在这里插入图片描述](https://img-blog.csdnimg.cn/20a1a888f6934f7cbd62df74955d5604.png)

## 路径映射

一般情况下，使用了页面模板后，用户需要通过控制器才能访问页面。有些页面需要在控制器加载数据然后渲染才能显示出来，还有一些页面在控制器不需要加载数据，只是完成简单的跳转，对于这种页面，可以直接配置路径映射，提高访问速度。
例如有两个页面login.html和index.html，直接在MVC配置中重写addViewControllers方法配置映射关系即可。

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
	@Override
	public void addViewControllers(ViewControllerRegistry re) {
		re.addViewControllers("/login").setViewName("login");
		... "/index"
	}
}
```

配置完成后就可以8080/login了

## 配置AOP

AOP（面向切面编程），有一些概念需要读者了解：

1. Joinpoint(连接点) 类里面可以被增强的方法为连接点。例如：想要修改哪个方法的功能那么该方法就是一个连接点。
2. Pointcut(切入点)：对连接点进行拦截。
3. Advice（通知）
4. Aspect(切面)
5. Target(目标对象)

首先引入aop依赖：

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

```

创建切面：
@Aspect注解表示这是一个切面类：import org.aspectj.lang.annotation.*;

定义的pc1()使用了Pointcut注解，这是一个切入点定义。execution中第一个*表示方法返回任意值，第二*表示包任意类，第三个就是任意方法
before注解表示一个前置通知，该方法在目标方法执行之前执行。
After表示一个后置通知

```java
@Component
@Aspect
public class LogAspect {
    @Pointcut("execution(* com.example.demo.*.*(..))")
    public void pc1() {

    }
    @Before(value="pc1()")
    public void before(JoinPoint jp) {
        String name = jp.getSignature().getName();
        System.out.println(name + "start");
    }
    @After(value="pc1()")
    public void after(JoinPoint jp) {
        String name = jp.getSignature().getName();
        System.out.println(name +"end");
    }
    @AfterReturning(value ="pc1()" ,returning = "result")
    public void afterReturning(JoinPoint jp,Object result) {
        String name = jp.getSignature().getName();
        System.out.println(name + "方法返回"+result);
    }
    @Around("pc1()")
    public  Object around(ProceedingJoinPoint pjp ) throws Throwable {
        return pjp.proceed();
    }
}

```

配置完成后，在Controller创建接口：

```java
@RestController
public class UserController {
    @Autowired(required = false)
    UserService userService;
    @GetMapping("/getUserById")
    public  String getUserById(Integer id) {
        return userService.getUserById(id);
    }
    @GetMapping("/deleteUserById")
    public  void deleteUserById(Integer id) {
        userService.deleteUserByID(id);
    }
}

```

## 自定义首页与favicon

Spring Boot项目启动后，首先会去静态资源下查找index.html作为首页内容，如果查不到，则会查找动态Index文件。
如果想用静态的index.html作为项目首页，那么只需要在resources/static目录创建Index.html文件即可。如果要使用动态页面的，则在resources/templates目录创建index.html（Thymeleaf模板）或者index.ftl(FreeMarker模板），然后在Controller中返回逻辑视图名：

```java
@RequestMapping("/index")
public String hello() {
	return "index";
}
```

启动项目，输入8080即可看到内容了。

什么是favicon呢？就是这个网站的右边符号 （浏览器左上角的图标）。
一般都要去图片转换Ico网站进行转换。只要把文件放在static/下就可以，名字要对应。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4d4cb014c744e80ac4d9dc106c15f82.png)
运行以后就出来了这个ico (这个就是标志）对了我一开始使用Chrome进行展示的时候一直出不来（一度怀疑哪里搞错了，还翻阅了源码看，是不是放在这个static目录下），放在Edge运行就直接就可以显示了。
后来了解到，在Chrom 需要按 ctrl + f5就可以出来了（一般的f5刷新还出不来）
![在这里插入图片描述](https://img-blog.csdnimg.cn/0df6db72d35942a9894cdf9913ccabd8.png)

以上。
