## ʲô��web������
	web��������Ҫ�Ƕ�Servlet������м����Ͷ����ģ������Լ����ͻ��˵����󣬷���˵Ĳ�����

## web�������ķ���
	���ݼ������������Է�Ϊ����
		1.����Servlet�����Ķ���ļ�����
			1. ��Servlet�����Ķ����ʼ�����������ٶ������м�����  ServletContextListener
			2. ��Servlet�����Ķ�������Խ��м����� ServletContextAttributeListener
		2.����HTTP�Ự Session����ļ�����
			1. ��HTTP Session�����ʼ�������ٶ������м�����  HttpSessionListener
			2. ��HTTP Session��������Խ��м�����  HttpSessionAttributeListener
			3. ��HTTP Session�������õ����Զ�����м����� HttpSessionBindingListener
			4. ��HTTP Session����Ļ�Ծ�Խ��м����� HttpSessionActivationListener
		3.�����ͻ�������Servlet Request����ļ�����
			1. ��Servlet Request�����ʼ�������ٶ������м����� ServletRequestListener
			2. ��Servlet Request��������Խ��м����� ServletRequestAttributeListener
## ����������ʹ��Demo
	 ע������ʹ�õ���Servlet3.0�����Ի���@WebListenerע����д�����ʾ������������汾ֻ����web.xml�ļ�������listener����
	1. ����Servlet�����Ķ���� ServletContextListener
		Ҫ��Servlet������ ������м���ֻ��ʵ��ServletContextListener�ӿڼ��ɣ��˽ӿ�ֻ�������������ֱ�ΪServlet������ʼ������contextInitialized�����Զ�ServletContext����Ӧ�ĳ�ʼ�����������Լ�Servlet���ٷ���contextDestroyed�����Զ�ServletContext�����е���Դ�����ͷţ�����tomcat��ͨstart��������ʱ��������Servlet����Ĵ�����contextInitialized�����ᱻִ�У���ִ��shutdown����ʱ��������Servlet���󽫱����٣�contextDestroyed�����ᱻִ�С�
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

	2.����Servlet�����Ķ������Ա仯�� ServletContextAttributeListener
	Ҫ��Servlet������ ��������Ա仯���м���ֻ��ʵ��ServletContextAttributeListener�ӿڼ��ɣ��˽ӿ�ֻ�������������ֱ�Ϊ��������ʱ�ļ���������attributeAdded����ͨ��ServletContextAttributeEvent �������Զ�ServletContext���������������ݽ��в����������������ݱ��滻ʱ�ļ���������attributeReplaced�����˷��������Ա��ظ���ֵ�������滻��ʱ���ã���ͨ��ServletContextAttributeEvent �������в�������ServletContext�����Ա��Ƴ�ʱ�ļ���������attributeRemoved����ͨ��ServletContextAttributeEvent ������ȡServletContext��������ʱ���ǿա�
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

JSP���룺
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


	3. ����HttpSession�Ĵ����������Լ����Եļ�����ServletContext���ƣ��ֱ��ӦHttpSessionListener��HttpSessionAttributeListener�����ǵĽӿڷ�����ServletContext�ӿڷ���Ҳ�����ƣ��˴�����׸�������⣬����HttpSession�Ĳ�����ͨ��HttpSessionEvent��HttpSessionBindingEvent�������л�ȡ���������롣
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

JSP���룺
		ע��HttpSessionListener�ļ�����ͨ����������ʷ���ʱ���ɶ��������Դ˴�û�мӲ������롣
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

	4. HttpSessionBindingListener���ڼ�����ʵ���౾����ʵ���������ӵ�session��ʱ��session.setAttribute()���������valueBound����������HttpSessionBindingEvent�а���session��ʵ����Ϣ����ʵ������󱻴�session���Ƴ���session.removeAttribute()������sessionʧЧʱ�������valueUnbound���������⣬��listener�ķ�����HttpSessionAttributeListener����֮ǰִ�С�
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

    //�����õ�session�У�setAttribute��
    @Override
    public void valueBound(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("HttpSessionBindingListener bean�����õ�session�У�sessionId=" + session.getId() + ", bean: " + event.getValue());
    }

    //��session�н����removeAttribute��
    @Override
    public void valueUnbound(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("HttpSessionBindingListener bean��session�б��Ƴ���sessionId=" + session.getId() + ", bean: " + event.getValue());
    }
}

JSP���룺
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

	5. Session�ĳ־û�����������Tomcat��������˵������ͨ������context.xml�ļ������ô˹��ܣ�Tomcat��Ĭ��û�����ô˹��ܣ�����Session������ʱʱ����sessionId.session�ļ����������£�
		ע����Session��ϵͳ�й���ʱ����Session��Ϣ�ᱻ�����ͬʱsessionId.session�ļ�Ҳ�ᱻɾ��
```
			<Context>
					<Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
							<Store className="org.apache.catalina.session.FileStore" directory="H:\selfwork\"></Store>
					</Manager>
			</Context>
			* maxIdleSwap��session���ڲ��״̬��ʱ��(s)��sesson����ת�Ƶ�FileStore�У�(��1��ʾû������)
			* directory - �洢Session�ļ���·��
			PersistentManager�ṩ�˰�Session���󱣴浽Session store�еĹ�������TomcatĿǰ�ṩ������Store��ʵ�ַ�ʽ��ʽ���ֱ�Ϊorg.apache.Catalina.FileStore����Session�����Ա����ļ��ķ�ʽ�洢��ָ��Ŀ¼�У�org.apache.Catalina.JDBCStore����Session����洢��ָ�������ݿ��У��������ļ�����ʽ������ʾ�����ұ��Ķ���PersistentManager �� Session store �����ã�ԭ��û�������������ֻ����Session�ĳ־û�����˵����
```
	6. HttpSessionActivationListenerͬ�������ڼ��ʵ���౾����ʵ�������bean����ӵ�session�У�session.setAttribute()������session�����л����ۻ������ļ�ʱ��sessionWillPassivate�����������ã�ֻ�б��浽Session�еĶ���Żᱻ��������������HttpSessionEvent����Session�������Ϣ�������������bean�ᱻ�������л�����������ʵ����ͬʱ��Ҫʵ��Serializable�ӿڡ�
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
        System.out.println("MyHttpSessionActivationListener sessionWillPassivate(session ������ʱ��bean�����л�������) and sessionId = " + session.getId() + ", " + this.toString());
    }

    public void sessionDidActivate(HttpSessionEvent httpSessionEvent) {
        HttpSession session = httpSessionEvent.getSession();
        System.out.println("MyHttpSessionActivationListener sessionDidActivate(session �������bean�������л����ڴ�) and sessionId = " + session.getId() + ", " + this.toString());
    }

    @Override
    public String toString() {
        return "MyHttpSessionActivationListener{" +
                "name='" + name + '\'' +
                '}';
    }
}

JSP���룺
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
���ڴ���û��session��Ϣʱ��ͨ��ָ��sessionId���ʷ�����ʱ��������ԭ��ҳ��������ˢ��ʱ��������sessionId.session�ļ��������л����������������л���Session��Ϣ��ͬʱ��sessionDidActivate���������ã�����HttpSessionEvent�к��巴���л���Session������bean��Ϣ
```
![](https://s1.51cto.com/images/blog/201801/10/be1c918fb8fbc179849dda175b32704e.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

	7. �����ͻ�������Servlet Request����ļ�������ServletContext������ͬ�����ƣ��ֱ��ӦServletRequestListener �� ServletRequestAttributeListener�����ǵĽӿڷ�����ServletContext�ӿڷ���Ҳ�����ƣ��˴�����׸�������⣬����ServletRequest�Ĳ�����ͨ��ServletRequestEvent��ServletRequestAttributeEvent�������л�ȡ���������롣
```
 ServletRequestListener ������
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

JSP���룺
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
ServletRequestAttributeListener������
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

JSP���룺
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

## �ܽ�
	��һ���Բ��͵���ʽ��¼���Լ�ѧϰweb listener�ľ�������֮ǰ��ʹ���Ʊʼ���ȣ������Լ���Ҫ�����Ĳ������ǶԸ��ּ�����ѧϰ�����գ������Լ��ı����������Ҫ����ѵ������������Ȼֻ�ǰ�listener�ĸ���demoʵ���Լ��Լ�������¼���������ǽ������޲����ĸ����˺ܶ�Ρ��������п�ͷ��ÿһ��ĵ�ν��������ܳɾͷǷ���׿Խ��