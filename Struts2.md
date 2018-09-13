第1章 Struts2
1.1上次课内容的回顾

Struts2的Servlet的API的访问

​	 解耦合的方式：通过ActionContext对象

​	 实现特定接口的方式：实现ServletRequestAware，ServletContextAware，ServletResponseAware

​	 通过ServletActionContext获取

 

Struts2的结果页面的配置<result>

​	 结果页面的分类：

​		 全局结果页面：针对一个包下的所有的action都生效

​		 局部结果页面：针对一个action生效

​          局部覆盖全局。

​	 结果页面的类型

​		 通过type属性进行配置:

​			 dispatcher（默认值）

​			 chain

​			 redirect

​			 redirectAction

​			 stream

 

Struts2的数据封装

​	 属性驱动（拦截器）

​		 提供属性的set的方法

​		 提供页面OGNL表达式的方式，在Action中提供对象

​	 模型驱动（拦截器）

​         实现一个ModelDriver的接口。

​		 采用模型驱动实现

1.2 案例：CRM的客户查询操作

1.2.1 案例需求

CRM的管理系统中的客户管理：

l 查询客户		：对查询进行优化.

EL表达式c:forEach

Struts2的标签：s:iterator，从struts2的值栈中获取数据

输出Struts2值栈中的值：s:property/  ${requestScope.name}

l 添加客户

l 修改客户

l 删除客户

1.3 相关知识点

1.3.1 OGNL的表达式

1.3.1.1 什么是OGNL

 

OGNL:对象图导航语言，是一门功能强大的表达式语言（功能比EL强大很多倍）。Struts2将OGNL引入到自身，作为Struts2的表达式语言，struts2的值栈就是对OGNL的扩展。

1.3.1.2 OGNL的作用

 

1.3.1.3 OGNL的要素：

l 表达式：

 

l 根对象（Root）：

 

l Context上下文对象：

 

注意：要想学会struts2的OGNL表达式取数据，先要学会struts2的值栈中存储数据的原理（即OGNL存储数据机构，Root对象、Content上下文对象存储数据）

1.3.2 OGNL的入门

1.3.2.1 在Java环境中使用

创建包：com.itheima.ognl.demo1

创建类：OgnlDemo1.java

l 调用对象的方法

/

  OGNL的入门操作

 /

public class OgnlDemo1 {

 

​	@Test

​	/

​	  OGNL调用对象的方法:

​	 /

​	public void demo1() throws OgnlException{

​		OgnlContext context = new OgnlContext();

​		Object value = Ognl.getValue("'helloworld'.length()", context, context.getRoot());

​		System.out.println(value);

​	}

}

输出：

 

 

l 调用对象的静态方法

​	@Test

​	/

​	  OGNL调用对象的静态方法:

​	  @包名.类名@属性/方法

​	 /

​	public void demo2() throws OgnlException{

​		OgnlContext context = new OgnlContext();

​		Object value = Ognl.getValue("@java.lang.Math@random()", context, context.getRoot());

​		System.out.println(value);

​	}

测试：

 

 

l 获得Root对象中的数据：获取root对象中的数据不需要加#

@Test

​	/

​	  获得Root中的数据

​	 /

​	public void demo3() throws OgnlException{

​		OgnlContext context = new OgnlContext();

​		User user = new User();

​		user.setUsername("aaa");

​		user.setPassword("123");

​		// 向root中存入数据

​		context.setRoot(user);

​		

​		// 获取root中的数据：

​		Object username = Ognl.getValue("username", context, context.getRoot());

​		Object password = Ognl.getValue("password", context, context.getRoot());

​		System.out.println(username +"   "+password);

​	}

测试：

 

/

​	  l向root中存数据，并获取数据

​	 /

​	@Test

​	public void demo4() throws Exception{

​		// 获取上下文的对象

​		OgnlContext context = new OgnlContext();

​		

​		User user = new User();

​		user.setUsername("张三");

​		user.setPassword("123");

​		

​		Map<String, Object> map = new HashMap<>();

​		map.put("name", "李四");

​		map.put("age", "22");

​		map.put("user", user);

​		context.setRoot(map);

​		

​		// 获取root对象

​		Object root = context.getRoot();

 

​		Object name = Ognl.getValue("name", context, root);

​		Object age = Ognl.getValue("age", context, root);

​		System.out.println(name+"    "+age);

​		Object username = Ognl.getValue("user.username", context, root);

​		Object password = Ognl.getValue("user.password", context, root);

​		System.out.println(username+"    "+password);

}

显示结果

 

 

l 获取context对象中的数据:获取context对象中的数据需要加#

​	@Test

​	/

​	  获取context中的数据

​	 /

​	public void demo5() throws OgnlException{

​		OgnlContext context = new OgnlContext();

​		context.put("name", "张三");

​		Object value = Ognl.getValue("#name", context, context.getRoot());

​		System.out.println(value);

​	}

测试：

 

 

 

或者：存放对象

OgnlContext context = new OgnlContext();

​		User u = new User();

​		u.setUsername("小宝");

​		u.setPassword("123");

​		// 将User对象存放到root

​		context.put("user", u);

​		Object username = Ognl.getValue("#user.username", context, context.getRoot());

​		Object password = Ognl.getValue("#user.password", context, context.getRoot());

​		System.out.println(username+"        "+password);

测试结果：

 

 

1.3.2.2 在Struts2环境中使用

页面：

使用struts2的标签，操作OGNL表达式。

创建页面：

demo1/demo1.jsp

引入struts2的标签：

<%@ taglib uri="/struts-tags" prefix="s"%>

<s:property value=”OGNL表达式”/>

 

<body>

​	<h1>OGNL在Struts2环境中使用</h1>

​	<h3>OGNL访问对象的方法</h3>

​	<s:property value="'helloworld'.length()" />

​	<h3>OGNL访问对象的静态方法</h3>

​	<s:property value="@java.lang.Math@random()" />

</body>

需要在struts.xml配置：

​	<!-- OGNL访问静态方法 -->

​	<constant name="struts.ognl.allowStaticMethodAccess" value="true"/>

测试结果：

 

1.3.3 Struts2的ValueStack

1.3.3.1 什么是ValueStack值栈

 

 

ValueStack是Struts2框架中的接口，实现类OgnlValueStack的类。ValueStack（值栈）实质就是一个Struts2的数据的中转站。ValueStack对象贯穿了Action的整个的生命周期。从客户端发送一个请求到Action，Struts2就会创建一个Action的实例，同时为这个Action的实例创建一个ValueStack对象。在执行Action过程中，Struts2框架将ValueStack存入到request域中一份。Struts2通过OGNL访问值栈的数据。

 

1.3.3.2 值栈的内部结构（重点）

 

查看值栈：

配置Action

/

  查看值栈的内部结构:

 /

public class ValueStack1Action extends ActionSupport{

 

​	@SuppressWarnings("unused")

​	@Override

​	public String execute() throws Exception {

​		ValueStack valueStack = ActionContext.getContext().getValueStack();

​		return SUCCESS;

​	}

}

配置struts.xml

<package name="demo1" extends="struts-default" namespace="/">

​		<action name="valueStack1" class="com.itheima.valuestack.demo1.ValueStack1Action">

​			<result>/valuestack/demo1.jsp</result>

​		</action>

</package>

创建valuestack/demo1.jsp

<body>

<h1>查看值栈的内部结构</h1>

s:debug/s:debug

</body>

 

我们查看值栈的情况。方案一：通过断点查看

 

root栈（ArrayList集合）：CompoundRoot.java

 

Context栈（Map集合）：OgnlContext.java

 

第二种查看值栈的方式，通过s:debug/

​	<h3>访问值栈，值栈中的存储特点</h3>

​	s:debug/s:debug

 

原理：

 

 

查看s:debug/标签

 

 

1.3.3.3 ActionContext与值栈关系

l 为什么可以通过ActionContext获得值栈？

ValueStack valueStack = ActionContext.getContext().getValueStack(); 

n ActionContext：表示Action的上下文。创建Action实例的时候，同时创建值栈对象，将值栈对象存入到ActionContext中。

查看：

（1）StrutsPrepareAndExecuteFilter.java

 

（2）查看createActionContext的方法

 

 

   

l ActionContext为什么访问Servlet的API？

 

【回顾】上次课用ActionContext访问Servlet的API:

/

​		  在ActionContext中提供了一些方法:操作域对象的数据

​		  	 Map<String,Object> getSession();

​		    Map<String,Object> getApplication();

​		 /

​		// 接收参数:

​		ActionContext actionContext = ActionContext.getContext();

​		// 向request域中存值：

​		actionContext.put("reqName", "req如花");

​		// 向session域中存值:

​		actionContext.getSession().put("sessName", "sess凤姐");

​		// 向application域中存值：

​		actionContext.getApplication().put("appName","app石榴");

 这种解耦合的方式访问Servlet的API的方式，只能操作Request、Session、Application域中数据的，但是不能获得真实的对象，即不是HttpServletRequest，HttpSession,ServletContext对象。

总结：ActionContext中能够访问Servlet的API，是基于ValueStack来实现的。

 

ActionContext.application：解耦合的方式存储

 

 

application：表示真正的ServletContext对象，即耦合的方式

 

 

Session：

 

 

而ServletActionContext继承ActionContext

 

在子类中可以获取真实的Servlet api的对象，因为ServletActionContext对ActionContext拓展。

 

1.3.3.4 获得ValueStack

l 通过ActionContext对象获取：

// 获取值栈的方式一：通过ActionContext获取:

​		ValueStack valueStack1 = ActionContext.getContext().getValueStack();

l 通过request域获取：key是struts.valueStack的值

（1）查看：StrutsPrepareAndExecuteFilter.java

 

（2）Dispatcher.java

 

 

在一个请求中，在执行Action的过程中，将ValueStack存入到request域中，key是struts.valueStack。

相当于request.setAttribute(“struts.valueStack”,valueStack);

// 获取值栈的方式二：通过request对象获取:

​		ValueStack valueStack2 = (ValueStack) ServletActionContext.getRequest()

​				.getAttribute(ServletActionContext.STRUTS_VALUESTACK_KEY);

 

 

第一步：创建ValueStack2Action.java

/

  获取值栈的2种方式:

 /

public class ValueStack2Action extends ActionSupport{

​	

​	@Override

​	public String execute() throws Exception {

​		// 获取值栈的方式一：通过ActionContext获取:

​		ValueStack valueStack1 = ActionContext.getContext().getValueStack();

​		

​		// 获取值栈的方式二：通过request对象获取:

​		ValueStack valueStack2 = (ValueStack) ServletActionContext.getRequest()

​				.getAttribute(ServletActionContext.STRUTS_VALUESTACK_KEY);

​		// true：只要是同一个Action，获取的值栈信息都是同一个对象

​		System.out.println(valueStack1 == valueStack2);

​		return NONE;

​	}

}

第二步：配置struts.xml

<action name="valueStack2" class="com.itheima.valuestack.demo1.ValueStack2Action"></action>

第三步：测试：

访问：http://localhost:8080/struts2_day03/valueStack2.action

查看结果

 

说明2次获取的值栈是一样的。

总结：获取值栈的时候，有2种方案，我建议大家：记住：

// 获取值栈

​		ValueStack valueStack = ActionContext.getContext().getValueStack();

​		// 验证一下每次请求都会创建一个新的值栈

​		System.out.println(valueStack);

1.3.3.5 操作ValueStack（重点）--操作root区域

l 操作root区域

n 通过值栈中的方法操作值栈

调用ValueStack中的使用方法：set、push

第一步：创建类ValueStack3Action.java

/

  操作值栈

 /

public class ValueStack3Action extends ActionSupport{

 

 

​	@Override

​	public String execute() throws Exception {

​		// 获得值栈:

​		ValueStack valueStack = ActionContext.getContext().getValueStack();

​		/

​		  ValueStack中的方法:

​		  	 void set(String key,Object value);  -- 创建一个Map集合，将Map集合存入到值栈（root）中。

​		  	 void push(Object obj);			  -- 将对象存入到值栈（root）中。

​		   栈的特点：先进后出.

​		 /		

​		valueStack.set("name", "王宝强");// 普通键值

​		

​		User user = new User();

​		user.setUsername("大鹏");

​        user.setPassword("123");

​		valueStack.push(user); // 一般Object使用push，压入栈顶对象（list集合的第1个位置）

 

valueStack.set("user", user);// 一般list集合使用set

​		

​		return SUCCESS;

​	}

}

n 通过Action中提供成员属性的方式

Action类默认是在栈顶，而且默认情况下（没有手动向值栈存值，没有实现模型驱动）是在栈顶的位置。Action已经在值栈中了，现在提供了一个Action的属性，Action的属性也是会在栈中。

/

  操作值栈

 /

public class ValueStack3Action extends ActionSupport{

 

​	private Integer age;

​	

​	public Integer getAge() {

​		return age;

}

​	public void setAge(Integer age) {

​		this.age = age;

}

 

}

第二步：创建包com.itheima.ognl.domain：创建User.java

public class User {

​	

​	public User() {

​		super();

​	}

​	public User(String username, String password) {

​		super();

​		this.username = username;

​		this.password = password;

​	}

 

​	private String username;

​	private String password;

​	public String getUsername() {

​		return username;

​	}

​	public void setUsername(String username) {

​		this.username = username;

​	}

​	public String getPassword() {

​		return password;

​	}

​	public void setPassword(String password) {

​		this.password = password;

​	}

​	

}

第三步：配置struts.xml文件

​		<action name="valueStack3" class="com.itheima.valuestack.demo1.ValueStack3Action">

​			<result>/valuestack/demo3.jsp</result>

​		</action>

第四步：创建valuestack/demo3.jsp

<body>

<h1>操作值栈</h1>

s:debug/s:debug

<h1>获取root栈中map集合的属性</h1>

<s:property value="name"/>

<h1>获取栈顶对象的值</h1>

<s:property value="username"/>----<s:property value="password"/>

 

<h1>获取Action类中的属性</h1>

<s:property value="age"/>

<h1>获取root栈中map集合的属性</h1>

<s:property value="user.username"/>----<s:property value="user.password"/>

 

</body>

第五步：测试：

 

查看root栈：

 

也可以查看断点：

 

结论：

思考：如果Root栈存放了相同的属性，读取的时候，是读取哪个属性的值呢？

ArrayList集合有一个栈顶的概念

A：栈顶（list集合0的位置）

B：最后

读取数据的时候，从上往下读，如果读到了，就直接获取；如果没有读到就继续往下读。

1.3.3.6 操作ValueStack（重点）--操作context区域

l 操作context区

第一步：创建ValueStack4Action.java

/

  操作context的数据

 /

public class ValueStack4Action extends ActionSupport{

 

​	@Override

​	public String execute() throws Exception {

​		

​		ServletActionContext.getRequest().setAttribute("name", "reqValue");

​		

​		ServletActionContext.getRequest().getSession().setAttribute("name", "sessValue");

​		

​		ServletActionContext.getServletContext().setAttribute("name", "appValue");

​		

​		

​		ActionContext.getContext().put("name", "柳岩"); // 存放到上下文

ValueStack valueStack = ServletActionContext.getContext().getValueStack();

System.out.println(valueStack);

 

​		return SUCCESS;

​	}

}

第二步：配置struts.xml

​		<action name="valueStack4" class="com.itheima.valuestack.demo1.ValueStack4Action">

​			<result>/valuestack/demo4.jsp</result>

​		</action>

第三步：创建demo4.jsp

<body>

<h1>操作值栈的context区</h1>

s:debug/s:debug

<s:property value="#request.name"/> <br/>

<s:property value="#session.name"/> <br/>

<s:property value="#application.name"/> <br/>

<s:property value="#parameters.name"/> <br/>

<s:property value="#attr.name"/> <br/>

 

​	<h3>取出context中存放的值</h3>

​	<s:property value="#name"/>

 

</body>

第四步：测试

 

提示：#attr 按照 page --- request --- session --- application的顺序依次进行搜索 ，大家了解这个属性即可，一般不用

查看值栈：

 

1.3.3.7 获取ValueStack（root）中的数据，操作集合

第一步：创建ValueStack5Action

/

  获取值栈数据

 /

public class ValueStack5Action extends ActionSupport{

 

​	@Override

​	public String execute() throws Exception {

​		// 向值栈中保存数据：

​		User user=new User();

​		user.setUsername("张三");

​		user.setPassword("123");

​		// 获取值栈:

​		ValueStack valueStack = ActionContext.getContext().getValueStack();

​		valueStack.push(user);// 将对象存放到栈顶

​		

​		valueStack.set("user", user);//将对象使用map集合的形式，存放到root中

​		

​		// 向值栈中存入集合数据

​		List<User> list = new ArrayList<User>();

​		list.add(new User("李四","123"));

​		list.add(new User("王五","123"));

​		list.add(new User("赵六","123"));

​		list.add(new User("田七","123"));

​		

​		valueStack.set("list", list);

​		

​		return super.execute();

​	}

}

第二步：配置struts.xml

<action name="valueStack5" class="com.itheima.valuestack.demo1.ValueStack5Action">

​	<result>/valuestack/demo5.jsp</result>

</action>

第三步：/valuestack/demo5.jsp

<body>

​	<h1>获取数据</h1>

​	<h3>获取push的数据</h3>

​	<s:property value="username" />

​	<s:property value="password" />

​	<h3>获取set的数据</h3>

​	<s:property value="user.username" />

​	<s:property value="user.password" />

​	<h3>获取list集合中的数据</h3>

​	<s:property value="list[0].username" /><br />

​	<s:property value="list[0].password" /><br />

​	<s:property value="list[1].username" /><br />

​	<s:property value="list[1].password" /><br />

​	<s:property value="list[2].username" /><br />

​	<s:property value="list[2].password" /><br />

​	<br />

​	<s:iterator value="list" var="user">

​		<s:property value="username" />-<s:property value="password" />

​		<br />

​		<s:property value="#user.username" />-<s:property value="#user.password" />

​		<br />

​	/s:iterator

​	s:debug/s:debug

</body>

 

1.3.3.8 EL访问ValueStack

第一步：创建ValueStack6Action

/

  EL获取值栈数据

 /

public class ValueStack6Action extends ActionSupport{

 

​	@Override

​	public String execute() throws Exception {

​		

​		ActionContext.getContext().getValueStack().set("name", "王宝强");

​		

​		return super.execute();

​	}

}

 

第二步：配置struts.xml

<action name="valueStack6" class="com.itheima.valuestack.demo1.ValueStack6Action">

​	<result>/valuestack/demo6.jsp</result>

</action>

第三步：/valuestack/demo6.jsp

<body>

<h1>使用OGNL获取</h1>

<s:property value="name"/>

 

<h1>使用EL获取</h1>

${ name }

</body>

 

备注：EL获取的数据来源都是四个作用域还有提供11操作web常用的对象。

11个web常用对象:

requestScope

sessionScope

pageScope

applicationScope

param

paramValues

header

headerValue

cookie

initparams

pageContext

因为Struts2底层对request对象进行包装：

（1）查看StrutsPrepareAndExecuteFilter.java

 

（2）查看：StrutsRequestWrapper.java

public class StrutsRequestWrapper extends HttpServletRequestWrapper {

 

​    private static final String REQUEST_WRAPPER_GET_ATTRIBUTE = "__requestWrapper.getAttribute";

​    private final boolean disableRequestAttributeValueStackLookup;

 

​    /

​      The constructor

​      @param req The request

​     /

​    public StrutsRequestWrapper(HttpServletRequest req) {

​        this(req, false);

​    }

 

​    /

​      The constructor

​      @param req The request

​      @param disableRequestAttributeValueStackLookup flag for disabling request attribute value stack lookup (JSTL accessibility)

​     /

​    public StrutsRequestWrapper(HttpServletRequest req, boolean disableRequestAttributeValueStackLookup) {

​        super(req);

​        this.disableRequestAttributeValueStackLookup = disableRequestAttributeValueStackLookup;

​    }

 

​    /

​      Gets the object, looking in the value stack if not found

​     

​      @param key The attribute key

​     /

​    public Object getAttribute(String key) {

​        if (key == null) {

​            throw new NullPointerException("You must specify a key value");

​        }

 

​        if (disableRequestAttributeValueStackLookup || key.startsWith("javax.servlet")) {

​            // don't bother with the standard javax.servlet attributes, we can short-circuit this

​            // see WW-953 and the forums post linked in that issue for more info

​            return super.getAttribute(key);

​        }

 

​        ActionContext ctx = ActionContext.getContext();

​        Object attribute = super.getAttribute(key); // 从Request中获取

 

​        if (ctx != null && attribute == null) {

​            boolean alreadyIn = isTrue((Boolean) ctx.get(REQUEST_WRAPPER_GET_ATTRIBUTE));

 

​            // note: we don't let # come through or else a request for

​            // #attr.foo or #request.foo could cause an endless loop

​            if (!alreadyIn && !key.contains("#")) {

​                try {

​                    // If not found, then try the ValueStack

​                    ctx.put(REQUEST_WRAPPER_GET_ATTRIBUTE, Boolean.TRUE);

​                    ValueStack stack = ctx.getValueStack();

​                    if (stack != null) {

​                        attribute = stack.findValue(key); // 如果request中没有获取到信息，从值栈中获取

​                    }

​                } finally {

​                    ctx.put(REQUEST_WRAPPER_GET_ATTRIBUTE, Boolean.FALSE);

​                }

​            }

​        }

​        return attribute;

​    }

}

表示Struts2重写了getAttribute()的方法。

${name}，先从request域中查找，找到了获取值；如果request域中没有查找到，再从值栈中查找。

测试：

第一步：修改ValueStack6Action.java

@Override

​	public String execute() throws Exception {

​		

​		ActionContext.getContext().getValueStack().set("name", "王宝强");

​		ServletActionContext.getRequest().setAttribute("name", "柳岩");

​		return super.execute();

​	}

第二步：demo6.jsp页面上使用

<body>

<h1>使用OGNL获取</h1>

<s:property value="name"/>  <br/>

<s:property value="#request.name"/>  <br/>

<h1>使用EL获取</h1>

<!--先从request中获取，取不到再从值栈中获取-->

${ name }  <br/>

${ requestScope.name }  <br/>

</body>

3次调用getAttribute的方法。

输出结果：

 

1.3.4 OGNL中特殊符号的使用:

1.3.4.1 #号

l 用法一：获取context中的数据

<s:property value="#request.name"/> <br/>

<s:property value="#session.name"/> <br/>

<s:property value="#application.name"/> <br/>

<s:property value="#parameters.name"/> <br/>

<s:property value="#attr.name"/> <br/>

 

l 在OGNL中手动构建一个Map集合

<h1>#号构建一个Map集合</h1>

<h3>遍历List集合</h3>

<h3>遍历Map集合</h3>

<h3>使用Struts2UI标签构建Map集合的地方</h3>

 

创建ognl/demo1.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"

​    pageEncoding="UTF-8"%>

<%@ taglib uri="/struts-tags" prefix="s"%>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

<html>

<head>

<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<title>Insert title here</title>

</head>

<body>

<h1>#号构建一个Map集合</h1>

<h3>遍历List集合</h3>

<s:iterator var="iii" value="{'aa','bb','cc'}">

​	<s:property value="#iii"/><br/>

/s:iterator

<h3>遍历Map集合</h3>

<s:iterator var="entry" value="#{'aa':'11','bb':'22','cc':'33'}">

​	<s:property value="key"/>--<s:property value="value"/><br/>

​	<s:property value="#entry.key"/>--<s:property value="#entry.value"/><br/>

/s:iterator

<h3>使用Struts2UI标签构建Map集合的地方</h3>

传统方式:<br/>

性别:<input type="radio" name="sex" value="男"/>男<input type="radio" name="sex" value="女"/>女<br/>

性别:<input type="radio" name="sex" value="1"/>男<input type="radio" name="sex" value="2"/>女<br/>

Struts2UI标签的方式:<br/>

<s:radio list="{'男','女'}" name="sex" label="性别"/> <br />

<s:radio list="#{'1':'男','2':'女' }" name="sex" label="性别"/> <br />

</body>

</html>

查看测试结果：

 

作业：

<s:radio list="">/s:radio

​	<s:select list="">/s:select

​	<s:checkboxlist list="">/s:checkboxlist

1.3.4.2 %号（应用场景：jsp页面）

l 强制解析OGNL表达式：

<s:property value=”ognl”/> 一定会执行的。

创建ognl/demo2.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"

​    pageEncoding="UTF-8"%>

<%@ taglib uri="/struts-tags" prefix="s"%>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

<html>

<head>

<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<title>Insert title here</title>

</head>

<body>

<h1>%号作用：强制解析OGNL表达式</h1>

<%

​	request.setAttribute("name", "王宝强");

%>

<input type="text" name="name" value="<s:property value="#request.name"/>"/> <br/>

<s:textfield name="name" value="%{#request.name}"/>

</body>

</html>

 

1.3.4.3 $号（应用场景：struts.xml或者.properties文件）（了解）

l 强制解析OGNL表达式：

l 在Struts2的配置文件中使用OGNL表达式

l 用法一：struts2的配置文件(struts.xml)

n 文件下载

<action name=”download” class=”cn.itcast.struts2.DownloadAction”>

​	<result name=”success” type=”stream”>

​		<param name=”Content-Dispostion”>attachment;filename=${#request.fileName}</param>

</result>

</action>

n 国际化（struts2支持国际化）

l 用法二：struts2的属性文件中使用.properties文件

message_zh_CN.properties

welcome=您好:${#session.user.name}

 

message_en_US.properties

welcome=Hello:${#session.user.name}

 

 

1.4 案例查询代码优化

1.4.1 修改CustomerAction的查询方法

/

​	  查询所有客户的方法:findAll

​	 /

​	public String findAll(){

​		List<Customer> list = customerService.findAll();

​		// 存入request域中:

​		// ServletActionContext.getRequest().setAttribute("list", list);

​		ActionContext.getContext().getValueStack().set("list", list);

​		return "findAll";

​	}

 

1.4.2 修改jsp/customer/list.jsp

添加struts2的标签库

<%@taglib uri="/struts-tags" prefix="s"%>

s:iterator/遍历

<%-- <c:forEach items="${list }" var="customer"> --%>

​	<s:iterator value="list" var="customer">

​		<TR

​			style="FONT-WEIGHT: normal; FONT-STYLE: normal; BACKGROUND-COLOR: white; TEXT-DECORATION: none">

​			<TD><s:property value="cust_name"/></TD>

​			<TD><s:property value="cust_level"/></TD>

​			<TD><s:property value="cust_source"/></TD>

​			<TD><s:property value="cust_industry"/></TD>

​			<TD><s:property value="cust_phone"/></TD>

​			<TD><s:property value="cust_mobile"/></TD>

​			<TD>

​				">修改

​				  

​				">删除

​			</TD>

​		</TR>

​	/s:iterator

​	<%-- /c:forEach --%>

 

 
