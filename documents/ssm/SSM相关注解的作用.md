### 一.注解含义及应用场景

#### 1.组件扫描

1.指定一个包路径，Spring会自动扫描该包及其子包所有组件类，当发现组件类定义前**有特定的注解标记**时，就将该组件纳入到Spring容器。等价于原有XML配置中的<bean>定义功能，组件扫描可以替代大量XML配置的<bean>定义。

​			

#### 2.使用组件扫描，首先需要在XML配置中指定扫描类路径

```java
<context:component-scan base-package = "org.example"/>
```

上面配置，容器实例化时会自动扫描org.example包及其子包下所有组件类，指定扫描类路径后，并不是该路径下所有组件类都扫描到Spring容器的，只有在组件类定义前面有以下注解标记时，才会扫描到Spring容器。



#### 3.注解介绍

@Component:通用注解

@Name:通用注解

@Repository:持久化层组件注解 

@Service:业务层组件注解

@Controller:控制层组件注解



当一个组件在扫描过程中被检测到时，会生成一个默认id值，默认id为小写开头的类名。也可以在注解标记中自定义id。下面两个组件id名字分别是oracleUserDao和loginService

```java
@Repository
public class OracleUserDao implements UserDao{

　　//.....
}

@Service("loginService")
public class UserService{

　　//....
}
```



通常受Spring管理的组件，默认的作用域是“singleton”。如果需要其他作用域可以使用@Scope注解，只要在注解中提供作用域的名称即可。

```java
@Scope("prototype")
@Repository
public class OracleUserDao implements EmpDao{
　　//...
}
```





#### 4.具有依赖关系的Bean对象，利用下面任何一种注解都可以实现关系注入

@Resource

@Autowired/@Qualifier



@Resource注解标记可以用在字段定义或setter方法定义前面，默认首先按名称匹配注入，然后类型匹配注入：

```java
public class UserService{
　　//@Resource
　　private UserDao userDao;
　　@Resource
　　public void setUserDao(UserDao dao){
　　　　this.userDao = dao;
　　}
}
```

当遇到多个匹配Bean时注入会发生错误，可以显示指定名称，例如@Resource(name = "empDao1")





@Autowired注解标记也可以用在字段定义或setter方法定义前面，默认按类型匹配注入：

```java
public class UserService{
　　//@Autowired
　　private UserDao userDao;
　　@Autowired
　　public void setUserDao(UserDao dao){
　　　　this.userDao = dao;
　　}
}
```

@Autowired当遇到多个匹配Bean时注入会发生错误，可以使用下面方法指定名称：

```java
public class UserService{
　　//@Autowired
　　//@Qualifiter("mysqlUserDao")
　　private UserDao userDao;
　　@Autowired
　　public void setUserDao(@Qualifiter("mysqlUserDao")  userDao dao){
　　　　this.userDao = dao;
　　}
}
```







@Value注解可以注入Spring表达式值，使用方法：首先在XML配置中指定要注入的properties文件

<util:properties id = "jdbcProps"  location = "classpath:db.properties"/>然后在setter方法前使用@Value注解

```java
public class JDBCDataSource{
　　@Value("#{jdbcProps.url}")
　　private String url;
　　@Value("#{jdbcProps.driver}")
　　public void setUrl(String driver){
　　　　try{Class.forName(driver)}catch(.....)...
　　}
}
```





#### 5.RequestMapping注解应用

@RequestMapping可以用在类定义和方法定义上

@RequestMapping标明这个类或者方法与哪一个客户请求对应

@RequestMapping可以定义在Controller类前和处理方法前，主要用于指定Controller的方法处理哪些请求

```java
@Controller
@RequestMapping("/demo01")
public class Controller{
　　@RequestMapping("/hello")
　　public String execute()throws Exception{
　　　　return "hello";
　　}
}
```

开启@RequestMapping注解映射，需要在Spring的XML配置文件中定义RequestMappingHandlerMapping(类定义前)和RequestMappingHandlerAdapter(方法定义前)两个bean组件.示例：

```java
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>

<bean
class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
```

从Spring3.2版本开始可以使用下面XML配置简化RequestMappingHandlerMapping和RequestMappingHandlerAdapter定义：

```java
<mvc:annotation-driven/>
```





#### 6.Controller注解应用

推荐使用@Controller注解声明Controller组件，这样可以使得Controller定义更加灵活，可以不用实现Controller接口，请求处理的方法也可以灵活定义

```java
@Controller
@RequestMapping("/demo01")
public classs Controller{
　@RequestMapping("/hello")
　　public String execute()throws Exception{
　　　　return "hello";
　　}
}
```

为了使@Controller注解生效，需要在Spring的XML配置文件中开启组件扫描定义，并指定Controller组件所在包

```java
<context:component-scan  base-package = "com.controller"/>
```



#### 7.@ResponseBody注解

@ResponseBody注解(在Spring MVC中，此注解与JSON响应相关)

在控制器方法上使用 @ResponseBody 注解, 就可以自动的将控制器的返回值(JavaBean)转换为JSON发送到客户端.

@ResponseBody注解主要用于Controller组件的处理方法前，使用方法为：

引入jackson开发包，后面示例用的是jackson-annotations-2.4.1.jar,jackson-core-2.4.1.jar,jackson-databind-2.4.1.jar

在Spring配置文件中定义<mvc:annotation-driven/>,开启对@ResponseBody应用的支持

在Controller处理方法前定义@ResponseBody注解

```java
@RequestMapping("/test1")
@ResponseBody
public boolean f1(){
　　//业务处理代码
　　return true;
}
```

