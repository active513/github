### 一.springMVC基本运行流程图

![1539585513168](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1539585513168.png)

### 二.springMVC基本功能配置

##### 1.新建工程

##### 2.导入jar包

![1539585805149](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1539585805149.png)

##### 3.配置DispatcherServlet

###### (1).需要通过使用`web.xml`文件中的URL映射来映射希望`DispatcherServlet`处理的请求。

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns="http://xmlns.jcp.org/xml/ns/javaee" 
xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
  <display-name>SpringMVC</display-name>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  
  <servlet>
  	<servlet-name>dispatcherServlet</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<!-- 初始化时启动 -->
  	<load-on-startup>1</load-on-startup>
  </servlet>
  
  <servlet-mapping>
  	<servlet-name>dispatcherServlet</servlet-name>
  	<url-pattern>/</url-pattern>
  </servlet-mapping>
 </web-app>
```

###### (2).配置springmvc配置文件的名字及位置

框架将尝试从位于应用程序的`WebContent/WEB-INF`目录中的名为`[servlet-name]-servlet.xml`的文件加载应用程序上下文，在这个示例中，使用的文件将是dispatcherServlet-servlet.xml,若需要自定义名称和位置则需要在web.xml进行如下配置

```java
<servlet>
  	<servlet-name>dispatcherServlet</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>classpath:spring-mvc.xml</param-value>
  	</init-param>
  	<load-on-startup>1</load-on-startup>
</servlet>
  
  <servlet-mapping>
  	<servlet-name>dispatcherServlet</servlet-name>
  	<url-pattern>/</url-pattern>
  </servlet-mapping>
```



##### 4.springmvc配置文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:context="http://www.springframework.org/schema/context"
   xmlns:mvc="http://www.springframework.org/schema/mvc"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="
   http://www.springframework.org/schema/beans     
   http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
   http://www.springframework.org/schema/context 
   http://www.springframework.org/schema/context/spring-context-4.0.xsd
   http://www.springframework.org/schema/mvc 
   http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
   
  <context:component-scan base-package="com.controller"/>
  
  <mvc:annotation-driven/>
  
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  	<property name="prefix" value="/"></property>
  	<property name="suffix" value=".jsp"></property>
  </bean>
</beans>
```

###### (1).mvc:annotation-driven说明

<mvc:annotation-driven /> 会自动注册DefaultAnnotationHandlerMapping与AnnotationMethodHandlerAdapter 两个bean,是spring MVC为@Controllers分发请求所必须的,即解决了@Controller注解使用的前提配置。

需要注意的是，在spring mvc 3.1以上，DefaultAnnotationHandlerMapping与AnnotationMethodHandlerAdapter对应变更为： 
  DefaultAnnotationHandlerMapping -> RequestMappingHandlerMapping 
  AnnotationMethodHandlerAdapter -> RequestMappingHandlerAdapter AnnotationMethodHandlerExceptionResolver -> ExceptionHandlerExceptionResolver

头文件里说的http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd，并不是真的到网上去下载这个文件，在spring.schemas文件中，定义了它指向org/springframework/web/servlet/config/spring-mvc-3.1.xsd这个文件(在jar包里)

###### (2).context:component-scan说明

<context:component-scan/>标签是告诉Spring 来扫描指定包下的类，并注册被@Component，@Controller，@Service，@Repository等注解标记的组件。

而<mvc:annotation-scan/>是告知Spring，我们启用注解驱动。然后Spring会自动为我们注册上面说到的几个Bean到工厂中，来处理我们的请求。<span style="color:red">就一般功能而言，context:component-scan可以代替mvc:annotation-driven，所以有的配置文件写了context:component-scan就没有写mvc:annotation-driven</span>



##### 5.controller层相关代码

```java
@Controller
@RequestMapping(value="/UserController")
public class UserController {
	
	@RequestMapping(value="/login")
	public String login(Model model) {
		model.addAttribute("msg","Hello Spring MVC");
		return "index";
	}
}
```

DispatcherServlet若识别出了相对应的地址，则走相应的方法，这里面是/UserController/login,地址http://localhost:8080/springMVC/Usercontroller/login则调用相应的方法



##### 6.创建相应的jsp视图

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
	<h2>Hello,${msg}</h2>
</body>
</html
```

