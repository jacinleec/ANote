在之前学习过python web后并实践了一个项目后（目前我也在使用django + bootstrap在独立开发部署一个管理系统，有望可以开源放到gitee/github供大家学习参考与交流）

下面一个阶段以Java EE为主，层层深入，从最简单的JSP+Servlet入手，到Spring boot框架，（不建议先学Spring,配置相当麻烦，比Servlet还麻烦很多,下面的博客提供了 **最全的配置Tomcat方法** ）再到Spring Cloud微服务等，同时接触Ruo-Yi来快速搭建。

 **关于Servlet 配置(IDEA配置Tomcat服务器）可以看我这篇博客** ，[点击此处](https://blog.csdn.net/QAZJOU/article/details/121097745?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166091475016781683952125%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=166091475016781683952125&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-121097745-null-null.nonecase&utm_term=tomcat&spm=1018.2226.3001.4450) 本人在 2022年亲测依旧有效， **本篇博客也成为收藏最多帮助最广的博客之一** ,希望对你也有所收获。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1e872e99dde04a8eb82e6234c240d0ef.png)

JSP语法基本就是换个体系的Java,所以 **Java基本功非常重要** 。当然这里浅谈以下如何运行.jsp文件。这里我用的是IDEA,注意此时你已经按照我上述博客**安装了Tomcat与JDK**了。一般只要在index.jsp改代码就行了，当然也可以在统一web目录下进行多个jsp书写，但是第一个跳转的永远是Index.jsp。原因在于web.xml配置与idea运行配置里面，关于其他运行配置将在后期详细补充。

![在这里插入图片描述](https://img-blog.csdnimg.cn/345f78f68a3d4dfab8616b642fa8ad0e.png)

### 目录

* [环境介绍](https://editor.csdn.net/md/?articleId=126431931#_15)
* [JSP简介](https://editor.csdn.net/md/?articleId=126431931#JSP_22)
* [JSP基础语法](https://editor.csdn.net/md/?articleId=126431931#JSP_69)
* [JSP内置对象](https://editor.csdn.net/md/?articleId=126431931#JSP_153)
  * [request对象](https://editor.csdn.net/md/?articleId=126431931#request_157)
  * [response对象](https://editor.csdn.net/md/?articleId=126431931#response_269)
  * [session对象](https://editor.csdn.net/md/?articleId=126431931#session_316)
  * [application 对象](https://editor.csdn.net/md/?articleId=126431931#application__408)
  * [out对象](https://editor.csdn.net/md/?articleId=126431931#out_446)
  * [page对象](https://editor.csdn.net/md/?articleId=126431931#page_468)

## 环境介绍

静态网页展示的内容是不变的，而动态网页会随身变化，内容大部分来自于数据库的变化。目前流行的动态网页技术是ASP php JSP.
ASP:中间件，将web的请求转入到IIS解释器中，IIS将ASP上的Script脚本全部解析执行。缺点不能跨平台，只在windows平台上。
PHP：流行的动态网页技术，一种html内嵌式的语言，融合了Java,C,Perl.很多网站采用PHP编写自身的网页。缺点是安装复杂。
JSP：(java server pages)服务器端脚本，由html和嵌入式java组成。简单易用，完全面对对象。

## JSP简介

首先客户端向web服务器提出请求，然后JSP负责将页面转化为Servlet，Servlet经过虚拟机编译成类文件，再把类文件加载到内存中执行，最后由服务器将处理结果返回给客户端。

```html
<body>
    <% 
       int count=0;
       for(int i=1;i<10;i++) {
    	count+=i;
    	}
    	out.print(" "+count);
       %>
</body>
```

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-wDdTSSHK-1660914492865)(C:\Users\17399\AppData\Roaming\Typora\typora-user-images\image-20220814222057702.png)]](https://img-blog.csdnimg.cn/b6f599c0389d492b85c7325c18be7d83.png)

可以在server.xml更改服务端口和改变web默认的访问目录。
1.修改端口号

```xml
# server.xml
<Connector port='8080'></Connector>
```

2.修改web默认的访问目录

```xml
<Host name="localhost" appBase= "webapps"
      unpackWARs="true" autoDeploy="true"
      xmlValidation="false" xmlNamespaceAware="false"></Host>


# 修改appBase中的文件夹地址
appBase = "D:\test"
```

3.建立自身的web目录

应用部署再Tomcat服务器的默认webapps目录，也可以部署自己创建的目录下。方法：首先创建目录D:\test，其次配置web目录,在server.xml末尾 加语句

```xml
<Context path="test" docBase ="D:\test" debug="0" reloadable="true"></Context>
```

## JSP基础语法

注释：一般和java混用

```html
<%-- 注释内容 --%> 
```

声明：和java一样.在其页面可以被调用和使用

```html
<%!
    int x,y=60;
    String name = "Joah";
    int chengji(int m,int n) {
    	return m*n;
    }
 %> 
```

JSP表达式：将动态信息显示在页面中

```html
<%=new Date()%>
```

page指令:设置JSP页面的属性和相关功能，基本语法如下:language 默认是java, import需要导入的包，pageEncoding 用于设置页面的编码

```html
<%@ page attribute1="value1" [...attribute n="value n"]%>
```

include指令：在JSP页面生成Servlet 引入需要包含的页文件，可以是html文件或者JSP文件。在编译时导入

```html
<%@ include file="url"%>
```

taglib指令：自定义新的标签在页面执行，语法如下：JSTL标签

```html
<%@taglib uri="tagliburl" prefix="tagPre" %></%@taglib>

例如:
<%@ taglib uri="http://java.sun.com/jstl/core_rt" prefix="c" %></%@>
<c:forEach begin="1" end="10" var="num">
	<c:out value="${num}"></c:out>
</c:forEach>
```

include动作：和指令十分相似，引入文件到目标页面，被访问时导入的。效率稍慢

```html
<jsp:include page="rel url" flush="true"/>
page需要包含的文件路径，flush用于指定输出缓存是否转移到被导入文件中
```

forward动作：转发请求到另一个页面，请求过程中会连同请求的参数数据一起被转发到目标页面中。

```html
<jsp:forward page="rel url" />
page="<%=url %>"
```

param动作：传递参数信息，和上述两个结合使用。格式要统一，否则会出现乱码问题

```html
<jsp:param name="参数名称" value="李四"/>

main.jsp:


<jsp:include page="sub.jsp">
	<jsp:param value="Joan" name="username"/>
</jsp:include>

sub.jsp:
<%
   String username = request.getParameter("username")
   %>

<td>用户名: <%=uername%></td>
```

## JSP内置对象

含义是可以直接在JSP页面使用的对象，使用前不需要声明它们。

### request对象

**方法**

每访问一个页面，就会产生一个HTTP请求。包含了参数值或者信息，可以获取客户端和服务端的信息，如IP，传递参数等。

| 方法                | 方法说明                           |
| ------------------- | ---------------------------------- |
| getParameter()      | 取得请求中指定的参数值，返回String |
| getParameterNames() | 获取参数名称，返回枚举类型         |
| getMethod()         | 获取客户提交信息的方式。即POST,GET |
| getRemoteAddr()     | 获取客户的IP地址                   |
| getHeader()         | 获取HTTPt头文件的指定值            |

**接收请求参数**

获取网页请求参数

浏览器地址栏输入 ?name=jacin&city=shanghai
在url参数传递时候，页面使用？连接请求参数。

```jsp
index.jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" import="java.util.*" %>
<html>
<head>
  <title>Title</title>
</head>
<body  style="text-align: center;">
<%
  String name = request.getParameter("name");
  String city = request.getParameter("city");
  if(name != null && city != null) {
%>
<p> Welcome <%=name%>,你的城市是<%=city%></p>
<%

}else {
%>
<p>欢迎访问</p>
<%
  }
%>
</body>
</html>
```

一般采取表单提交的方式传递参数。在form中，两个重要的属性：action 和method （post)。

```jsp
getPa.jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" import="java.util.*" %>
<html>
<head>
    <title>Title</title>
</head>
<body  style="text-align: center;">
    <form  action="index.jsp" method="post">
        <table align="center">
            <tr>
                <td>姓名</td>
                <td><input type="text" name="name" value="" /></td>
            </tr>
            <tr>
                <td>城市</td>
                <td><input type="text" name="city" value="" /></td>
            </tr>
            <tr>
                <td><input type="submit" value="提交" /></td>
            </tr>
            <tr>
                <td><input  type="reset" value="重置"/></td>
            </tr>
        </table>

    </form>
</body>
</html>
```

 **请求中文乱码的处理** ：
可以在接收页面（即上述的index.jsp)添加 request.setCharacterEncoding(“utd-8”);

```jsp
或者:
在取得参数后，通过转码方式转为合适的字符集
String name = new String(request.getParameter("name").getBytes("ISO-8859-1"),"utf-8")
```

可以编写一个Servlet过滤器来解决中文乱码问题，并可以通过配置解决所有的请求处理字符集问题（推荐）
**获取请求的头部信息**

除了有getHeaders(),有以下方法:String getHeader(String name) ,int getIntHeader(String name)获取整型的表头信息

```jsp
<body  style="text-align: center;">
  <%
    Enumeration<String> e = request.getHeaderNames();
    while(e.hasMoreElements()) {
      String name = e.nextElement();
      String value = request.getHeader(name);
      if(value == null || "".equals(value)) {
        value = "空字符";
      }
     %>
        <p> 表头名称: <%=name%>  对应: <%=value%></p>
     <%
    }
     %>
</body>
```

### response对象

当用户访问一个页面时候，会产生一个HTTP请求，服务器做出响应时调用的是reponse包，实现的是javax.servlet.http.HttpServletResponse。

| 方法                                | 说明                       |
| ----------------------------------- | -------------------------- |
| addHeader(String name,String value) | 向页面添加头和对应的值     |
| sendRedirect(String url)            | 页面重定向                 |
| setContentType(String type)         | 设定页面的MIME类型和字符集 |

**设置头信息**

包括设置返回的MIME类型、返回的字符集、页面中的meta等信息。有两种方法设置

```jsp
reponse.setContentType(String type):type值为 "text/html;charset=utf-8"
page指令： <%@ page contentType="text/html;charset=utf-8 language="java" %>
利用page指令设置字符集相对简单。
```

设置meta信息是指在html页面存在head之间的信息。

```jsp
<meta http-equiv ="x" content =" y">
x：cache-control,y:no-cache:请求和响应遵循的缓存机制策略
x: pragma y:no-cache 设定禁止浏览器从本地缓存中调取页面内容

# setMeta.jsp:
实际开发中直接在html写定，不是在响应中设定
<body>
    <%
    	response.setHeader("refresh","1")
         response.setHeader("keywords/description/cache-control",words)
    %>
</body>
```

设置页面重定向
重定向指一个页面在收到一个访问请求后，根据请求的url重新跳转到其他页面。

```jsp
<%
	response.sendRedirect("sendPage.jsp")
%>
<!DOC>
```

### session对象

web开发中，session对象占据极其重要的位置。判断是否为同一用户，用来记录客户的连接信息等。HTTP协议是一种无状态（不保存连接状态）的协议.因此，如果浏览器没有关闭又有请求了，那么网站可以识别出来。

| 名称                | 说明                                           |
| ------------------- | ---------------------------------------------- |
| 会话                | 打开到关闭浏览器过程称为一个会话               |
| session对象生命周期 | 访问的这段时间                                 |
| session,cookie      | 一一对象，JSP会将创建的session对象存在Cookie中 |

获取session对象ID可以判断会话是否为同一会话。
假设有3个页面，ex3_2.jsp ex3_3.jsp ex3_4.jsp （2和3在一个应用下）
一个web应用的session对象的ID值是唯一的，病情两个应用的参数利用session对象是获取不到值的.

```jsp
# ex3_2.jsp
<body>
    <%
    	String sessionID =session.getID();
    	session.setAttribute("name","john");
         String author = (String) session.getAttribute("author");
    %>
    <form  action="ex3_3.jsp" method ="post">
        <input type="submit" value="转向ex3_3.jsp"/>
    </form>
    <a href="../ch03/ex3_4.jsp" >来到4jsp</a>
</body>

```

**用户登录信息的保存**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4c7419e18c144d05ab8d386cdeea49bc.png)

```jsp
# login.jsp
<body>
    <form action="validate.jsp" method= "post">
        用户名称：<input type ="text" name="username" /><br>
 		密码：<input type="password" name="password" /><br>
      	<input type+"submit" value="登录">
    </form>
</body>

# validate.jsp
<body>
   	<%!
     //声明一个用户集合，模拟数据库取出用户集
    Map<String,String> map=new HaspMap<String,String>();
    bool flag= false;
    map.put("jon","12345"); //添加样例数据
    %>
    <%!
    	//声明验证方法
    boolean validate(String username,String password) {
    	String passwd = map.get(username);
    	if(passwd !=null && passwd.equals(password)) {
            return true;
        }else {
            return false;
        }
}
    %>
  
    <%
    	//获得页面提交的用户名和密码
    	String username = request.getParameter("username");
    	String password = request.getParameter("password");
    	if(username==null || username=="" || password==null || password =="") {
            response.sendRedirect("login.jsp");
        }
    	flag = validate(username.password);
    	if(flag) {
            //保存session对象
            session.setAttribute("username",username);
            session.setAttribute("password",password);
            response.sendRedirect("class.jsp");
        }
    %>
</body>

# logout.jsp:
<%
String name = (String)session.getAttribute("name");
session.removeAttribute("john");
session.invalidate();
reponse.sendRedirect("login.jsp")
%>
退出操作利用简单的invalidate实现的，实际开发中结合Struts框架

```

### application 对象

实现的接口javax.servlet.ServletContext.服务器运行时候有效，关闭则消失了。

| 方法                      | 说明                            |
| ------------------------- | ------------------------------- |
| getAttribute(String name) | 存放在application中关键字含name |
| getServletInfo()          | 获取Servlet当前版本信息         |
| getRealPath(String path)  | 获取指定文件的实际路径          |

**获取指定页面的路径**

```jsp
# application.jsp 用于指定页面输出所在实际路径和相对路径
<%=application.getRealPath("application.jsp") %>
<%=application.getResource("application.jsp") %>
<%=application.getContextPath("application.jsp") %>
```

**设计一个网站计数器**

用于保存访问网站的人数。

```jsp
<%
	Integer count = (Integer) application.getAttribute("count");
	if(count == null) {
        count=1;
    }else {
        count++;
    }
	application.setAttribute("count",count);
%>
<!DOC>

application 对象在web应用时一直存在于服务器，因此保存这种相对全局变量相对占用资源，因此不推荐使用。实际开发，让对象存在于必要的时间段，否则加剧时会造成不足。
```

### out对象

继承javax.servlet.jsp.JspWriter 一个输出流对象。常用的方法就是输出内容到Html

| 方法           | 说明                   |
| -------------- | ---------------------- |
| append(char c) | 将字符添加到输出流     |
| clear()        | 清空缓存               |
| flush()        | 网页流的刷新           |
| println()      | 将内容直接打印         |
| write()        | 只输出与字符相关的数据 |

```jsp
<body>
    <%
    	out.println("<table border'2 align = 'center' " );
    	out.println("<tr>")
    %>
</body>
调用clear之前不要调用flush方法，否则抛IO异常。
```

### page对象

对象实质是java.lang.Object对象，代表转译后的Servlet。page对象是指当前的JSP页面本身，在实际开发中不常用。

| 方法       | 说明                            |
| ---------- | ------------------------------- |
| getClass() | 返回被转译的Servlet类           |
| hashCode() | 返回被转译的Servlet类的哈希代码 |
| toString() | 被转译的Servlet类转换字符串     |

```jsp
<body>
    <%
    	int code = page.hashCode(); //获取page对象中的值
    	String str = page.toString();
    	out.println(code);
    	out.println(str); 
    %>
</body>
```

**config对象**

实现了javax.servlet.ServletConfig 接口，一般用于在页面初始化传递参数。

| 方法             | 说明                               |
| ---------------- | ---------------------------------- |
| getServletName() | 获得Servlet名字                    |
| equals(Obj)      | 比较此时的对象是否和指定的对象相等 |
| toString()       | 获得此对象的值                     |

在web-inf文件的web.XML

```xml
<web-app version="4.0"></web-file-file>

<servlet>
	<servlet-name>
    jspconfigdemo
    </servlet-name>
    <jsp-file>
    /config.jsp
    </jsp-file>
    <init-param>
    <param-name>url</param-name>
     <param-value>baidu.com</param-value>
    </init-param>
</servlet>

<servlet-mapping>
	<serlet-name>
    	jspconfigdemo
    </serlet-name>
    <url-pattern>/config.jsp</url-pattern>
</servlet-mapping>

</web-app>
```

配置Servlet,包括初始化参数。

```jsp
# config.jsp
<body>
    <%
    	String url = config.getInitParameter("url");
    	String str = config.toString();
    	out.config(url)
	%>
</body>
```

一般而言很少使用config对象，因为JSP实质是Servlet.

以上就是JSP全部内容了，希望有所帮助。
