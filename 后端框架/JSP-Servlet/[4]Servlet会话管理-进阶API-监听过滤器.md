进入到Servlet3的部分，这里主要以会话管理，Servlet进阶API,监听器与过滤器等相应的内容。会话管理是对于SessionID,Cookie的管理操作。进阶API主要是ServletConfig,ServletContext为例。监听与过滤器主要是对于请求进行的处理。

[Servlet1知识点](https://blog.csdn.net/QAZJOU/article/details/126431931?spm=1001.2014.3001.5502)
[Servlet2知识点](https://blog.csdn.net/QAZJOU/article/details/126432249?spm=1001.2014.3001.5502)

等Servlet 4（主要内容是异步处理，数据库开发）上传后将以**一个项目为实例**进行进阶讲解，以JSP+Servlet项目结束后将紧接着SpringBoot来看了。**关于标签页等前端内容将不再进行系统讲解，后期将以Bootstrap，Vue配合Django项目为例。**

这里多说几句，对于Servlet都是以几个模块为例的， **web开发主要的还是以项目为主** ，主要可以使用的开源平台例如Gitee,Github进行开发。

### 目录

* [会话管理](https://editor.csdn.net/md/?articleId=126637717#_9)

  * [会话管理的基本原理](https://editor.csdn.net/md/?articleId=126637717#_11)

    * [使用隐藏域](https://editor.csdn.net/md/?articleId=126637717#_15)
    * [使用Cookie](https://editor.csdn.net/md/?articleId=126637717#Cookie_20)
    * [使用URL重写](https://editor.csdn.net/md/?articleId=126637717#URL_28)
  * [HttpSession会话管理](https://editor.csdn.net/md/?articleId=126637717#HttpSession_37)
  * [使用HttpSession管理会话](https://editor.csdn.net/md/?articleId=126637717#HttpSession_41)
  * [HttpSession 与URL 重写](https://editor.csdn.net/md/?articleId=126637717#HttpSession_URL__109)
  * [HttpSession 生命周期](https://editor.csdn.net/md/?articleId=126637717#HttpSession__122)
  * [HttpSession会话管理的实例](https://editor.csdn.net/md/?articleId=126637717#HttpSession_137)
* [Servlet 进阶API](https://editor.csdn.net/md/?articleId=126637717#Servlet_API_193)
* [Servlet ,ServletConfig, GenericServlet](https://editor.csdn.net/md/?articleId=126637717#Servlet_ServletConfig_GenericServlet_197)
* [使用ServletConfig](https://editor.csdn.net/md/?articleId=126637717#ServletConfig_231)
* [使用ServletContext](https://editor.csdn.net/md/?articleId=126637717#ServletContext_291)
* [监听器](https://editor.csdn.net/md/?articleId=126637717#_301)
* [ServletContext事件](https://editor.csdn.net/md/?articleId=126637717#ServletContext_305)
* [HttpSession 事件监听器](https://editor.csdn.net/md/?articleId=126637717#HttpSession__354)
* [HttpSessionIDListener :](https://editor.csdn.net/md/?articleId=126637717#HttpSessionIDListener__356)
* [HttpSessionAttributeListener](https://editor.csdn.net/md/?articleId=126637717#HttpSessionAttributeListener_439)
* [HttpSessionBindingListener](https://editor.csdn.net/md/?articleId=126637717#HttpSessionBindingListener_450)
* [过滤器](https://editor.csdn.net/md/?articleId=126637717#_461)
* [实现与设置过滤器](https://editor.csdn.net/md/?articleId=126637717#_466)
* [Filter接口](https://editor.csdn.net/md/?articleId=126637717#Filter_470)

# 会话管理

## 会话管理的基本原理

会话管理，通过以下来实现：使用隐藏域、使用Cookie、使用URL重写。

### 使用隐藏域

隐藏域是将表单内容在显示时隐藏，即不显示数据。在JSP将input标签的type设定为hidden,即生成一个隐藏表单域。再将会话的唯一标识记录到隐藏域中的value属性，并设定name属性。表单提交时，会话标识也被提交到服务端了，根据它找到对应的会话对象。
使用隐藏域，需要每个页面包含标识表单，这样才能跳转时候保存会话并进行管理。但是安全性差，目前在开发中很少使用。

### 使用Cookie

利用Cookie实现会话管理是最容易的实现方式，也是使用较多的方法。原理是服务端保存的会话对象中设定会话的唯一标识，客户端将会话标识保存在Cookie.当发起请求，从Cookie中取得会话标识并发送给服务器，服务器在收到请求后，根据发送的会话标识查找会话对象。
目前这个是主流方法。

![在这里插入图片描述](https://img-blog.csdnimg.cn/af2e4302fa964cf59207e61f7ad3efcc.png)

### 使用URL重写

URL重写是指在URL地址末尾添加会话标识，改写原先的URL.本质是用于唯一标识会话的信息以参数的形式添加到URL中，服务端收到请求时，解析标识，利用会话标识查找对象。该方法是用在浏览器Cookie被禁用的情况下，利用该方法实现会话跟踪而不受到参数影响。
问题就是，复制URL会暴露会话标识，信息不安全。

无论使用哪种方式实现会话管理，在服务端需要完成相关代码才能完整实现会话管理的任务。

## HttpSession会话管理

HTTP协议是无意识单向(服务端不能主动连接)协议,只能等待并答复客户端请求。所以使用session来保存客户端信息。

### 使用HttpSession管理会话

当用户第一次请求服务器时，就创建了session对象。以key-value形式保存，通过读写方法保存客户的状态信息，例如getAttribute(String key) 用于获得参数,setAttribute(String key,Object value)用于设定参数和参数值。

```java
import javax.servlet.http.HttpSession
HttpSession session =request.getSession() //获取session对象
Session.setAttribute("username","Joah") //设置session属性
```

JSP内置了Session对象，可以直接使用，Servlet使用上述方法获取session对象。若JSP设定了page session=“false” 代表不可用。
login.jsp

```jsp
<%
	String path = request.getContextPath();
%>
<body>
    <form action="<%=path%>/servlet/CheckUser" method = "post">
        用户名: <input name="userID"/>
        密码： <input name="passwd" type="password"/>
        <input type="submit" value="提交"/>
        <input type="reset" value="重置"/>
    </form>
</body>
```

使用Servlet 实现，编写跳转的Servlet类CheckUser

```java
// CheckUser.java
do Post(...)...{
    request.setCharacterEncoding("UTF-8");
    String userID = request.getParameter("userID");
    String passwd = request.getParameter("passwd");
  
    //判断是否为lin1用户且密码相符
    if(userID!=null && "lin1".equals(userID) && passwd!=null && "123456".equals(passwd)) {
        //获得session对象
        HttpSession session = request.getSession();
        //设置user参数
        session.setAttribute("user",userID);
        //跳转页面
        RequestDispatcher dispatcher =request.getRequestDispatcher("/welcome.jsp");
        dispatcher.forward(request,response);
    }else {
        RequestDispatcher dispatcher = request.getRequestDispatcher("/login.jsp");
        dispatcher.forward(request,response);
    }
}

```

```jsp
// welcome.jsp 读取session对象ID
<%
	String user = (String)session.getAttribute("user");
	if(user == null ) {
%>
<jsp:forword page = "/login.jsp"/>
 <%   } %>
<body>
    welcome : <%=user%>
</body>
```

HttpSession管理会话的原理：利用服务器来管理会话的机制，当程序客户端创建session，服务器会检查是否包含一个session标识。如果有服务器就把session检索使用。

### HttpSession 与URL 重写

URL重写在客户端不支持Cookie情况下，实现方式将sessionID 添加到URL.

```java
response.encodeURL("login.jsp");
或者
response.sendRedirect(response.encodeRedirectURL("login.jsp"));

//方法的效果一样，如果支持Cookie那么URL不变，不支持URL会带有jsessionid字符串的地址

```

### HttpSession 生命周期

Servlet 有它的的生命周期，同样，HttpSession也有生命周期：创建 - 使用 - 消亡
**创建**

```java
request.getSession() 或者 request.getSession(true) 
```

**使用**
将sessionID 存入Cookie中。客户端发送请求时候会将sessionID与request一起传送给服务器端；根据请求过来的sessionID与保存在服务器端的session 对应起来，判断是否为同一session。
**消亡**
将浏览器关闭；HttpSession.invalidate() ;session超时
只有访问JSP,Servlet才会创建session，访问静态时候不会创建session对象的。

## HttpSession会话管理的实例

通过在线猜数字来说明session会话管理的过程。
guessNumber.jsp

```jsp
<%
	String flag = request.getParameter("flag");
	String message = "";
	if(flag != null && "larger".equals(flag)) {
        message = "太大了";
    }else if (flag != null && "lessner".equals(flag)) {
        message = "太小了";
    }else if (flag != null && "success".equals(flag)) {
        message = "猜对了";
    }
%>

<body>
    <form action="<%=path%>/servlet/Guess" method ="post" >
        数字: <input name = "guessNumber"/>
        <input type ="sumbit" value="提交" />
        <%=message%>
    </form>
</body>
```

Servlet : Guess.java

```java
doPost(...) ... {
    String guessNumber = request.getParameter("guessNumber");
    int number = Integer.parseInt(guessNumber);
    //产生一个Session ,并获取session的currentNumeber
  
    HttpSession session = request.getSession();
    Integer currentNumber = (Integer) session.getAttribute("currentNumber");
    String context = request.getContextPath();
    if(currentNumber == null ) {
        //产生1-50的随机数
        currentNumber = 1+(int)(Math.random()*50);
        session.setAttribute("currentNumber",currentNumber);
    }
    // 判断所猜数与cur大小
    if(number > currentNumber) {
        response.sendRedirect(context+"/guessNumber.jsp?flag=larger");
    }else if(number < currentNumber) {
        response.sendRedirect(context+"/guessNumber.jsp?flag=lessner");
    }else {
        currentNumber = 1 + (int)(Math.random()*50);
        session.setAttribute("currentNumber",currentNumber);
        response.sendRedirect(context+"/guessNumber.jsp?flag=success");
    }
}
```

# Servlet 进阶API

编写一个Servlet 类，需要在web.xml 或者通过注解方式配置，这样web容器才能读取Servlet信息。对于每个Servlet配置，web都会生成与之对应的ServletConfig对象，可以得到Servlet初始化参数。

## Servlet ,ServletConfig, GenericServlet

GenericServlet 实现了上面两个类的接口，这个使得编写Servlet更加方便，提供了一个简单的方法执行Servlet生命周期。

```java
public abstract class GenericServlet implements Servlet,ServletConfig,java.io.Serializable{
    public GenericServlet() {
      
    }
    public void destory() {
      
    }
    //获得初始化参数方法
    public String getInitParameter(String name) {
      
    }
    //获得参数名称，返回枚举类型
    public Enumeration<String> getInitParameterNames() {
      
    }
    //获得ServletConfig对象
    public ServletConfig getServletConfig() {
        return config;
    }
    //获得ServletContext对象
    public ServletContext getServletContext() {
      
    }
    ....
}
```

GenericServlet类将ServletConfig封装了。

## 使用ServletConfig

容器初始化，会为Servlet创建唯一的ServletConfig对象。读取web.xml，将初始化传给ServletConfig,而ServletConfig作为参数传递到init()
Servlet 3.0开始，允许以注入的方式配置Servlet.

```java
@WebServlet(
	name = "ServletConfigDemo",
    ulrPatterns = {"/servletConfigDemo.do"},
)
```

| @WebServlet属性名 | 描述                      |
| ----------------- | ------------------------- |
| name              | servlet-name              |
| urlPatterns       | URL匹配模式，url-pattern  |
| initParams        | Servlet初始化，init-param |

编写ServletConfigDemo类，输出初始化参数，成功登录

```java
@WebServlet(
        public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取ServletConfig对象
        ServletConfig config = getServletConfig();

        //getInitParameter(name)
        String success = config.getInitParameter("success");
        String error = config.getInitParameter("error");
        System.out.println("success----"+success);
        System.out.println("error----"+error);

        //getInitParameterNames
        Enumeration enumeration = config.getInitParameterNames();
        while(enumeration.hasMoreElements()) {
            String name =(String) enumeration.nextElement();
            String value = config.getInitParameter(name);
            System.out.println("name---"+name);
            System.out.println("value---"+value);
        }

        //getServletContext
        ServletContext servletContext = config.getServletContext();
        System.out.println("servletContext-----"+servletContext);

        //getServletName
        String servletName = config.getServletName();
        System.out.println("servletName---"+servletName);

        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        String userID = request.getParameter("userID");
        String passwd = request.getParameter("passwd");

        //判断是否为lin1
    
    }
)
```

## 使用ServletContext

是Servlet的全局存储信息。服务器启动时，Web容器为Web创建唯一的ServletContext对象。其接口定义了运行Servlet应用程序的环境信息，可以获取请求资源的URL,设置等属性。

| 方法                        | 描述                    |
| --------------------------- | ----------------------- |
| getRealPath(String path)    | 真实路径名              |
| getResource(String uripath) | 返回由path对应的url对象 |
| getServletInfo              | 获取服务器名字          |

# 监听器

监视行为。监听事件来监听请求中的行为而创建的一组类。实现相应的监听接口。触发监听事件，应用服务器会自动调用监听方法。

## ServletContext事件

ServletContextListener：生命周期监听器，用来监听web初始化或者结束时响应的动作事件。
方法是contextInitialized (加载和初始化) ,contextDestroyed （应用即将关闭）
实现ServletContextListener步骤如下：
配置：

```java
<listener>
	<listener-class>
    	MyServletContextListener
    </listener-class>
</listener>

// 或者利用注入的方式注入监听类
@WebListener
@WebListener
public class MyServletContextListener implements ServletContextListener {
    Logger log =Logger.getLogger("MyServletContextListener");
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        //获得对象
        ServletContext context = servletContextEvent.getServletContext();
        String name = context.getInitParameter("username");
        System.out.println(name+"实现接口");
        log.debug("初始化name"+name);
        log.debug("Tomcat启动中"); //使用的是log4j 1.2 jar
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        log.debug("Tomcat关闭");
    }
}
```

web.xml

```xml
<context-param>
        <param-name>username</param-name>
        <param-value>John</param-value>
    </context-param>

    <listener>
        <listener-class>MyServletContextListener</listener-class>
    </listener>
```

## HttpSession 事件监听器

### HttpSessionIDListener :

监听session ID 的变化

```java
public void sessinIDChanged (HttpSessionEvent se,java.lang.String oldSessionID)
```

用于通知sessionID发生了变化。实现方法;

```java
@WebListener
public class MyHttpSessionIDListener implements HttpSessionListener {
  
}

//web.xml配置,同上面
```

下面实现统计在线人数的功能,LoginServlet.java

```java
@WebServlet(
        name = "loginServlet",
        urlPatterns = {"/login.htm"},
        initParams = {
                @WebInitParam(name = "success",value="success.jsp")
        }
)
public class LoginServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public void init() throws ServletException{
        System.out.println("初始化 init loginServlet 方法");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        doPost(request, response);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String userName = request.getParameter("username");
        String passwd = request.getParameter("passwd");

        if(checkLogin(userName,passwd)) {
            request.getSession().setAttribute("user",userName);
            request.getSession().setAttribute("count",MyHttpSessionListener.getCount());
         
            System.out.println(userName+" "+passwd);
        }else {
            response.sendRedirect("login.jsp");
        }
        String suss= getInitParameter("success");
        System.out.println(suss);
        response.sendRedirect(suss);

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

success.jsp

```jsp
<p>
    ${sessionScope.count}    ${sessionScope.user}
</p>
```

关于login.jsp就不再书写。

### HttpSessionAttributeListener

属性改变监听器。

```java
接口3个监听方法：
default void attributeAdded(HttpSessionBindingEvent se) :方法用于通知监听器
default void attributeRemoved(...)
default void attributeReplaced(...)
```

### HttpSessionBindingListener

对象绑定监听器，用来监听属性与通知。

```java
default void valueBound(...)
default void valueUnbound(...)
```

# 过滤器

作用是阻挡某些事情的发生。在服务器端与客户端起到了一个中间组件的作用，对二者之间的数据进行过滤。
利用过滤器实现如下功能：对用户请求进行身份认证；数据过滤与替换；加密与压缩；审核等

## 实现与设置过滤器

在Servlet实现过滤器， **必须实现Filter接口** ，并用注入的方式或者在web.xml定义过滤器。

### Filter接口

接口方法：

```java
default void init(FilterConfig conf):初始化过滤器
public void doFilter(ServletRequest req,ServletResponse res,FilterChain chain):
default void destroy()
```

```java
//注入方式
@WebFilter(
	filterName = "myfilter",
    servletNames = {"*.do"},
    urlPatterns = {"/*"},
    initParams = {
        @WebInitParam(name = "param" , value = "paramvalue")
    },
    dispatcherTypes = {DispatcherType.REQUEST}
)

```

```xml
<filter>
	<description>demo</description>
    <filter-name>myfilter</filter-name>
    <filter-class>Myfilter</filter-class>
  
    <init-param>
    	<param-name>param</param-name>
        <param-value>paramvalue</param-value>
    </init-param>
</filter>

<!--过滤器映射配置-->
<filter-mapping>
	<filter-name>myfilter</filter-name>
    <servlet-name>*.do</servlet-name>
    <url-pattern>/*</url-pattern>
    <dispatcher>REQUEST</dispatcher>
</filter-mapping>
```

**请求封装器**

将请求内容进行修改，例如修改请求字符编码、替换字符、权限验证等。

```java
// EncodingFilter.java
@WebFilter(
	filterName = "encodingFilter",
    urlPatterns ={"/*"},
    initParams ={
        @WebInitParam(name ="ENCODING",value ="UTF-8")
    }
)

public class EncodingFilter implements Filter{
    public void init(FilterConfig filterConfig) throws ServletException {
        encoding = filterConfig.getInitParameter("ENCODING");
        filterName = filterConfig.getFilterName();
        log.debug("编码");
    }
    public void doFilter(...)... {
        request.setCharacterEncoding(encoding); //只对消息体中的数据起作用，对URL不起作用。对POST请求没有问题，但是对get会出现乱码。
        response.setCharacterEncoding(encoding);
        HttpServletRequest req =(HttpServletRequest)request;
        Systems.out.println("过滤");
    }
  
    public void destroy() {
        log.debug("destroy");
    }
}

```

鉴于对URL不起作用，可以编写请求包装类.

```java
public class RequestEncodingWrapper extends HttpServletRequestWrapper{
    //重新定义getParameter方法
    public String getParameter(String name) {
		String value = getRequest().getParameter(name);
        try{
			if(value != null && ! "".equals(value)) {
                value = new String (value.trim().getBytes("ISO-8859-1"),encoding);
            }
        }catch(UnsupportedEncodingException ex) {
            ex.printStackTrace();
        }
        return value;
    }
}
```

**响应封装器**
将响应内容进行统一修改，例如压缩输出内容，替换输出内容等。
编写一个封装器缓存response的内容，这是一个简易的过滤器，

```java
// ResponseReplaceWrapperr.java
public class ResponseReplaceWrapperr extends HttpServletResponseWrapper {

    private  CharArrayWriter charWriter = new CharArrayWriter();

    public ResponseReplaceWrapperr(HttpServletResponse response) {
        super(response);
    }

    public PrintWriter getWriter() throws IOException {
        return new PrintWriter(charWriter);
    }

    public CharArrayWriter getCharWriter() {
        return charWriter;
    }
}

```

编写内容过滤器：这里使用了Map进行筛选过滤

```java
// ReplaceFilter.java
@WebFilter(
        filterName = "replaceFilter",
        urlPatterns = {"/*"},
        initParams = {
                @WebInitParam(name="filePath",value="replace_ZH.properties")
        }
)

public class ReplaceFilter implements Filter {

    private Properties propert = new Properties();

    public void init(FilterConfig filterConfig) throws ServletException {
        //通过filterConfig
//        String filePath = filterConfig.getInitParameter("filePath");
//        try{
//            propert.load(ReplaceFilter.class.getClassLoader().getResourceAsStream(filePath));
//        }catch (FileNotFoundException ex) {
//            ex.printStackTrace();
//        }catch (IOException ex) {
//            ex.printStackTrace();
//        }
    }

    public void doFilter(ServletRequest request , ServletResponse response , FilterChain chain) throws IOException,ServletException{
        Map<String,String> map = new HashMap<String,String >();
        map.put("赌博","***");
        HttpServletResponse res = (HttpServletResponse) response;
        //实例化响应器包装类
        ResponseReplaceWrapperr resp = new ResponseReplaceWrapperr(res);
        chain.doFilter(request,resp);

        //缓存输出字符
        String outString = resp.getCharWriter().toString();

        //循环替换不合法的字符
        for(Object o:map.keySet()) {
            String key = (String) o;
            outString = outString.replace(key,map.get(key));
        }

        //利用原先的输出字符
        PrintWriter out = res.getWriter();
        out.write(outString);
    }
    public void destroy() {

    }
}

```

```java
// TestServletFilter.java
@WebServlet(
        urlPatterns = {"/Test.do"},
        loadOnStartup = 0,
        name ="testServletFilter"
)
public class TestServletFilter extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        response.setContentType("text/html;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.println("<BODY>");
        out.println("dfdasf  赌博");
        out.println("</BODY>");
        out.flush();
        out.close();
    }
}

```

但运行的时候，赌博会变成***，实现了过滤功能。

以上就是基本内容了！
