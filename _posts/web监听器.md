## 什么是web监听器
	web监听器主要是对Servlet对象进行监听和动作的，它可以监听客户端的请求，服务端的操作等

## web监听器的分类
	根据监听的域对象可以分为三类
		1.监听Servlet上下文对象的监听器
			1. 对Servlet上下文对象初始化及对象销毁动作进行监听的  ServletContextListener
			2. 对Servlet上下文对象的属性进行监听的 ServletContextAttributeListener
		2.监听HTTP会话 Session对象的监听器
			1. 对HTTP Session对象初始化及销毁动作进行监听的  HttpSessionListener
			2. 对HTTP Session对象的属性进行监听的  HttpSessionAttributeListener
			3. 对HTTP Session对象设置的属性对象进行监听的 HttpSessionBindingListener
			4. 对HTTP Session对象的活跃性进行监听的 HttpSessionActivationListener
		3.监听客户端请求Servlet Request对象的监听器
			1. 对Servlet Request对象初始化及销毁动作进行监听的 ServletRequestListener
			2. 对Servlet Request对象的属性进行监听的 ServletRequestAttributeListener
## 各监听器的使用Demo
	 注：本文使用的是Servlet3.0，所以基于@WebListener注解进行代码演示，如果是其他版本只需在web.xml文件中配置listener即可
	1. 监听Servlet上下文对象的 ServletContextListener
		要对Servlet上下文 对象进行监听只需实现ServletContextListener接口即可，此接口只有两个方法，分别为Servlet启动初始化方法contextInitialized（可以对ServletContext做相应的初始化操作），以及Servlet销毁方法contextDestroyed（可以对ServletContext所持有的资源进行释放）；在tomcat中通start命令启动时，监听到Servlet对象的创建后contextInitialized方法会被执行，当执行shutdown命令时，监听到Servlet对象将被销毁，contextDestroyed方法会被执行。
```
@WebListener
public class MyServletContextListener implements ServletContextListener {
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("---------------------------------------------");
        System.out.println("MyServletContextListener contextInitialized");
        System.out.println("---------------------------------------------");
    }

    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("---------------------------------------------");
        System.out.println("MyServletContextListener contextDestroyed");
        System.out.println("---------------------------------------------");
    }
}
```

![](https://s1.51cto.com/images/blog/201712/25/3ccb90fee93ad13783443d1af1d9abcc.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

	2.监听Servlet上下文对象属性变化的 ServletContextAttributeListener
	要对Servlet上下文 对象的属性变化进行监听只需实现ServletContextAttributeListener接口即可，此接口只有三个方法，分别为属性新增时的监听方法（attributeAdded），通过ServletContextAttributeEvent 参数可以对ServletContext及新增的属性内容进行操作；已有属性内容被替换时的监听方法（attributeReplaced），此方法在属性被重复赋值（内容替换）时调用，可通过ServletContextAttributeEvent 参数进行操作；当ServletContext的属性被移除时的监听方法（attributeRemoved），通过ServletContextAttributeEvent 参数获取ServletContext属性内容时已是空。
```
@WebListener
public class MyServletContextAttributeListener implements ServletContextAttributeListener {
    public void attributeAdded(ServletContextAttributeEvent event) {
        System.out.print("MyServletContextAttributeListener attributeAdded : [value=");
        ServletContext servletContext = event.getServletContext();
        Enumeration<String> attributeNames = servletContext.getAttributeNames();
        while (attributeNames.hasMoreElements()) {
            System.out.print(attributeNames.nextElement() + ", ");
        }
        System.out.println("]");
        System.out.println("add ServletContextAttribute and value = " + servletContext.getAttribute("name"));

    }

    public void attributeRemoved(ServletContextAttributeEvent servletContextAttributeEvent) {
        ServletContext servletContext = servletContextAttributeEvent.getServletContext();
        System.out.println("MyServletContextAttributeListener attributeRemoved and value = " + servletContext.getAttribute("name"));

    }

    public void attributeReplaced(ServletContextAttributeEvent servletContextAttributeEvent) {
        ServletContext servletContext = servletContextAttributeEvent.getServletContext();
        System.out.println("MyServletContextAttributeListener attributeReplaced and value = " + servletContext.getAttribute("name"));
    }
}

JSP代码：
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<h2>Hello ServletContextAttributeListener!</h2>
<p>ServletContext changes</p>
<p>request.getServletContext() setAttribute</p>
<p><b>request.getServletContext().setAttribute("name", "add value");</b></p>
<%
    ServletContext servletContext = request.getServletContext();
    servletContext.setAttribute("name", "add value");
%>
<hr/>
<p>request.getServletContext() replaceAttribute</p>
<p><b>request.getServletContext().setAttribute("name", "replace value");</b></p>
<%
    servletContext.setAttribute("name", "replace value");
%>
<hr/>
<p>request.getServletContext() removeAttribute</p>
<p><b>request.getServletContext().removeAttribute("name");</b></p>
<%
    servletContext.removeAttribute("name");
%>
</body>
</html>
```
![](https://s1.51cto.com/images/blog/201801/09/d28d987a99c3e3870deaab5e59c2463c.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)


	3. 对于HttpSession的创建，销毁以及属性的监听与ServletContext类似，分别对应HttpSessionListener和HttpSessionAttributeListener，它们的接口方法与ServletContext接口方法也是类似，此处不再赘述，另外，对于HttpSession的操作可通过HttpSessionEvent和HttpSessionBindingEvent参数进行获取，贴出代码。
```
@WebListener
public class MyHttpSessionListener implements HttpSessionListener {
    public void sessionCreated(HttpSessionEvent httpSessionEvent) {
        HttpSession session = httpSessionEvent.getSession();
        System.out.println("MyHttpSessionListener sessionCreated : " + session.getId());
    }

    public void sessionDestroyed(HttpSessionEvent httpSessionEvent) {
        HttpSession session = httpSessionEvent.getSession();
        System.out.println("MyHttpSessionListener sessionDestroyed : " + session.getId());
    }
}

@WebListener
public class MyHttpSessionAttributeListener implements HttpSessionAttributeListener {
    public void attributeAdded(HttpSessionBindingEvent httpSessionBindingEvent) {
        HttpSession session = httpSessionBindingEvent.getSession();
        System.out.println("MyHttpSessionAttributeListener attributeAdded and id = " + session.getId() + ", value = " + session.getAttribute("name"));
    }

    public void attributeRemoved(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("MyHttpSessionAttributeListener attributeRemoved and id = " + session.getId() + ", value = " + event.getValue());
    }

    public void attributeReplaced(HttpSessionBindingEvent httpSessionBindingEvent) {
        HttpSession session = httpSessionBindingEvent.getSession();
        System.out.println("MyHttpSessionAttributeListener attributeReplaced and id = " + session.getId() + ", value = " + session.getAttribute("name"));
    }
}

JSP代码：
		注：HttpSessionListener的监听在通过浏览器访问服务时即可动作，所以此处没有加操作代码。
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<h2>Hello HttpSessionAttributeListener</h2>
<p>HttpSessionAttribute changes</p>
<p>request.getSession() setAttribute</p>
<p><b>request.getSession().setAttribute("name", "add value");</b></p>
<%
    HttpSession httpSession = request.getSession();
    httpSession.setAttribute("name", "add value");
%>
<hr/>
<p>request.getSession() replaceAttribute</p>
<p><b>request.getSession().setAttribute("name", "replace value");</b></p>
<%
    httpSession.setAttribute("name", "replace value");
%>
<hr/>
<p>request.getSession() removeAttribute</p>
<p><b>request.getSession().removeAttribute("name");</b></p>
<%
    httpSession.removeAttribute("name");
%>
<hr/>
</body>
</html>
```
![](https://s1.51cto.com/images/blog/201801/09/b0db5bb6e6b91fef26917e1ab4763dd9.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

	4. HttpSessionBindingListener用于监听其实现类本身，当实现类对象被添加到session中时（session.setAttribute()），会调用valueBound方法，参数HttpSessionBindingEvent中包含session和实体信息，当实现类对象被从session中移除（session.removeAttribute()）或者session失效时，会调用valueUnbound方法。另外，此listener的方法在HttpSessionAttributeListener方法之前执行。
```
public class MyHttpSessionBindingListener implements HttpSessionBindingListener {
    private String name;

    public MyHttpSessionBindingListener(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "MyHttpSessionBindingListener{" +
                "name='" + name + '\'' +
                '}';
    }

    //被设置到session中（setAttribute）
    @Override
    public void valueBound(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("HttpSessionBindingListener bean被设置到session中，sessionId=" + session.getId() + ", bean: " + event.getValue());
    }

    //从session中解除（removeAttribute）
    @Override
    public void valueUnbound(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("HttpSessionBindingListener bean在session中被移除，sessionId=" + session.getId() + ", bean: " + event.getValue());
    }
}

JSP代码：
<%@ page import="MyHttpSessionBindingListener" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<h2>Hello HttpSessionBindingListener</h2>
<p>HttpSessionBinding changes</p>
<p>session setAttribute</p>
<%
    Object name = session.getAttribute("name");
    if (name == null) {
        MyHttpSessionBindingListener bindingListener = new MyHttpSessionBindingListener("SessionBean");
        session.setAttribute("name", bindingListener);
%>
<p><b>session.setAttribute("name", "new MyHttpSessionBindingListener("SessionBean")");</b></p>
<%} else {%>
<p><b>session.getAttribute("name") : <%= name%>
</b></p>
<%}%>
<hr/>
<p>request.getSession() removeAttribute</p>
<p><b>request.getSession().removeAttribute("name");</b></p>
<%
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    session.removeAttribute("name");
%>
<hr/>
</body>
</html>

```

![](https://s1.51cto.com/images/blog/201801/09/2a91691b67de32d8b1e74a91151fbebd.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

	5. Session的持久化操作：对于Tomcat服务器来说，可以通过配置context.xml文件来启用此功能（Tomcat中默认没有启用此功能），在Session即将超时时生成sessionId.session文件，配置如下：
		注：当Session在系统中过期时，则Session信息会被清除，同时sessionId.session文件也会被删除
```
			<Context>
					<Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
							<Store className="org.apache.catalina.session.FileStore" directory="H:\selfwork\"></Store>
					</Manager>
			</Context>
			* maxIdleSwap－session处于不活动状态长时间(s)，sesson对象转移到FileStore中；(－1表示没有限制)
			* directory - 存储Session文件的路径
			PersistentManager提供了把Session对象保存到Session store中的管理器，Tomcat目前提供了两种Store的实现方式方式，分别为org.apache.Catalina.FileStore，将Session对象以本地文件的方式存储在指定目录中；org.apache.Catalina.JDBCStore，将Session对象存储在指定的数据库中，本文以文件的形式进行演示，并且本文对于PersistentManager 和 Session store 的配置，原理没有做深入解析，只是做Session的持久化功能说明。
```
	6. HttpSessionActivationListener同样是用于监控实现类本身，当实现类对象bean被添加到session中（session.setAttribute()）后，在session被序列化（钝化）到文件时，sessionWillPassivate方法将被调用（只有保存到Session中的对象才会被监听到），参数HttpSessionEvent含有Session的相关信息，由于现类对象bean会被进行序列化操作，所以实现类同时需要实现Serializable接口。
```
public class MyHttpSessionActivationListener implements HttpSessionActivationListener, Serializable {
    private String name;

    public MyHttpSessionActivationListener(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void sessionWillPassivate(HttpSessionEvent httpSessionEvent) {
        HttpSession session = httpSessionEvent.getSession();
        System.out.println("MyHttpSessionActivationListener sessionWillPassivate(session 即将超时，bean被序列化到本地) and sessionId = " + session.getId() + ", " + this.toString());
    }

    public void sessionDidActivate(HttpSessionEvent httpSessionEvent) {
        HttpSession session = httpSessionEvent.getSession();
        System.out.println("MyHttpSessionActivationListener sessionDidActivate(session 即将激活，bean被反序列化到内存) and sessionId = " + session.getId() + ", " + this.toString());
    }

    @Override
    public String toString() {
        return "MyHttpSessionActivationListener{" +
                "name='" + name + '\'' +
                '}';
    }
}

JSP代码：
<%@ page import="com.MyHttpSessionActivationListener" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<h2>Hello HttpSessionActivationListener</h2>
<p>HttpSessionActivation changes</p>
<%
    Object name = session.getAttribute("name");
    if (name == null) {
        MyHttpSessionActivationListener activationListener = new MyHttpSessionActivationListener("SessionActivationBean");
        session.setAttribute("name", activationListener);
%>
<p>session setAttribute</p>
<p><b>session.setAttribute("name", "activationListener");</b></p>
<%} else {%>
<p>session getAttribute</p>
<p>
    <b>session.getAttribute("name") : <%= name%>
    </b>
</p>
<%}%>
<hr/>
</body>
</html>
```
![](https://s1.51cto.com/images/blog/201801/10/ba732cb9016987cce1f6aaab13d5d523.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
![](https://s1.51cto.com/images/blog/201801/10/00a2d8b45a8f1c038bb2b8a402ad959b.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
```
当内存中没有session信息时，通过指定sessionId访问服务器时（或者在原有页面中重新刷新时），根据sessionId.session文件（反序列化）会重新载入序列化的Session信息，同时，sessionDidActivate方法被调用，参数HttpSessionEvent中含义反序列化的Session及对象bean信息
```
![](https://s1.51cto.com/images/blog/201801/10/be1c918fb8fbc179849dda175b32704e.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

	7. 监听客户端请求Servlet Request对象的监听器与ServletContext监听器同样类似，分别对应ServletRequestListener 和 ServletRequestAttributeListener，它们的接口方法与ServletContext接口方法也是类似，此处不再赘述，另外，对于ServletRequest的操作可通过ServletRequestEvent和ServletRequestAttributeEvent参数进行获取，贴出代码。
```
 ServletRequestListener 监听器
@WebListener
public class MyServletRequestListener implements ServletRequestListener {
    public void requestDestroyed(ServletRequestEvent servletRequestEvent) {
        ServletRequest servletRequest = servletRequestEvent.getServletRequest();
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        String attribute = (String) request.getAttribute("name");
        System.out.println("MyServletRequestListener requestDestroyed and request attribute : " + attribute);
    }

    public void requestInitialized(ServletRequestEvent servletRequestEvent) {
        ServletRequest servletRequest = servletRequestEvent.getServletRequest();
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        request.setAttribute("name", "requestInitialized");
        System.out.println("MyServletRequestListener requestInitialized and request set attribute : [name = requestInitialized]");
    }
}

JSP代码：
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<h2>Hello ServletRequestListener!</h2>
<p>ServletRequestListener changes</p>
<p>request get init attribute</p>
<p><b>request.getAttribute("name");</b></p>
<%
    String name = (String) request.getAttribute("name");
%>
<p>request init value is : <%= name%>
</p>
<hr/>
<p>request.setAttribute</p>
<p><b>request.setAttribute("name", "add ServletRequestListener value");</b></p>
<%
    request.setAttribute("name", "add ServletRequestListener value");
%>
<hr/>
</body>
</html>
```
![](https://s1.51cto.com/images/blog/201801/10/5641f13bb5fb31f1d2a8e667a21df454.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
```
ServletRequestAttributeListener监听器
@WebListener
public class MyServletRequestAttributeListener implements ServletRequestAttributeListener {
    public void attributeAdded(ServletRequestAttributeEvent event) {
        Object value = event.getValue();
        System.out.println("MyServletRequestAttributeListener attributeAdded and content is : " +
                event.getName() + " = " + value);
    }

    public void attributeRemoved(ServletRequestAttributeEvent event) {
        Object value = event.getValue();
        System.out.println("MyServletRequestAttributeListener attributeRemoved and content is : " +
                event.getName() + " = " + value);
    }

    public void attributeReplaced(ServletRequestAttributeEvent event) {
        Object value = event.getValue();
        System.out.println("MyServletRequestAttributeListener attributeReplaced and content is : " +
                event.getName() + " = " + value);
    }
}

JSP代码：
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<h2>Hello ServletRequestAttributeListener!</h2>
<p>ServletRequestAttributeListener changes</p>
<p>request get init attribute</p>
<p><b>request.getAttribute("name");</b></p>
<%
    String name = (String) request.getAttribute("name");
%>
<p>request init value is : <%= name%>
</p>
<hr/>
<p>request.setAttribute</p>
<p><b>request.setAttribute("name", "add ServletRequestListener value");</b></p>
<%
    request.setAttribute("name", "add ServletRequestListener value");
%>
<hr/>
<p>request replace attribute</p>
<p><b>request.setAttribute("name", "add ServletRequestListener replace value");</b></p>
<%
    request.setAttribute("name", "add ServletRequestListener replace value");
%>
<hr/>
<p>request remove attribute</p>
<p><b>request.removeAttribute("name");</b></p>
<%
    request.removeAttribute("name");
%>
<hr/>
</body>
</html>
```
![](https://s1.51cto.com/images/blog/201801/10/3ecc6631cfed7924afa129bdf74e766c.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

## 总结
	第一次以博客的形式记录了自己学习web listener的经过，与之前的使用云笔记相比，发现自己需要提升的不仅仅是对各种技术的学习和掌握，对于自己的表达能力更需要进行训练和提升，虽然只是把listener的各个demo实例以及自己的理解记录下来，还是进行修修补补的更改了很多次。万事总有开头，每一天的点滴进步，才能成就非凡的卓越。