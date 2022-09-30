 **本次属于Java Servlet/JSP 最后的笔记内容了** ，常见的几大板块均以结束了。这一模块是Java Bean与DAO设计模式，这个是相当重要的。主要是涉及到类的编写与数据库操作上面。
这里对JavaEE部分做一个总结，供大家学习参考，每篇博客也非常详实，希望对你有所帮助：
[环境配置](https://blog.csdn.net/QAZJOU/article/details/121097745?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166238284316782246415036%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=166238284316782246415036&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-121097745-null-null.nonecase&utm_term=Tomcat&spm=1018.2226.3001.4450)
[JSP基础知识](https://blog.csdn.net/QAZJOU/article/details/126431931?spm=1001.2014.3001.5502)
[Servlet请求与响应](https://blog.csdn.net/QAZJOU/article/details/126432249?spm=1001.2014.3001.5502)
[Servlet会话管理与监听器，过滤器](https://blog.csdn.net/QAZJOU/article/details/126637717?spm=1001.2014.3001.5502)

其中Java Bean就是类，**本博客以一个Product 类层层递进，并在DAO设计模式中完成了主要的增删改查部分，是一个小型的CRUD练手项目，快速掌握DAO的例子。**

DAO设计模式，主要是 **为了降低耦合度** ，易于理解，也是非常容易入门的设计模式。在实际开发中，DAO设计模式虽然过时，但是其思想依旧深远！

 **本次模块结束后，将会抽时间综合前面的内容完成一个简易的系统内容。** （主要也是恶补一下前端知识了），预计最快这周，如果比较紧将会在下周如约而至。等相应的模块结束后，就得紧跟着了进行Spring大家庭了！

### 目录

* [Java Bean](https://editor.csdn.net/md/?articleId=126682339#Java_Bean_14)

  * [基本概念](https://editor.csdn.net/md/?articleId=126682339#_15)
  * [JSP 使用Bean](https://editor.csdn.net/md/?articleId=126682339#JSP_Bean_41)
  * [访问Bean属性](https://editor.csdn.net/md/?articleId=126682339#Bean_58)
    * [表达式设置属性](https://editor.csdn.net/md/?articleId=126682339#_69)
    * [表单设置属性](https://editor.csdn.net/md/?articleId=126682339#_135)
    * [取得属性](https://editor.csdn.net/md/?articleId=126682339#_153)
  * [Bean的作用域](https://editor.csdn.net/md/?articleId=126682339#Bean_158)
* [DAO 设计模式](https://editor.csdn.net/md/?articleId=126682339#DAO__171)
* [DAO开发](https://editor.csdn.net/md/?articleId=126682339#DAO_184)
* [DBConnection](https://editor.csdn.net/md/?articleId=126682339#DBConnection_200)
* [ProductDao](https://editor.csdn.net/md/?articleId=126682339#ProductDao_242)
* [ProductDaoImpl](https://editor.csdn.net/md/?articleId=126682339#ProductDaoImpl_258)
* [ProductService](https://editor.csdn.net/md/?articleId=126682339#ProductService_343)
* [DAOFactory](https://editor.csdn.net/md/?articleId=126682339#DAOFactory_406)
* [JSP调用DAO](https://editor.csdn.net/md/?articleId=126682339#JSPDAO_445)
* [Bug小插曲](https://editor.csdn.net/md/?articleId=126682339#Bug_552)

# Java Bean

## 基本概念

开发软件过程中， **尽量将业务逻辑和表现层分开，从而达到解耦** ，这是软件分层设计的基本理念。在JSP中，经常利用Java Bean实现核心的业务逻辑，而JSP页面用于表现层。

在这种设计模式下，JSP页面只用于接收用户的输入以及显示处理之后的结果，因此不需要在JSP嵌入大量的Java代码，不但提高了系统的可维护性，而且方便工作的分工。
下面以一个例子来说明：

```java
public class User implements Serializable {
	private String username;
	private String passwd;
	private String sex;

	public User(){}

	//下面是get 和 set 方法，必须是public
	public String getUsername() {
		return username;
	}

	public void setUsername(String username){
		this.username = username;
	}
	...
}
```

这就是一个Java Bean 的基本结构，遵循的规范：必须是public类，提供给JSP页面调用的方法，属性必须提供get/set，必须拥有不带参数的构造方法。

## JSP 使用Bean

在JSP页面中使用Bean:1.规范定义Bean，给出get 和set 2.在页面中导入Bean类。 3.利用jsp:useBean使用Bean类
规范定义Bean类：新增一个类就可以。一种将Bean的class文件部署在Web服务器公共目录中；另一种将Class文件部署在特定目录，Web-INF\classess
在JSP页面中导入相应的Bean类：

```javascript
<jsp:useBean
	id="beanInstanceName">
```

| 属性值   | 说明                                                  |
| -------- | ----------------------------------------------------- |
| id       | Bean变量名                                            |
| class    | Bean的类路径，严格的package.class                     |
| scope    | Bean有效范围，取值为page ,request,session,application |
| beanName | 实例化的类名或序列化模板名称                          |
| type     | Map,HashMap                                           |

## 访问Bean属性

设置属性：

```javascript
<jsp:setProperty
name = "beanInstanceName"
{
property = "*" |
}
/>
```

### 表达式设置属性

 **利用表达式来设置Bean属性值** ，意思就是在jsp中使用value来进行set

```javascript
# product.jsp
<%@ page  contentType="text/html;charset=UTF-8" language="java"  import="java.util.*" %>
<jsp:useBean id="product" class ="BeanTest.Product" scope="page"/>
<html>
<head>
  <title>Title</title>
</head>
<body>
<jsp:setProperty name="product" property="product_name" value="Book"/>
产品名称：
<%=product.getProduct_name()%>

</body>
</html>

```

上述代码只是简单设置了Bean值，导入Bean类，分别设定了Bean属性值和获取Bean属性值

```java
package BeanTest;

import java.io.Serializable;

public class Product implements Serializable {
    private static  final  long serialVersionUID = 1L;
    private  String product_id;
    private  String product_name;
    private  double price;
    private  String info;

    public Product(){
        super();
    }

    public String getProduct_name() {
        return product_name;
    }
    public void setProduct_name(String name) {
        this.product_name = name;
    }
    public String getProduct_id() {
        return product_id;
    }
    public void setProduct_id(String id) {
        this.product_id = id;
    }
    public double getPrice() {
        return price;
    }
    public void setPrice(double price) {
        this.price = price;
    }

    public String getInfo() {
        return info;
    }
    public void setInfo(String info) {
        this.info = info;
    }
}


```

### 表单设置属性

**通过表单参数来设置属性值**
这样就不用设置value值。

```javascript
# product.jsp
# Bean类不用修改
<body>
  <form action="" method="post">
      输入产品名称:<input name="product_name" />
      <input type="submit" value="submit"/>
  </form>
  <jsp:setProperty name="product" property="product_name"/>
  <%=product.getProduct_name()%>
  或者以下的方法，只是通过参数值来设置的
  <jsp:setProperty name="product" property="productname" param="product_name"/>
</body>
```

### 取得属性

name就是Bean名字，值必须是jsp:useBean中的id值

```javascript
<jsp:getProperty name="beanInstacnceName" property="propertyName"/>
```

## Bean的作用域

Bean的作用域有4个：page request session application，作用范围有scope，默认是page,即该Bean在当前页有效。

```javascript
# 写一个请求的param
<%
	String r = request.getParameter("radius");
	if(r == null || r.equals("")) r ="1";
	double rad = Double.parseDouble(r);
	circle.setR(rad);
%>
<body>
```

# DAO 设计模式

这是一个值得学习的设计模式。虽然如今有很多成熟的框架了，例如Spring MVC ,Struts等
信息系统的开发架构如下：
客户层—显示层—业务层—数据层—数据库
客户层：客户端浏览器
显示层：利用JSP和Servlet进行页面显示
业务层：对数据层原子性DAO操作进行整合
数据层：数据增删改查
数据库：保存数据库信息
DAO：Data Access Object。主要是对数据进行操作，对于上面层级就是数据层。在数据操作中，主要以面向接口编程为主。一般DAO分为：
VO(Value Object),DatabaseConnection，DAO-implements,DAOImpl,DAOproxy,DAOFactory
DAO命名规则：XxxDAO.

## DAO开发

首先建一个产品表

```sql
use dao_product

create table product(
    idc varchar(20) not null ,
    product_name varchar(20) default null,
    price decimal(6,2) default  null,
    info varchar(100) default null,
    primary key (idc)
)
```

在src/TestBean文件如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/6c00a36b41bf4e8ba903c29ce1bad322.png)
首先是DBConnection类，只要是执行对数据库的连接配置，注意这里驱动最好写成com.mysql.cj.jdbc.Driver,这里用于获取数据库连接.

### DBConnection

```java
package BeanTest;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConnection {
    private  static  final  String Driver = "com.mysql.cj.jdbc.Driver";
    private  static  final  String URL = "jdbc:mysql://127.0.0.1:3306/dao_product";
    private static  final  String USER = "root";
    private  static  final  String PASSWORD = "qazwsx123";
    private Connection conn = null;

    //数据库连接
    public DBConnection() throws  Exception {
        try{
            Class.forName(Driver); //反射加载驱动
            this.conn = DriverManager.getConnection(URL,USER,PASSWORD);
        }catch (Exception e) {
            throw e;
        }
    }

    //取得连接
    public Connection getConnection() {
        return this.conn;
    }

    public void close() throws Exception{
        if(this.conn != null) {
            try{
                this.conn.close();
            }catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }
}

```

新建DAO接口类，接口类在DAO设计模式中极其重要。定义接口类之前，需要分析业务的需求，分析清楚系统需要哪些功能，方法。本例只完成新增，查询等简单功能。

### ProductDao

```java
package BeanTest;
import java.util.List;

public interface ProductDao {

    public boolean addProduct(Product product) throws Exception;

    public List<Product> findAll(String product_name) throws Exception;

    public  Product findByProductID(String product_id) throws  Exception;

}
```

在DAO接口定义完成后需要定义其实现类，为 **降低耦合度** （耦合度就是模块与模块之间的关联），需要有实现类。**一种是数据操作实现类，另一种是业务操作实现类。**

### ProductDaoImpl

数据操作实现类，完成的是具体的数据库操作。

```java
package BeanTest;

import sun.dc.pr.PRError;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

public class ProductDaoImpl implements  ProductDao{
    private Connection conn = null;
    private PreparedStatement pstmt = null;

    public ProductDaoImpl(Connection conn) {
        this.conn = conn;
    }

    @Override
    public boolean addProduct(Product product) throws Exception {
        boolean flag = false;
        String sql = "insert  into product(idc,product_name,price,info) values(?,?,?,?)";
        this.pstmt = this.conn.prepareStatement(sql);
        this.pstmt.setString(1,product.getProduct_id());
        this.pstmt.setString(2,product.getProduct_name());
        this.pstmt.setDouble(3,product.getPrice());
        this.pstmt.setString(4,product.getInfo());
        int t = this.pstmt.executeUpdate();
        //System.out.println(t);
        if(t>0) {
            flag = true;
        }
        this.pstmt.close();
        return flag;
    }

    @Override
    public List<Product> findAll(String product_name) throws Exception {
        List<Product> list = new ArrayList<Product>();
        String sql = "select idc ,product_name,price,info from product";
        if(product_name != null &&!"".equals(product_name)) {
            sql ="select idc,product_name,price,info from product where product_name like ?";
            this.pstmt = this.conn.prepareStatement(sql);
            this.pstmt.setString(1,"%" +product_name+"%");
         
        }else {
            this.pstmt = this.conn.prepareStatement(sql);
        }
        ResultSet rs = this.pstmt.executeQuery();
        Product product = null;
        while(rs.next()) {
            product = new Product();
            product.setProduct_id(rs.getString(1));
            product.setProduct_name(rs.getString(2));
            product.setPrice(rs.getDouble(3));
            product.setInfo(rs.getString(4));
            list.add(product);
        }
        this.pstmt.close();
        return list;
    }

    @Override
    public Product findByProductID(String product_id) throws Exception {
        Product product = null;
        String sql = "select idc,product_name,price,info from product where idc = ?";
        this.pstmt = this.conn.prepareStatement(sql);
        this.pstmt.setString(1,product_id);
        ResultSet rs = this.pstmt.executeQuery();
        if(rs.next()) {
            product = new Product();
            product.setProduct_id(rs.getString(1));
            product.setProduct_name(rs.getString(2));
            product.setPrice(rs.getDouble(3));
            product.setInfo(rs.getString(4));
        }

        this.pstmt.close();
        return product;
    }
}
```

### ProductService

对数据库的打开和关闭由业务操作实现类完成。业务操作类ProductService：

```java
package BeanTest;

import java.util.List;
import BeanTest.ProductDaoImpl;

public class ProductService implements  ProductDao{
    private  DBConnection dbconn = null;
    private  ProductDao dao = null;

    public ProductService() throws  Exception {
        this.dbconn = new DBConnection();
        this.dao = new ProductDaoImpl(this.dbconn.getConnection());
    }

    @Override
    public boolean addProduct(Product product) throws Exception {
        boolean flag = false;

        try{
            if(this.dao.findByProductID(product.getProduct_id()) == null) {
                //如果插入的产品编号不存在，那么就新增一条产品
                flag = this.dao.addProduct(product);
            }
        }catch (Exception ex) {
            ex.printStackTrace();
        }finally {
            this.dbconn.close();
        }

        return flag;
    }

    @Override
    public List<Product> findAll(String product_name) throws Exception {
        List<Product> all = null;
        try{
            all = this.dao.findAll(product_name);
        }catch (Exception ex) {
            ex.printStackTrace();
        }finally {
            this.dbconn.close();
        }
        return all;
    }

    @Override
    public Product findByProductID(String product_id) throws Exception {
        Product product = null;
        try{
            product = this.dao.findByProductID(product_id);
        }catch (Exception ex) {
            ex.printStackTrace();
        }finally {
            this.dbconn.close();
        }
        return product;
    }
}
```

### DAOFactory

编写DAO工厂类，用来获得业务操作类，在后续的客户端可以直接通过工厂获得DAO接口的实例对象：

```java
package BeanTest;

public class DAOFactory {
    public static ProductDao getIEmpDAOInstance() throws Exception {

        return new ProductService(); //取得业务操作
    }
}
```

这是一个测试文件：

```java
package BeanTest;

public class TestInsertProduct {
    public static  void main(String[] args) {
        Product product = null;
        try{
            for(int i=99;i<105;i++) {
                product = new Product();
                product.setProduct_id("000"+i);
                product.setProduct_name("水杯"+i);
                product.setPrice(100+i);
                product.setInfo("这是杯子"+i);
                DAOFactory.getIEmpDAOInstance().addProduct(product);
            }
        }catch (Exception ex) {
            ex.printStackTrace();
        }
        System.out.println("suss");
    }
}

```

## JSP调用DAO

编写一个DAO后，可以结合JSP一起实现前台的显示。

```javascript
# product_insert.jsp
<%@ page import="java.util.*" contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="BeanTest.DAOFactory" import ="BeanTest.Product" %>
<%
    request.setCharacterEncoding("utf-8");
%>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        Product product = new Product();
        product.setProduct_id(request.getParameter("product_id"));
        product.setProduct_name(request.getParameter("product_name"));
        product.setPrice(Double.parseDouble(request.getParameter("price")));
        product.setInfo(request.getParameter("info"));
        boolean flag = DAOFactory.getIEmpDAOInstance().addProduct(product);
        if(flag){
    %>添加成功
    <%
        }else{
    %>添加失败
    <%
        }
    %>
</body>
</html>

```

提交信息并跳转：

```javascript
# product_add.jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" import="java.util.*" %>
<html>
<head>
    <title>Add Info</title>
</head>
<body>
<form action="product_insert.jsp"  method="post">
    产品编号： <input name ="product_id"/><br>
    产品名称： <input name ="product_name"/><br>
    产品价格： <input name ="price"/><br>
    产品信息： <textarea rows="" cols="" name="info" ></textarea><br>
    <input type="submit" value="添加">
    <input type="reset" value="重置">
</form>
</body>
</html>

```

查询页面：

```javascript
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@page import="java.util.*"  %>
<%@ page import="BeanTest.Product" %>
<%@ page import="BeanTest.DAOFactory" %>
<%
    request.setCharacterEncoding("utf-8");
%>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    String product_name = request.getParameter("product_name");
    List<Product> list = DAOFactory.getIEmpDAOInstance().findAll(product_name);

%>
<form action="product_list.jsp" method="post">
    输入产品名称: <input name ="product_name"/>
    <input type="submit" value="提交">
</form>
<table border="1">
    <tr>
        <td>产品编号</td>
        <td>产品名称</td>
        <td>产品价格</td>
        <td>产品信息</td>
    </tr>
    <%
        for(int i=0;i<list.size();i++) {
          Product   p =list.get(i);

    %>
    <tr>
        <td><%=p.getProduct_id()%></td>
        <td><%=p.getProduct_name()%></td>
        <td><%=p.getPrice()%></td>
        <td><%=p.getInfo()%></td>
    </tr>
    <% } %>
</table>
</body>
</html>

```

# Bug小插曲

在编写DAO类的时候，一直遇到这个问题， **Duplicate entry ‘00099’ for key ‘PRIMARY’** ，我是真的醉了，把每一个类重新看了n遍。由于这是插入值抛出的异常。
当然了，正常思维还是搜索，有很多人说是因为主键插了重复的值原因，但是我根本没有重复值。
后来得益于这篇博客的启发，[点击此处](https://blog.csdn.net/qq_39390545/article/details/91046282)，在insert的时候使用insert ignore，这时候确实成功了。上面这篇博客也说可以设置一个自增主键id,那我索性也就设置了，后来运行出来没有那个问题了。

但当我重新审计代码时候，发现在实现接口的部分，在add部分犯了致命的错误，方法返回值是false,而不是flag.当我重新改了这个错误的时候，成功运行出来了。

不过一开始抛出来的Duplicate entry primary确实让我迷惑了很久，没想到不是这个问题（简直把我往错误的方向跑了）。看来日后code仍需要仔细啊！

以上就是全部内容了，希望有所帮助。
