本文讲述了NoSQL(非关系型数据库）与构建Restful服务等，有一点的挑战性。

基本上，到目前为止，spring boot体系讲得也差不多了，当然后续还有一系列进阶的内容：例如单元测试，缓存与安全管理等。对于这里讲到的NoSQL,后期也会再深入，这里都是最基本的用法。

同时在RESTful服务中也使用到了新工具（postman）来进行接口测试等工作，也是非常方便。

**希望以下内容对你有所帮助！**

### 目录

* [前言](https://editor.csdn.net/md/?articleId=127013097#_8)
* [整合Redis](https://editor.csdn.net/md/?articleId=127013097#Redis_17)

  * [安装Redis](https://editor.csdn.net/md/?articleId=127013097#Redis_19)
  * [配置Redis](https://editor.csdn.net/md/?articleId=127013097#Redis_43)
  * [整合Spring Boot](https://editor.csdn.net/md/?articleId=127013097#Spring_Boot_74)
  * [redis集群整合](https://editor.csdn.net/md/?articleId=127013097#redis_197)
* [整合MongoDB](https://editor.csdn.net/md/?articleId=127013097#MongoDB_202)
* [MongoDB安装](https://editor.csdn.net/md/?articleId=127013097#MongoDB_205)
* [整合Spring Boot](https://editor.csdn.net/md/?articleId=127013097#Spring_Boot_217)
* [Session 共享](https://editor.csdn.net/md/?articleId=127013097#Session__318)
* [Nginx负载均衡](https://editor.csdn.net/md/?articleId=127013097#Nginx_342)
* [JPA实现REST](https://editor.csdn.net/md/?articleId=127013097#JPAREST_377)
* [基本实现](https://editor.csdn.net/md/?articleId=127013097#_378)
* [自定义请求路径：](https://editor.csdn.net/md/?articleId=127013097#_504)
* [自定义查询方法](https://editor.csdn.net/md/?articleId=127013097#_512)
* [MongoDB 实现REST](https://editor.csdn.net/md/?articleId=127013097#MongoDB_REST_532)
* [浅谈RESTful服务](https://editor.csdn.net/md/?articleId=127013097#RESTful_573)

# 前言

NoSQL是非关系型数据库。非关系和关系型数据库存在不同点，其中最重要的是NoSQL不使用SQL作为查询语句。其数据存储可以不需要固定的表格模式，有以下分类：
1 Key/Value 键值存储。数据存储通常是无数结构的，一般被当做字符串等，但是数据加载快，典型使用场景就是处理高并发或者用于日志系统，这一类数据库有Redis，Tokyo Cabinet等。
2 列存储数据库。一般用于分布式文件系统
3 文档型数据库。和Key / Value 键值存储类似。这一类有MongoDB等。
4 图形数据库，专注于构建关系图谱。有Neo4J,DEX等。

REST 是一种Web软件架构风格，（注意不是标准）匹配或兼容这种架构的网络服务是REST服务，服务简洁且有层次。在REST中，资源是由URL指定的，对资源增删改查操作可以通过HTTP协议提供的GET,POST,PUT,DELETE等方法实现。可以利用缓存来提高响应速度，同时REST通信会话由客户端来实现。

# 整合Redis

是一个使用C编写的基于内存的NoSQL数据库，目前最流行的键值对存储数据库。

## 安装Redis

安装环境为Centos 7

```bash
wget http://download.redis.io/releases/redis-4.0.10.tar.gz

# 如果没找到命令
yum install wget
```

不过我安装的时候遇到了这个问题：
![在这里插入图片描述](https://img-blog.csdnimg.cn/53bb6b82e5d74b2dba3077bc70a62293.png)
使用 vim /etc/resolv.conf 打开配置文件
输入nameserver 8.8.8.8 以及 nameserver 8.8.4.4就可以了
![在这里插入图片描述](https://img-blog.csdnimg.cn/fda654b9daee49a5874728e83d0ce0c0.png)
首先解压下载的文件，然后进入解压目录进行编译，执行语句：

```bash
tar -zxvf redis-4.0.10.tar.gz
cd redis-4.0.10
make MALLOC=libc
make install

# 如果提示gcc 未找到命令
yum install gcc
```

## 配置Redis

安装成功后，需要进行配置，打开redis，解压src/redis.conf文件

```bash
vim redis.conf
# i 插入模式：
daemonize yes # 表示允许redis在后台启动
# bind 127.0.0.1 # 允许连接的实例地址，默认情况下只允许本地连接，将默认配置注释，外网就可以连接
requirepass ***** # 登陆的密码
protected-mode no # 关闭保护模式
```

为了远程连接上redis，需要关闭Centos防火墙，执行命令如下：

```bash
systemctl stop firewalld.service # 关闭防火墙
systemctl disable firewalld.service # 禁止防火墙开机启动
```

执行启动redis

```bash
redis-server redis.conf

redis-cli -a *** # 进入控制台，这个表示密码
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b68ccb32a9324976a9c68ac8954c9767.png)
windows版本连接就更简单了，之间去[redis-windows](https://github.com/tporadowski/redis/releases)选择zip下载就可以。
不过注意要在**cmd管理员**下运行

```bash
redis-server.exe redis.windows.conf
```

一定要保持开启才能运行项目

## 整合Spring Boot

redis的Java客户端有很多，例如Jedis ,JRedis，Spring Data Redis等。Spring Boot借助于Spring Data Redis为Redis提供了开箱即用的自动化配置，开发者只需要添加相关依赖既可。
**添加依赖：**

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>


        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

在application.properties配置redis连接信息：

```bash
spring.redis.database=0
spring.redis.host=127.0.0.1
spring.redis.port=6379
spring.redis.password=qazwsx123
spring.redis.jedis.pool.max-active=8
spring.redis.jedis.pool.max-idle=8
spring.redis.jedis.pool.max-wait=-1ms
spring.redis.jedis.pool.min-idle=0
```

创建一个实体类：

```bash
import java.io.Serializable;

/**
 * Created by sang on 2018/7/18.
 */
public class Book implements Serializable {
    private Integer id;
    private String name;
    private String author;
    //省略getter/setter


    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", author='" + author + '\'' +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }
}

```

创建BookController进行测试：

```bash
@RestController
public class BookController {
    @Autowired(
            required = false
    )
    RedisTemplate redisTemplate;
    @Autowired(
            required = false
    )
    StringRedisTemplate stringRedisTemplate;
    @GetMapping("/test1")
    public  void test1() {
        ValueOperations<String,String> ops1 = stringRedisTemplate.opsForValue();
        ops1.set("name","三国演义");
        String name = ops1.get("name");
        ValueOperations ops2 = redisTemplate.opsForValue();
        Book b1 = new Book();
        b1.setId(1);
        b1.setName("红楼梦");
        b1.setAuthor("曹雪芹");
        ops2.set("b1", b1);
        Book book = (Book) ops2.get("b1");
        System.out.println(book);
    }
}
```

不过当我运行的时候发现了这个错误：
Description:

Failed to configure a DataSource: ‘url’ attribute is not specified and no embedded datasource could be configured.

Reason: Failed to determine a suitable driver class

![在这里插入图片描述](https://img-blog.csdnimg.cn/89c9305648fd4af1a2d1d51d87ffda20.png)
解决办法在启动程序上面加：

```bash
@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
```

不过可能再次运行的时候出现8080端口被占用情况（原因可能是没有杀死程序）那么最简单的办法就是重启或者在配置修改端口为8081
输入8081/test1便可以看到控制台信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/72c9f7eb484d47b2b0452f1764bcacd0.png)

## redis集群整合

上文是单个redis实例整合，在实际项目中，开发者为了提高redis扩展性，往往搭建redis集群。
原理：所有的redis节点彼此互联，节点内部使用二进制协议优化传输速度与宽带。当一个节点挂掉后，集群超过半数的节点检测失效时才认为该节点失效。不同于Tomcat集群需要使用反向代理服务器，redis集群中的任意节点都可以直接与java客户端连接。数据分配是采用哈希槽（Hash SLOT）。
**集群中的相关知识，将在后期补充。**

# 整合MongoDB

MongoDB是一种面向文档的数据库管理系统，它是一个介于关系型数据库和非关系型数据库之间的产品，支持一种类似JSON的BSON数据格式，既可以存储简单的数据格式，也可以存储复杂的数据类型。总的来说，MongoDB是一款应用广泛的NoSQL数据库。

## MongoDB安装

windows 去此处下载：[MongoDB](https://www.mongodb.com/try/download/community)
安装完以后可以用Navicat 或者datagrip进行打开，注意此数据库一开始是没有密码的。
所以这在生产环境中非常不安全的，但是不同于关系型数据库，MongoDB每一库都有一个独立的密码，在哪一个库创建用户就要在哪一个库验证密码。要配置密码，首先创建一个用户，例如在admin库中创建一个用户：

```sql
use admin;

db.createUser({user:"jacin",pwd:"qazwsx123",roles:[{role:"readWrite",db:"test"}]})
```

对数据库test具有读写两项权限

## 整合Spring Boot

**引入依赖：**

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>


        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
        </dependency>
```

配置MongoDB,在application.properties：

```bash
spring.data.mongodb.authentication-database=admin
spring.data.mongodb.database=test
spring.data.mongodb.host=127.0.0.1
spring.data.mongodb.port=27017
spring.data.mongodb.username=jacin
spring.data.mongodb.password=******
```

创建的实体库Book和之前一样：
创建BookDao:类似于Spring Data JPA的repository定义
MongoRepository已经预定义了针对实体类的查询、添加、删除等操作，可以按照命名规则定义方法。

```bash
import org.springframework.data.mongodb.repository.MongoRepository;

import java.util.List;


public interface BookDao extends MongoRepository<Book,Integer> {
    List<Book> findByAuthorContains(String author);
    Book findByNameEquals(String name);
}

```

创建Controller:
insert方法，插入集合中的数据。

```bash
@RestController
public class BookController {
    @Autowired
    BookDao bookDao;
    @Autowired(
            required = false
    )
    MongoTemplate mongoTemplate;

    @GetMapping("/test2")
    public void test2() {
        List<Book> books = new ArrayList<>();
        Book b1 = new Book();
        b1.setId(3);
        b1.setName("围城");
        b1.setAuthor("钱钟书");
        books.add(b1);
        Book b2 = new Book();
        b2.setId(4);
        b2.setName("宋诗选注");
        b2.setAuthor("钱钟书");
        books.add(b2);
        mongoTemplate.insertAll(books); //插入数据
        List<Book> list = mongoTemplate.findAll(Book.class);
        System.out.println(list);
        Book book = mongoTemplate.findById(3, Book.class);
        System.out.println(book);
    }

    @GetMapping("/test1")
    public void test1() {
        List<Book> books = new ArrayList<>();
        Book b1 = new Book();
        b1.setId(1);
        b1.setName("朝花夕拾");
        b1.setAuthor("鲁迅");
        books.add(b1);
        Book b2 = new Book();
        b2.setId(2);
        b2.setName("呐喊");
        b2.setAuthor("鲁迅");
        books.add(b2);
        bookDao.insert(books);
        List<Book> books1 = bookDao.findByAuthorContains("鲁迅");
        System.out.println(books1);
        Book book = bookDao.findByNameEquals("朝花夕拾");
        System.out.println(book);
    }
}


```

可以使用命令db.book.find()进行查看。
![在这里插入图片描述](https://img-blog.csdnimg.cn/fa710ba40d1443f989e21b7248be6f57.png)

# Session 共享

正常情况下，HttpSession是通过Servlet容器创建来进行管理的，创建成功后，都是保存在内存中，如果开发者需要对项目横向扩展搭建集群，利用一些硬件或者软件来做负载均衡。使用Redis来方便解决问题，就是把原本存储在不同服务器上的session拿出来放在一个独立服务器上。
![在这里插入图片描述](https://img-blog.csdnimg.cn/3551584b52f043bfbbbf32b5576ae3fa.png)
需要添加redis 和session依赖：
下面创建一个controller来测试操作：

```bash
@RestController
public class HelloController {
    @Value("${server.port}")
    String port;
    @PostMapping("/save")
    public String saveName(String name, HttpSession session) {
        session.setAttribute("name", name);
        return port;
    }
    @GetMapping("/get")
    public String getName(HttpSession session) {
        return port + ":"
                + session.getAttribute("name").toString();
    }
}
```

一个save接口用来向session存储数据，还有一个get接口用来从session 获取数据，这里注入了项目启动的端口号server.port，主要区分是哪个服务器提供的服务，真正的session此时存在了redis服务器上了。

## Nginx负载均衡

本次使用nginx来进行负载均衡，环境是centos 7,关于安装与编译不再多说了。

项目完成后，将项目打成jar包上传到Centos上，然后执行如下两条命令启动项目：

```bash
nohup java -jar session-0.0.1-SNAPSHOT.jar --server.port=8080 &
nohup java -jar session-0.0.1-SHAPSHOT.jar --server.port=8081 &
```

nohup就是不挂使程序在后台运行，本次设置了2个端口，启动成功后开始配置负载均衡器了。

进入nginx修改配置文件：
/conf/nginx.conf:

```bash
upstream jacin.com {
	server 192.168.66.130:8080 weight =1;
	server 192.168.66.130:8081 weight =1;
}
server {
	listen 80;
	server_name  localhost;
	location / {
		proxy_pass http://jacin.com
		proxy_redirect default;
	}
}
```

在修改的配置中，首先配置上游服务器，即两个real server，权重都是1（意味平均分配）然后在server中设置拦截规则，将请求转发到real server上。

记得重启nginx。

经过以上步骤，便可以利用redis实现session共享的功能。

# JPA实现REST

## 基本实现

 **添加依赖** ：

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-rest</artifactId>
        </dependency>



        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>


        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.2</version>
        </dependency>
```

相关application配置如下：

```bash
server.port=8082
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url=jdbc:mysql:///jparestful
spring.datasource.username=root
spring.datasource.password=qazwsx123
spring.jpa.show-sql=true
spring.jpa.database=mysql
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL57Dialect
```

 **创建实体类** ：

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity(name = "t_book")
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String name;
    private String author;
    //省略getter/setter

    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", author='" + author + '\'' +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }
}

```

 **创建BookRepository** :
由于是继承，所以下面的方法也都可以不写，写只是告诉有一些基本的操作方法例如增删改查等。

```java
import org.springframework.data.jpa.repository.JpaRepository;
//继承JpaRepository默认提供的一些基本的数据库操作方法
public interface BookRepository extends JpaRepository<Book,Integer> {
}
```

以上就是RESTful服务了。注意我一开始启动项目的时候并没有生成表格，后面才发现是我的启动程序多了东西，只需要@SpringBootApplication就可以了。

下面使用PostMan进行接口测试（注意这是软件，根据需要来下载，chrome也有，不过你懂的）
我们可以在t_book添加一些数据：
![在这里插入图片描述](https://img-blog.csdnimg.cn/3522b532efef4d2a9e85527e81948556.png)
打开postman，
RESTFul API默认请求路径是实体类名小写再加上s后缀，本文实体类为book，加上s后缀，即默认路径名是books。
使用get命令来查询结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ad3d578e2e64f2693a6984699e37ac9.png)
新建测试，我们在postman创建一个post请求，body放入新增的数据，选择json类型.
添加一个数据，此时已经写入数据库中。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f55b435b38394543b993e0c477925373.png)
按照id查询，主要在books后面加id号即可了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/0b41aa6dee5349f1a468c1a3220d6a0b.png)
 **修改测试** ：
发送Put请求，url是请求id号![在这里插入图片描述](https://img-blog.csdnimg.cn/e4e4043c096e471880a33409eed8662d.png)

## 自定义请求路径：

默认情况下，请求路径为实体类名加小写s,如果开发者想对请求路径进行重定义，可以通过@RepositoryRestResource注解即可实现。

```java
@RepositoryRestResource(path = "bs",collectionResourceRel = "bs",itemResourceRel = "b")
```

注解的path属性表示将所有请求路径中的books全都修改为bs，返回的JSON集合单个book的key修改为b.
![在这里插入图片描述](https://img-blog.csdnimg.cn/1c1b37838b3d4cbc9d2ac43a6b2e1d54.png)

## 自定义查询方法

默认的查询方法支持分页查询，排序查询以及按照id查询，如果开发者想要按照某个属性查询，只需在定义相关方法并暴露出去即可：

```java
@RepositoryRestResource(path = "bs",collectionResourceRel = "bs",itemResourceRel = "b")
public interface BookRepository extends JpaRepository<Book, Integer> {
    @Override
    @RestResource(exported = false)
    void deleteById(Integer integer);

    @RestResource(path = "author",rel = "author")
    List<Book> findByAuthorContains(@Param("author") String author);
    @RestResource(path = "name",rel = "name")
    Book findByNameEquals(@Param("name") String name);
}

```

只需要在BookRepository定义相关查询方法即可了，可以不用添加restresource注解。
通过/bs/search 来查看该实体类暴露了哪些查询方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7cbeeb982e874365801bf6bd2e82e02c.png)

# MongoDB 实现REST

快速构建RESTful服务除了结合Spring Data JPA之外，也可以结合MongoDB来实现，步骤如下：
添加依赖：

```java
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-rest</artifactId>
        </dependency>
```

和之前的MongoDB设置一样，配置见上文：

创建实体类：

```java
public class Book {
    private Integer id;
    private String name;
    private String author;
    //省略getter/setter
}

```

创建BookRepository:

```java
import org.springframework.data.mongodb.repository.MongoRepository;


public interface BookRepository extends MongoRepository<Book,Integer> {
}

```

这样一个RESTful服务就搭建成功了。（注意要启动MongoDB）

# 浅谈RESTful服务

对于上面的restful服务可能理解起来有困难，所以我单独写了这个模块来加强认识。
RESTful可以通过**一套统一的接口**为Web，iOS和Android提供服务。另外对于广大平台来说，比如微博开放平台，微信开放平台等，它们不需要有显式的前端，只需要一套提供服务的接口，RESTful无疑是最好的选择。（所以这就是我们使用PostMan来进行接口测试的原因）

传统的API接口：一般来说删除一个数据delete/{id} ，更新就是update/{id}等
url是一种资源，在之前一直用的是get和post.
 **基于REST构建的api就是restful风格** ：

```java
GET /rest/api/getDogs --> GET /rest/api/dogs 获取所有小狗狗 
GET /rest/api/addDogs --> POST /rest/api/dogs 添加一个小狗狗 
GET /rest/api/editDogs/:dog_id --> PUT /rest/api/dogs/:dog_id 修改一个小狗狗 
GET /rest/api/deleteDogs/:dog_id --> DELETE /rest/api/dogs/:dog_id 删除一个小狗狗

```

REST 是面向资源的，这个概念非常重要，而资源是通过 URI 进行暴露。
区别HTTP（面向无状态的）

对于RESTful 需要在日后继续加强。
