### 一.springboot的基本配置

##### 1.配置pom.xml

```java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.demo.lj</groupId>
  <artifactId>springBoot</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>war</packaging>
  
  <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
    
    <dependencies>
    	<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

##### 2.创建Application.java

创建 Application.java，其注解 @SpringBootApplication 表示这是一个SpringBoot应用，运行其主方法就会启动tomcat,默认端口是8080

<span style="color:#f00"> Application.java所在的包必须是最外层，他必须包含所有程序，例如HelloCpntroller所在的包名为springboot.controller,则Application.java所在的包名必须为springboot,不然会报错</span>

![1540523221776](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540523221776.png)

```java
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		SpringApplication.run(Application.class, args);
	}

}
```

##### 3.创建controller

接着创建控制器类HelloController， 这个类就是Spring MVC里的一个普通的控制器。@RestController 是spring4里的新注解，是@ResponseBody和@Controller的缩写。

@ResponseBody:表示返回一个json数据

```java
package springboot.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
	
	@RequestMapping("/hello")
	public String hello() {
		return "Hello Spring Boot";
	}
}
```

##### 4.访问结果

​	接下来就运行[Application.java](http://how2j.cn/k/springboot/springboot-eclipse/1640.html#step7122)， 然后访问地址http://127.0.0.1:8080/hello

![1540534718706](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540534718706.png)

### 二.部署-jar方式

1.mvn install 将项目打包成jar

2.java -jar jar包名

3.访问地址http://127.0.0.1:8080/hello



### 三.部署-war方式

##### 1.Application.java

Application 修改为如下代码新加@ServletComponentScan注解，并且继承SpringBootServletInitializer 。
为什么要这么改？ 这是规定。。。。 要搞成war ，反正就得这么改~

```java
@SpringBootApplication
@ServletComponentScan
public class Application extends SpringBootServletInitializer {
	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		// TODO Auto-generated method stub
		return application.sources(Application.class);
	}

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		SpringApplication.run(Application.class, args);
	}

}
```

##### 2.pom.xml

spring-boot-starter-tomcat修改为 provided方式，以避免和独立 tomcat 容器的冲突. 表示provided 只在编译和测试的时候使用，打包的时候就没它了。

```java
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
```

##### 3.打成war包

mvn clean package



##### 4.将war放入tomcat中

讲war包放入tomcat下的webapps中，然后启动bin目录下的startup.bat



##### 5.访问

访问地址http://127.0.0.1:8080/springBoot/hello(<span style="color:#f00;">要加入项目名，不然无法访问相应界面</span>)



### 四.增加对JSP的支持

Springboot的默认视图支持是Thymeleaf，但是Thymeleaf 后面才讲，目前我们暂时不熟悉，我们熟悉的还是jsp。 所以本知识点讲解如何让Springboot支持 jsp。

##### 1.pom.xml

```java
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>
		
		<!-- servlet依赖 -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>

		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		
		<!-- tomcat的支持.-->
        <dependency>
               <groupId>org.apache.tomcat.embed</groupId>
               <artifactId>tomcat-embed-jasper</artifactId>
        </dependency>  
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
```

##### 2.application.properties

在src/main/resources 目录下增加 application.properties文件，用于视图重定向jsp文件的位置

```java
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```



##### 3.controller

修改HelloController，把本来的@RestController 改为@Controller。这时返回"hello"就不再是字符串，而是根据[application.properties](http://how2j.cn/k/springboot/springboot-jsp/1647.html#step7150) 中的视图重定向，到/WEB-INF/jsp目录下去寻找hello.jsp文件

```java
@Controller
public class HelloController {
	
	@RequestMapping("/hello")
	public String hello(Model model) {
		model.addAttribute("msg","你好");
		return "hello";
	}

}

```

##### 4.访问地址

访问地址http://127.0.0.1:8080/hello

### 五.热部署

目前的Springboot，当发生了任何修改之后，必须关闭后再启动Application类才能够生效，显得略微麻烦。 Springboot提供了热部署的方式，当发现任何类发生了改变，马上通过JVM类加载的方式，加载最新的类到虚拟机中。 这样就不需要重新启动也能看到修改后的效果了

做法很简单，在pom.xml中新增加一个依赖和一个插件就行了。

依赖：

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
</dependency>
```

插件：

```java
 <plugin>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-maven-plugin</artifactId>
 </plugin>
```

##### pom.xml

```java
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>

		<!-- servlet依赖 -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>

		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>

		<!-- tomcat的支持. -->
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
```

##### 现象

重新启动Application，然后随便修改一下HelloController, 就会如图所示观察到控制台的自动重启现象



### 六.错误处理

1.修改controller,使进入controller一定会产生异常

```java
@Controller
public class HelloController {
	
	@RequestMapping("/hello")
	public String hello(Model model) throws Exception {
		model.addAttribute("msg","how are you?");
		if(true) {
			throw new Exception("some Exception");
		}
		return "hello";
	}

}

```

2.增加异常处理，使其跳转的异常处理界面以及异常信息

```java
package springboot.exception;

import javax.servlet.http.HttpServletRequest;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

@ControllerAdvice
public class GlobalExceptionHandler {
	
	@ExceptionHandler(value=Exception.class)
	public ModelAndView defaultErrorHandler(HttpServletRequest req,Exception e) throws Exception {
		ModelAndView m = new ModelAndView();
		m.addObject("exception", e);
		m.addObject("url",req.getRequestURL());
		m.setViewName("errorPage");
		return m;
	}

}
```

3.结果页面显示

![1540777153823](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540777153823.png)

###  七.端口切换和上下文路径

如图所示，可以通过修改application.properties，修改访问的端口号和上下文路径

![1540777443578](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540777443578.png)

![1540777480676](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540777480676.png)

### 八.多配置文件切换

有时候在本地测试是使用8080端口，可是上线使用的又是80端口。 此时就可以通过多配置文件实现多配置支持与灵活切换

多配置文件:

3个配置文件：
核心配置文件：application.properties
开发环境用的配置文件：application-dev.properties
生产环境用的配置文件：application-pro.properties
这样就可以通过application.properties里的spring.profiles.active 灵活地来切换使用哪个环境了

1.application.properties

```java
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
spring.profiles.active=pro
```

2.application-dev.properties

```java
server.port=8080
server.context-path=/test
```

3.application-pro.properties

```java
server.port=80
server.context-path=/
```



### 九.yml

1.在springboot里还是要用到配置文件的。 除了使用.properties外，springboot还支持 yml格式。

如图所示，左边是application.properties的写法，右边是application.yml的写法，他们达到的效果是相同的

![1540778524642](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540778524642.png)

2.关于application.yml

在application.yml 文件书写注意：
1. 不同“等级” 用冒号隔开
2. 次等级的前面是空格，不能使用制表符(tab)
3. 冒号之后如果有值，那么冒号和值之间至少有一个空格，不能紧贴着



3.排斥性

要么用application.properties 要么用 application.yml，不要都用



### 十.JPA

JPA(Java Persistence API)是Sun官方提出的Java持久化规范，用来方便大家操作数据库。真正干活的可能是Hibernate,TopLink等等实现了JPA规范的不同厂商,默认是Hibernate

1.创建相应的表

2.application.properties

```java
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.url=jdbc:mysql://localhost:3306/how2j?characterEncoding=UTF-8
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.jpa.properties.hibernate.hbm2ddl.auto=update
```

spring.jpa.properties.hibernate.hbm2ddl.auto=update(表示会自动更新表结构，所以[创建表](http://how2j.cn/k/springboot/springboot-jpa/1648.html#step7173) 这一步其实是可以不需要的)

3.pom.xml

增加对mysql和jpa的支持

```java
<!-- mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.21</version>
        </dependency>
 
        <!-- jpa-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency> 
```

```java
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.demo.lj</groupId>
	<artifactId>springBoot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>

		<!-- servlet依赖 -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>

		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>

		<!-- tomcat的支持. -->
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
		</dependency>
		
		<!-- mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.21</version>
        </dependency>
 
        <!-- jpa-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

4.创建一个实体类Category

```java
package springboot.pojo;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="category_")
public class Category {
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="name")
	private String name;
	
	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

}
```

@Entity 注解表示这是个实体类
@Table(name = "category_") 表示这个类对应的表名是 category_ ，注意有下划线哦
@Id 表明主键
@GeneratedValue(strategy = GenerationType.IDENTITY) 表明自增长方式
@Column(name = "id") 表明对应的数据库字段名

5.创建一个dao接口CategoryDao,继承了JpaRepository，并且提供泛型<Category,Integer> 表示这个是针对Category类的DAO,Integer表示主键是Integer类型。
JpaRepository 这个父接口，就提供了CRUD, 分页等等一系列的查询了，直接拿来用，都不需要二次开发的了。

```java
package springboot.dao;

import org.springframework.data.jpa.repository.JpaRepository;

import springboot.pojo.Category;

public interface CategoryDao extends JpaRepository<Category,Integer>{
	
}
```



6.创建一个categoryController

```java
package springboot.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import springboot.dao.CategoryDao;
import springboot.pojo.Category;

@Controller
public class CategoryController {
	
	@Autowired
	CategoryDao categoryDao;
	
	@RequestMapping("/listCategory")
	public String listCategory(Model m) throws Exception{
		List<Category> cs = categoryDao.findAll();
		m.addAttribute("cs",cs);
		return "listCategory";
	}

}
```

7.创建一个jsp页面

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<table align='center' border='1' cellspacing='0'>
    <tr>
        <td>id</td>
        <td>name</td>
    </tr>
    <c:forEach items="${cs}" var="c" varStatus="st">
        <tr>
            <td>${c.id}</td>
            <td>${c.name}</td>
                
        </tr>
    </c:forEach>
</table>
</body>
</html>
```

8.结果展示

![1540797328351](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540797328351.png)

### 十一.mybatis注解方式

1.application.properties

```java
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.url=jdbc:mysql://localhost:3306/how2j?characterEncoding=UTF-8
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

2.pom.xml

```java
 
<!-- mybatis -->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.1.1</version>
</dependency>
<!-- mysql -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.21</version>
</dependency>
```

```java
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.demo.lj</groupId>
	<artifactId>springBoot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>

		<!-- servlet依赖 -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>

		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>

		<!-- tomcat的支持. -->
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
		</dependency>

		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.21</version>
		</dependency>

		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.1.1</version>
		</dependency>

		<!-- jpa -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

3.创建一个Category实体类

```java
package springboot.pojo;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

public class Category {
	
	private int id;
	
	private String name;
	
	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

}

```

4.创建一个接口CategoryDao

使用注解@Mapper 表示这是一个Mybatis Mapper接口。
使用@Select注解表示调用findAll方法会去执行对应的sql语句。

```java
package springboot.dao;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import springboot.pojo.Category;

@Mapper
public interface CategoryDao{
	
	@Select("select * from category_")
	List<Category> findAll();	
}

```

4.controller

```java
package springboot.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import springboot.dao.CategoryDao;
import springboot.pojo.Category;

@Controller
public class CategoryController {
	
	@Autowired
	CategoryDao categoryDao;
	
	@RequestMapping("/listCategory")
	public String listCategory(Model m) throws Exception{
		List<Category> cs = categoryDao.findAll();
		m.addAttribute("cs",cs);
		return "listCategory";
	}

}

```

5.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<table align='center' border='1' cellspacing='0'>
    <tr>
        <td>id</td>
        <td>name</td>
    </tr>
    <c:forEach items="${cs}" var="c" varStatus="st">
        <tr>
            <td>${c.id}</td>
            <td>${c.name}</td>
                
        </tr>
    </c:forEach>
</table>
</body>
</html>
```

### 十二.mybatis-xml方式

在注解的基础上改动(dao层的方法名要和Category.xml的id保持一致)

1.CategoryDao

```java
import java.util.List;

import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import springboot.pojo.Category;

@Mapper
public interface CategoryDao{
	
	List<Category> findAll();
	
	void insertCategory(Category category);
	
}
```

2.Category.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
<mapper namespace="springboot.dao.CategoryDao">
	<select id="findAll" resultType="Category">
		select * from category_
	</select>
</mapper>
```

3.application.properties

```java
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.url=jdbc:mysql://localhost:3306/how2j?characterEncoding=UTF-8
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

mybatis.mapper-locations=classpath:springboot/dao/*.xml
mybatis.type-aliases-package=springboot.pojo
```

### 十三.mybatis分页

1.pom.xml

增加对PageHelper的支持

```java
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>4.1.6</version>
</dependency>
```

```java
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.demo.lj</groupId>
	<artifactId>springBoot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>

		<!-- servlet依赖 -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>

		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>

		<!-- tomcat的支持. -->
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
		</dependency>

		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.21</version>
		</dependency>

		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.1.1</version>
		</dependency>

		<!-- jpa -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		
		<!-- mybatis分页插件 -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper</artifactId>
			<version>4.1.6</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

2.PageHelperConfig

注解@Configuration 表示PageHelperConfig 这个类是用来做配置的。
注解@Bean 表示启动PageHelper这个拦截器

新增加一个包springboot.config， 然后添加一个类PageHelperConfig ，其中进行PageHelper相关配置。
offsetAsPageNum:设置为true时，会将RowBounds第一个参数offset当成pageNum页码使用.

```java
p.setProperty("offsetAsPageNum", "true");
```

rowBoundsWithCount:设置为true时，使用RowBounds分页会进行count查询.

```java
p.setProperty("rowBoundsWithCount", "true");
```

reasonable：启用合理化时，如果pageNum<1会查询第一页，如果pageNum>pages会查询最后一页。

```java
p.setProperty("reasonable", "true");
```

```java
package springboot.config;

import java.util.Properties;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.github.pagehelper.PageHelper;

@Configuration
public class PageHelperConfig {
	
	@Bean
	public PageHelper pageHelper() {
		PageHelper pageHelper = new PageHelper();
		Properties p = new Properties();
		p.setProperty("offsetAsPageNum", "true");
        p.setProperty("rowBoundsWithCount", "true");
        p.setProperty("reasonable", "true");
        pageHelper.setProperties(p);
		return pageHelper;
	}

}

```

3.修改CategoryDao

```java
package springboot.dao;



import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import springboot.pojo.Category;

@Mapper
public interface CategoryDao{
	
	@Select("select * from category_")
	List<Category> findAll();
	
	@Insert("insert into category_(name) values(#{name})")
	public int insertCategory(Category category);
	
	@Delete("delete from category_ where id = #{id}")
	public void delete(int id);
	
	@Select("select * from category_ where id = #{id}")
	public Category get(int id);
	
	@Update("update category_ set name = #{name} where id = #{id}")
	public int update(Category category);
	
}

```

4.修改CategoryController

```java
package springboot.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;

import springboot.dao.CategoryDao;
import springboot.pojo.Category;

@Controller
public class CategoryController {
	
	@Autowired
	CategoryDao categoryDao;
	
	@RequestMapping("/listCategory")
	public String listCategory(Model m,@RequestParam(value="start",defaultValue="0") int start,@RequestParam(value="size",defaultValue="5") int size) throws Exception{
		PageHelper.startPage(start,size,"id desc");
		
		List<Category> cs = categoryDao.findAll();
		
		PageInfo<Category> page = new PageInfo<Category>(cs);
		
		m.addAttribute("page",page);
		
		return "listCategory";
	}
	
	@RequestMapping("/insertCategory")
	public String insertCategory() {
		for(int i=0;i<100;i++) {
			Category category = new Category();
			category.setName("category "+i);
			categoryDao.insertCategory(category);
		}
		return "redirect:listCategory";
	}
	
	@RequestMapping("/deleteCategory")
	public String deleteCategory(int id) throws Exception{
		categoryDao.delete(id);
		return "redirect:listCategory";
	}
	
	@RequestMapping("/getCategory")
	public String getCategory(Model m) throws Exception{
		Category cs = categoryDao.get(1);
		m.addAttribute("cs",cs);
		return "listCategory";
	}
	
	@RequestMapping("/updateCategory")
	public String updateCategory(Model m) throws Exception{
		Category category = new Category();
		category.setId(2);
		category.setName("haha");
		categoryDao.update(category);
		return "redirect:listCategory";
	}
}

```

在参数里接受当前是第几页 start ，以及每页显示多少条数据 size。 默认值分别是0和5。

```java
@RequestParam(value = "start", defaultValue = "0") int start,@RequestParam(value = "size", defaultValue = "5"
```

根据start,size进行分页，并且设置id 倒排序

```java
PageHelper.startPage(start,size,"id desc");
```

因为PageHelper的作用，这里就会返回当前分页的集合了

```java
List<Category> cs=categoryMapper.findAll();
```

根据返回的集合，创建PageInfo对象

```java
PageInfo<Category> page = new PageInfo<>(cs);
```

把PageInfo对象扔进model，以供后续显示

```java
m.addAttribute("page", page);
```

跳转到listCategory.jsp

```java
return "listCategory";
```

5.listCategory.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<div style="width: 500px; margin: 20px auto; text-align: center">
		<table align='center' border='1' cellspacing='0'>
			<tr>
				<td>id</td>
				<td>name</td>
				<td>编辑</td>
				<td>删除</td>
			</tr>
			<c:forEach items="${page.list}" var="c" varStatus="st">
				<tr>
					<td>${c.id}</td>
					<td>${c.name}</td>
					<td><a href="editCategory?id=${c.id}">编辑</a></td>
					<td><a href="deleteCategory?id=${c.id}">删除</a></td>
				</tr>
			</c:forEach>

		</table>
		<br>
		<div>
			<a href="?start=1">[首 页]</a> <a href="?start=${page.pageNum-1}">[上一页]</a>
			<a href="?start=${page.pageNum+1}">[下一页]</a> 
			<a href="?start=${page.pages}">[末 页]</a>
		</div>
		<br>
		<form action="addCategory" method="post">
			name: <input name="name"> <br>
			<button type="submit">提交</button>
		</form>
	</div>
</body>
</html>
```

### 十四.上传文件

1.upload.jsp

method="post" 是必须的

enctype="multipart/form-data" 是必须的，表示提交二进制文件

name="file" 是必须的，和后续服务端对应

accept="image/*" 表示只选择图片

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="upload" method="post" enctype="multipart/form-data">
		选择图片:<input type="file" name="file" accept="image/*"/> <br>
		<input type="submit" value="上传">
	</form>
</body>
</html>
```

2.UploadController

```java
package springboot.controller;

import java.io.File;
import java.io.IOException;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

@Controller
public class UploadController {
	
	@RequestMapping("/uploadPage")
	public String uploadPage() {
		return "upload";
	}
	
	@RequestMapping(value="/upload",method=RequestMethod.POST)
	public String upload(HttpServletRequest req,@RequestParam("file") MultipartFile file,Model m) {
		try {
			String fileName = System.currentTimeMillis()+file.getOriginalFilename();
			String destFileName = req.getServletContext().getRealPath("")+"uploaded"+File.separator+fileName;
			File destFile = new File(destFileName);
			destFile.getParentFile().mkdirs();
			file.transferTo(destFile);
			m.addAttribute("fileName", fileName);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
			return "上传失败"+e.getMessage();
		}
		return "showImg";
	}
}
```

接受上传的文件

```java
@RequestParam("file") MultipartFile file
```

根据时间戳创建新的文件名，这样即便是第二次上传相同名称的文件，也不会把第一次的文件覆盖了

```java
String fileName = System.currentTimeMillis()+file.getOriginalFilename();
```

通过req.getServletContext().getRealPath("") 获取当前项目的真实路径，然后拼接前面的文件名

```java
String destFileName=req.getServletContext().getRealPath("")+"uploaded"+File.separator+fileName;
```

第一次运行的时候，这个文件所在的目录往往是不存在的，这里需要创建一下目录

```java
File destFile = new File(destFileName);
destFile.getParentFile().mkdirs();
```

把浏览器上传的文件复制到希望的位置

```java
file.transferTo(destFile);
```

把文件名放在model里，以便后续显示用

```java
m.addAttribute("fileName",fileName);
```

3.showImg.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<div>${fileName}</div>
	<img width="300px" alt="this is img" src="../../../uploaded/${fileName}">
</body>
</html>
```

4.application.properties

```java
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.url=jdbc:mysql://localhost:3306/how2j?characterEncoding=UTF-8
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.http.multipart.max-file-size=100Mb
spring.http.multipart.max-request-size=100Mb
```

5.访问地址

http://localhost:8080/upload