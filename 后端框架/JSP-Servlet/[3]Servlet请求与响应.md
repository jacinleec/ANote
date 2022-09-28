本章节讲述了Servlet技术，同时对请求与响应部分详细说明，包括上传下载文件（输入输出流）调派请求等做了相应的补充。

相信大家学了第一章的内容[Servlet 1](https://blog.csdn.net/QAZJOU/article/details/126431931?spm=1001.2014.3001.5501),对JSP有了初步的概念了，当然Servlet和JSP也是差不多的，关键一个是在页面写java，一个就是在Java写页面罢了。当然，这其中 **需要扎实的Java功底** ，基本功到位了，也不再多大的困难了。本次在最后也附上一个注册与登录界面，当然使用的是Map进行读取，还没有涉及到数据库内容，在后面的笔记中将依次附上会话，数据库，Java Bean 与标签库。

**Java是非常重要的，在后面的学习还是需要加大学习，不仅仅是各种框架体系，还有本身的语法内容，进阶模块等等。**

希望接下来的内容对你有所帮助，文章虽长，却是收获满满，可以先从Servlet1[点击此处](https://blog.csdn.net/QAZJOU/article/details/126431931?spm=1001.2014.3001.5501)配置环境等内容！

### 目录

* [Servlet 技术](https://editor.csdn.net/md/?articleId=126432249#Servlet__10)

  * [Servlet 是什么？](https://editor.csdn.net/md/?articleId=126432249#Servlet__13)
  * [Servlet 生命周期](https://editor.csdn.net/md/?articleId=126432249#Servlet__17)
  * [编写和部署Servlet](https://editor.csdn.net/md/?articleId=126432249#Servlet_116)
  * [Servlet与JSP比较](https://editor.csdn.net/md/?articleId=126432249#ServletJSP_149)
* [请求与响应](https://editor.csdn.net/md/?articleId=126432249#_159)
* [容器到HttpServlet](https://editor.csdn.net/md/?articleId=126432249#HttpServlet_162)
* [HttpServletRequest 对象](https://editor.csdn.net/md/?articleId=126432249#HttpServletRequest__167)
* [getPart()](https://editor.csdn.net/md/?articleId=126432249#getPart_199)
* [RequestDispatcher 调派请求](https://editor.csdn.net/md/?articleId=126432249#RequestDispatcher__282)
* [HttpServletResponse对象](https://editor.csdn.net/md/?articleId=126432249#HttpServletResponse_367)
* [综合练习](https://editor.csdn.net/md/?articleId=126432249#_430)

# Servlet 技术

web应用中，Servlet是一个重要的技术，其利用Java类编写的服务端程序，与架构、协议无关。JSP实质就是Servlet，因为JSP页面传回服务端都转为Servlet 进行编译，运行。由于JSP 编写HTML直观，所以也在逐步取代Servlet。

## Servlet 是什么？

服务端运行的程序，打开浏览器即可调用一个。可以看作位于客户端和服务端的一个中间层，负责接收和请求客户端用户的响应。客户端 -----> request <----- response Servlet容器 （init , service, destroy)
一般来说，定义一个Servlet 类，在web.xml 配置程序，继而发布程序，这样一个Web 程序就完成了。

## Servlet 生命周期

有3个阶段，分别是init ,service ,destroy
init (初始化）：分为装载和初始化。装载一个Servlet 类到Java内存。调用Init方法，只调用一次。
运行阶段：响应客户端的请求，有请求创建HttpServletRequest (/Response),调用service 方法。
消亡阶段，调用destroy，释放所占的资源。例如关闭流，关闭连接等。

注意先导入模块，IDEA-设置-项目结构-库
![在这里插入图片描述](https://img-blog.csdnimg.cn/8b59ba42b3af43f98454f0e94aeb7218.png)

HelloServlet.java

```java
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;


//@WebServlet(
//        urlPatterns = {"/HelloServlet"},
//        name = " helloServlet"
//)
public class HelloServlet extends HttpServlet {
    private  static  final long serialVersionUID = 1L;

    @Override
    public void init() throws ServletException{
        System.out.println("初始化 init 方法");
    }

    @Override
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        super.service(req, res);
        System.out.println("调用service 方法");
        res.setContentType("text/html;charset=gbk");
        PrintWriter out = res.getWriter();
        out.println("收到service请求");
    }

    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        System.out.println("调用doGet方法");
        response.setContentType("text/html;charset=gbk");
        PrintWriter out = response.getWriter();
        out.println("收到doGet请求");
    }

    @Override
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        System.out.println("调用doPost方法");
        response.setContentType("text/html;charset=gbk");
        PrintWriter out = response.getWriter();
        out.println("收到doPost请求");
    }

    @Override
    public void destroy() {
        System.out.println("destroy");
    }
}

```

web.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>HelloServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/HelloServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

这样照着我的代码运行，注意，运行.class就一个好运行。
跑完以后在/加HelloServlet即可运行了。（因为配置先打开的会是index.jsp)
![在这里插入图片描述](https://img-blog.csdnimg.cn/91fe4bc4c77444b383c0526e3da19aab.png)

也可以像HelloServlet.java 加入@WebServlet

| 属性                       | 描述                              |
| -------------------------- | --------------------------------- |
| name(String name)          | 指定Servlet的name属性，等价于     |
| urlPatterns(String[] urls) | 指定Servlet 的URL匹配模式，等价于 |

可以不用idea来运行，稍微麻烦一点，步骤如下：先运行Tomcat ,/bin/startup
输入127.0.0.1:8080 .注意吧HelloServlet 编译完以后放在webapps文件下，同时按照上述修改web.xml文件，在url后面输入相应的url即可运行。

## 编写和部署Servlet

关于相应的部署请参考我上篇的博客内容。
web-inf/ web.xml 配置相应的类文件

```java
<servlet>
	<servlet-name>
	HelloServlet   //自定义的Servlet名字,与下面要对应
	</servlet-name>
	<servlet-class>
	com.yxtech.HelloServlet  //具体的类位置，src/ ，如果就在src下面只要写/HelloServlet
	</servlet-class>
</servlet>


<servlet-mapping>
	<servlet-name>
	HelloServlet
	</servlet-name>
	<url-pattern>
	/HelloServlet //对应网页地址具体的servlet名
	</url-pattern>
</servlet-mapping>
```

分析创建Servlet 的步骤：
1.引入相应的包。2.创建扩展类 3.重构doGet() doPost() 4.配置web.xml
本地址：http://localhost:8080/TestTomcat/HelloServlet
TestTomcat：工程名 HelloServlet : url-pattern 的Servlet名

部署：根据我之前的博客选择Tomcat部署，这里给出两种说明。
![在这里插入图片描述](https://img-blog.csdnimg.cn/47e2bc223dac4b4083093cbe4902cda2.png)
关于IDEA部署就是之前的配置了，比较简单不再赘述了。

## Servlet与JSP比较

本质他们是一样的。JSP页面最后运行时候会转换成Servlet。
区别：
1.Servlet是JAVA代码，JSP是页面代码
2.Servlet速度快过JSP
3.Servlet手动编译，JSP由服务器自动编译
4.编辑HTML不支持Servlet
一般而言，Servlet大多用于负责对客户端的请求和处理调用Java Bean，由Java Bean负责提供可复用的数据和数据的访问等。JSP主要负责展示页面，将动态数据展现给客户，这就是简易的MVC模式，减少了JSP页面Java Html代码的耦合度，对维护工作有重大意义。

# 请求与响应

JSP开发中，请求与响应是最基本的内置对象。Web容器是JSP唯一可以识别的HTTP服务器。

## 容器到HttpServlet

web 容器根据配置信息，查找相应的Servlet 并调用service方法，根据请求方式决定调用doPost()或者doGet().
![](https://img-blog.csdnimg.cn/6f3f2ca45b4d47deb71a234aa3b43476.png)
当Method是POST时，请求会调用doPost();同理get一样。需要实现doGet()/doPost(),如果不实现会出现
![在这里插入图片描述](https://img-blog.csdnimg.cn/4339e4e6c5c84ddf95192a798addbe7d.png)

## HttpServletRequest 对象

请求封装对象，由Web容器生成，使用该对象可以取得HTTP请求信息。
使用getReader()获取body,使用getInputStream()获得上传文件内容。

```java
// HelloServlet.java
public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        BufferedReader br = request.getReader();
        String input = "";
        String body = "";
        while((input=br.readLine())!=null) {
            body += input + "<br/>";
        }
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<body>");
        out.println(body);
        out.println("</body>");
        out.flush();
        out.close();
    }
```

```java
<form  action = "<%=request.getContextPath()%>/HelloServlet" method ="POST">
       用户名： <input name = "username"/><br/>
       密码： <input type="password"  name = "password"/> <br/>
        <input type="submit" value="sumbit" />
   </form>
```

运行以后输入地址提交边可以看到相应的内容了。使用统一的UTF-8编码可以一一对应。如果在页面中设置了form enctype:multipart/form-data,那么就会出现乱码。

### getPart()

**使用getPart() getParts() 取得上传文件**
getPart()处理单文件内容，getParts()处理多文件内容， **必须使用MultipartConfig注解** ，这样Servlet才能获得Part对象。下表是其属性：

| 属性              | 说明                                                          |
| ----------------- | ------------------------------------------------------------- |
| fileSizeThreshold | 数值类型，当上传文件的大小大于该值内容先写入缓存文件，默认为0 |
| location          | 设置存放生成的文件目录地址                                    |
| maxFileSize       | 设置允许文件上传的最大值，默认是-1L表示不限制                 |

```java
// HelloServlet.java 
import org.jsoup.internal.StringUtil; //注意这个包没有用。放这里只为了提醒
import org.apache.commons.lang3.StringUtils;

@MultipartConfig(location = "D:/tmp/",maxFileSize = 1024*1024*10)
@WebServlet(
        name = "HelloServlet",
        urlPatterns = {"/upload.do"}, //注意web.xml也需要更改urlpattern
        loadOnStartup = 0
)
public class HelloServlet extends HttpServlet {
    @Override
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        //设置处理编码
        request.setCharacterEncoding("UTF-8");
        Part part = request.getPart("filename");
        String filename = getFilename(part);
        part.write(filename); //上传文件保存的路径

        //返回页面消息
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<HEAD><TITLE>SERVLET </TITLE></HEAD>");
        out.println("<script> alert(\"suss\")</script>");

        out.flush();
        out.close();

    }

    private String getFilename(Part part) {
        if(part == null ) {
            return null;
        }
        String fileName = part.getHeader("content-disposition");
        if(StringUtils.isBlank(fileName)) {
            return null;
        }
        return StringUtils.substringBetween(fileName,"filename=\"","\"");
    }
}

```

```java
 <form  action = "<%=request.getContextPath()%>/upload.do" method ="POST" enctype="multipart/form-data">
      选择文件: <input type="file" name="filename" />
        <input type="submit" value="submit" />
   </form>
```

有个小插曲值得一讲，在我进行对commons-lang导入的时候（需要在官网下载），在idea正常导入模块，但运行的时候出现了以下问题。
![在这里插入图片描述](https://img-blog.csdnimg.cn/69a3a6373d174c5cad5154cdc5d38cf3.png)
在根本原因那里出现了DefFoundError，简单来说，这是一个编译时候找到包了，但是运行的时候没有找到包。暴力解决办法：在web-inf/lib中导入：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ef10c5b21cb4d6a841fcf3ecc6cb9c6.png)
问题就解决了，运行index.jsp时候，点击提交文件，待完成以后在d:/tmp/已经有了上传的文件内容。

**如果是批量上传文件：**

```java
// getParts.java
request.getParts();
for(Part part: request.getParts()) {
	if(part.getName().startWith("filenam")) {
		String filename = getFilename(part);
		part.write(filename);
	}
}

//index.jsp
<form>
	<input type="file" name="filename1"/>
```

### RequestDispatcher 调派请求

web中，经常是由多个Servlet来完成请求。RequestDispatcher接口就是为了多个Servlet调整实现的。调用时候指定跳转url完成跳转。有两种方法，分别是include() forward()
 **include()** ： 在当前的Servlet显示另外一个Servlet()

```java
// first.java ，this url :include.do
 public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        //返回页面消息
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<BODY>");
        out.println("First.Servlet<br/>");

        //获取RequestDispatcher
        RequestDispatcher dispatcher = request.getRequestDispatcher("/second.do");
        dispatcher.include(request,response);
        out.println("include <br/>");
        out.println("</BODY>");
        out.close();
    }

// second.java: this url is second.do
 public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("sec<br>");
    }
```

在web.xml先配置include.do，编译运行后完成第一个全部的打印，说明第二个程序没有问题，那么在浏览器中输入/second.do可以看到效果。
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa37cefa1c3349c4ac8e1d5f68abaa8a.png)

forward(): Servlet跳转方法，跳转到其他的Servlet，不会返回到前面的Servlet。

```java
//include.do
@Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        doPost(request,response);
    }

    @Override
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        String userName = request.getParameter("username");
        String message = user.sayHello(userName); // user = new HelloUser()
        request.setAttribute("message",message);
        request.getRequestDispatcher("/second.do").forward(request,response);
    }

//HelloUser.java
public class HelloUser {
    private Map<String,String> helloMessage = new HashMap<String,String>();
    public HelloUser() {
        helloMessage.put("John","Hello,John");
        helloMessage.put("Smith","Welcome");
        helloMessage.put("Rose","Hi,Rose");
    }

    //根据用户，返回Map消息：
    public String sayHello(String userName) {
        return helloMessage.get(userName);
    }
}

//second.do
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request,response);
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String message = (String) request.getAttribute("message");
        //返回页面
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<BODY>");
        out.println(message);
        out.println("</BODY>");
        out.close();
    }
```

运行输入/include.do?username=Smith，出现下图效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/36f7dcea364f44fdaa67ef6071c58361.png)
在上述代码中，演示了简单的MVC模型，HellosServlet作用是Controller(不含HTML)，只负责业务处理。\HelloUser中是对数据处理，获取欢迎信息。second.do是view,用于展示结果，这是一个初步的认识。

## HttpServletResponse对象

用于对浏览器做出响应响应的操作对象，可以设置响应类型，也可以输出HTML。setContentType设置JSP类型，使用getWriter()取得PrintWriter对象。
 **getWriter()输出字符** ：

```java
request.setCharacterEncoding("UTF-8") ;//设置请求的编码类型
response.setContentType("text/html;charset=UTF-8");
//或者 response.setContentType("UTF-8") 设置支持中文的字符编码
PrintWriter out = response.getWriter();//取得对象使得可以写html内容。
out.println("<BODY>");
out.println("</BODY>");
```

在Servlet开发中，告诉浏览器以何种方式处理响应（内容），要设置响应标头。设置context-type，指定MIME类型，浏览器就知道标头类型。

```java
<mime-mapping>
   <extension>jar</extension> //用于设置文件的后缀
   <mime-type>application</mime-type> //设置对应的MIME类型
</mime-mapping>
```

**getOutputStream()输出二进制字符**
对于上传和下载文件需要用到字节输出流。通过这个可以取得ServletOutStream对象。

```java
//download.java
void doPosts(...)...{
	response.setContentType("application/msword"); //设置响应的内容类型
	response.addHeader("Content-disposition","attachment;filename=test.doc");//设置响应的标头内容
	InputStream in = getServletContext().getResourceAsStream("/doc/test.doc");//获取资源文件，web根目录
	ServletOutputStream os = response.getOutputStream(); //输出到浏览器
	byte[] bytes = new byte[1024];
	int len = -1;
	while((len=in.read(bytes))!=-1) {
	 	os.write(bytes,0,len);
	}

	in.close();
	os.close();
}
```

**sendError() 方法**
如果在处理请求时候发生错误，就可以用sendError()传递服务器的状态和错去信息。

```java
response.sendError(HttpServletResponse.SC_NOT_FOUND,"错误");// 请求地址不存在


void doPost(...){
	String pro = req.getProtocol();
	String msg = IString.getString("http.method_post_not_supported");
	if(protocol.endsWith("1.1")) {
		response.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED,"错误");
	} else {
			response.sendError(HttpServletResponse.SC_BAD_REQUEST,"错误");
	}
}
```

常见的HTTP请求错误代码说明：
![在这里插入图片描述](https://img-blog.csdnimg.cn/484fa2087a9c4554a964a44906e5686e.png)

# 综合练习

利用之前内容实现注册和登录的功能。利用Servlet编写，之前已经用jsp实现了。
逻辑图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c556f92a5456450b9e7577bb60095961.png)
本练习从注册页面到登录页面都做到了不错的响应和请求，其中也对密码邮箱等做了验证，包含List Map等数据结构内容，属于综合性的小练习。
首先是注册jsp

```java
# register.jsp
<form  action = "<%=request.getContextPath()%>/register.htm" method ="POST" >
       <table border="1">
           <tr>
               <td>登录名:</td>
               <td><input name = "username"/></td>
           </tr>
           <tr>
               <td>密码：</td>
               <td><input name="passwd" type="password" /></td>
           </tr>
           <tr>
               <td>密码确认：</td>
               <td><input name="confirdPasswd"  type="password"/></td>
           </tr>
           <tr>
               <td>邮箱地址：</td>
               <td><input name ="email"/></td>
           </tr>
           <tr>
               <td><input type="submit" value="提交"/></td>
           </tr>
       </table>
   </form>
```

RegisterServlet.java注册响应验证

```java
import org.omg.PortableServer.REQUEST_PROCESSING_POLICY_ID;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class HelloForm
 */
@WebServlet(
        name="HelloForm",
        urlPatterns = "/register.htm",
        loadOnStartup = 0
)
public class HelloForm extends HttpServlet {
    private static final long serialVersionUID = 1L;

    HelloUser User  = new HelloUser();
    /**
     * @see HttpServlet#HttpServlet()
     */
    public HelloForm() {
        super();
        // TODO Auto-generated constructor stub
    }

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request,response);
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      String userName = request.getParameter("username");
      String passwd = request.getParameter("passwd");
      String confirdPasswd = request.getParameter("confirdPasswd");

      String email = request.getParameter("email");

      List<String> errors = new ArrayList<String>();
      if(isVaildUesrname(userName)) {
          errors.add("用户名空或已存在");
      }
      if(!isValidEmail(email)) {
          errors.add("无效的邮箱号码！");
      }
      if(isValidPassword(passwd,confirdPasswd)) {
          errors.add("密码问题！");
      }
      if(!errors.isEmpty()) {
          request.setAttribute("errors",errors);
          request.getRequestDispatcher("/error.htm").forward(request,response);
      }else {
          HelloUser user= HelloUser.getInstance();
          Map<String,String> map= user.getUserMap();
          map.put(userName,passwd+"##"+email);
          request.getRequestDispatcher("/success.htm").forward(request,response);
      }
    }

    //正则表达式验证邮箱
    public  boolean isValidEmail(String email) {
        boolean flag =false;
        if(email == null || "".equals(email)) flag = false;
        if(email!=null && ! "".equals(email)) {
            flag = email.matches("^[\\w-]+(\\.[\\w-]+)*@[\\w-]+(\\.[\\w-]+)+$");
        }
        return  flag;
    }

    //验证用户名是否存在
    //根据key 判断是否存在
    public boolean isVaildUesrname(String userName) {
        HelloUser user =HelloUser.getInstance();
        Map<String,String> map = user.getUserMap();
        if(userName != null && !userName.equals("")) {
            if(map.get(userName)!=null && ! map.get(userName).equals("")) {
                return true;
            }else {
                return false;
            }
        }
        if(userName == null && userName.equals("")) {
            return true;
        }
        return  true;
    }

    //验证密码
    public boolean isValidPassword(String passwd,String confirdPasswd) {
        return passwd == null || confirdPasswd == null || passwd.length()<6 || confirdPasswd.length()<6 || !passwd.equals(confirdPasswd);
    }
}
```

用户类（Pojo）

```java
import java.util.HashMap;
import java.util.Map;

public class HelloUser {
    private Map<String,String> helloMessage = new HashMap<String,String>();
    private static  HelloUser user = null;
    public HelloUser() {
        helloMessage.put("John","1115555##a@sina.com");
        helloMessage.put("Smith","2225555##j@bai.com");
        helloMessage.put("Rose","3335555##wang@sina.com");
    }

    public static  HelloUser getInstance() {
        if(user == null) {
            user=new HelloUser();
        }
        return user;
    }
    //根据用户，返回Map消息：
    public String sayHello(String userName) {
        return helloMessage.get(userName);
    }
    public Map<String,String> getUserMap() {
        return helloMessage;
    }
}

```

成功注册的Servlet代码

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(
        name="successServlet",
        urlPatterns = {"/success.htm"}
)

public class SuccessServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request,response);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<BODY>");
        out.println("注册成功");
        out.println("</BODY>");
        out.flush();
        out.close();
    }
}

```

失败注册的Servlet代码

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.List;

@WebServlet(
        name = "errorServlet",
        urlPatterns = {"/error.htm"}
)

public class ErrorServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request,response);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<BODY>");
        out.println("注册失败");
        List<String> list =(List<String>) request.getAttribute("errors");
        for(String str:list) {
            out.println(str+"<br>");
        }
        out.println("</BODY>");
        out.flush();
        out.close();
    }
}

```

登录login.jsp

```java
<form  action = "<%=request.getContextPath()%>/login.htm" method ="POST" >
    <table border="1">
        <tr>
            <td>登录名:</td>
            <td><input name = "username"/></td>
        </tr>
        <tr>
            <td>密码：</td>
            <td><input name="passwd" type="password" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="提交"/></td>
        </tr>
    </table>
</form>
```

loginServlet.java

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;

@WebServlet(
        name = "loginServlet",
        urlPatterns = {"/login.htm"}
)
public class LoginServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request, response);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String userName = request.getParameter("username");
        String passwd = request.getParameter("passwd");
        if(checkLogin(userName,passwd)) {
            request.getRequestDispatcher("/member.htm").forward(request,response);
        }else {
            response.sendRedirect("login.jsp");
        }
    }

    public  boolean checkLogin(String username,String passwd) {
        HelloUser user = HelloUser.getInstance();
        Map<String,String> map = user.getUserMap();
        if(username!=null && !"".equals(username)&& passwd!=null && !"".equals(passwd)) {
            String[] arr = map.get(username).split("##");
            if(arr[0].equals(passwd)) return true;
            else return  false;
        }else {
            return  false;
        }
    }
}
```

登录成功的界面

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(
        name = "memberServlet",
        urlPatterns = {"/member.htm"}
)
public class MemberServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request,response);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<BODY>");
        out.println("登录成功");
        out.println("登录成功");
        out.println("</BODY>");
        out.flush();
        out.close();
    }
}

```

以上就是综合练习部分了，不仅仅是用到了Servlet知识，同时也对Java中的数据结构，模块设计与联系做了有效的巩固。希望有所帮助。
