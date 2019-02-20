# 一.依赖注入

	### 1.基于构造函数的依赖注入

```java
package com.pojo;
public class SpellChecker {
	public SpellChecker() {
		System.out.println("spellChecker constructor");
	}
	public void checkSpelling() {
		System.out.println("checkSpelling");
	}
}
```

```java
public class TextEditor {
	private SpellChecker spellChecker;
	public TextEditor(SpellChecker spellChecker) {
		System.out.println("TextEditor constructor");
		this.spellChecker = spellChecker;
	}
	public void spellCheck() {
		spellChecker.checkSpelling();
	}
}
```

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
<bean id="textEditor" class="com.pojo.TextEditor">
	<constructor-arg ref="spellChecker"/>
</bean>
```

```java
public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
		TextEditor texteditor = (TextEditor) context.getBean("textEditor");
		texteditor.spellCheck();
	}
```





### 2.基于setter的依赖注入

​	setter依赖注入使用property,构造函数依赖注入使用<constructor-arg>

```java
public class TextEditor {
	private SpellChecker spellChecker;
	public void setSpellChecker(SpellChecker spellChecker) {
		System.out.println();
		this.spellChecker = spellChecker;
	}
	public void spellCheck() {
		spellChecker.checkSpelling();
	}
}
```

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
	<bean id="textEditor" class="com.pojo.TextEditor">
		<property name="spellChecker" ref="spellChecker"></property>
	</bean>
```

其他的同上



### 3.使用 p-namespace 实现 XML 配置

如果你有许多的设值函数方法，那么在 XML 配置文件中使用 **p-namespace** 是非常方便的。让我们查看一下区别：

以下两种配置等价

```java
<bean id="john-classic" class="com.example.Person">
      <property name="name" value="John Doe"/>
      <property name="spouse" ref="jane"/>
</bean>

<bean name="jane" class="com.example.Person">
      <property name="name" value="John Doe"/>
</bean>
```

```java
<bean id="john-classic" class="com.example.Person"
      p:name="John Doe"
      p:spouse-ref="jane"/>
</bean>

<bean name="jane" class="com.example.Person"
      p:name="John Doe"/>
</bean>
```





### 4.spring注入内部的beans

以下两种形式相当

```java
<bean id="textEditor" class="com.pojo.TextEditor">
		<property name="spellChecker">
			<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
		</property>
</bean>
```

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
<bean id="textEditor" class="com.pojo.TextEditor">
		<property name="spellChecker" ref="spellChecker"></property>
</bean>
```



### 5.spring注入集合

| list     | set      | map                    | prop                 |
| -------- | -------- | ---------------------- | -------------------- |
| 可以重复 | 不能重复 | 名称和值可以是任意类型 | 名称和值只能是字符串 |

```java
public class javaCollection {
	List addressList;
	Set addressSet;
	Map addressMap;
	Properties addressProp;
	public List getAddressList() {
		return addressList;
	}
	public void setAddressList(List addressList) {
		System.out.println("list element :"+addressList);
		this.addressList = addressList;
	}
	public Set getAddressSet() {
		return addressSet;
	}
	public void setAddressSet(Set addressSet) {
		System.out.println("set element :"+addressSet);
		this.addressSet = addressSet;
	}
	public Map getAddressMap() {
		return addressMap;
	}
	public void setAddressMap(Map addressMap) {
		System.out.println("map element :"+addressMap);
		this.addressMap = addressMap;
	}
	public Properties getAddressProp() {
		return addressProp;
	}
	public void setAddressProp(Properties addressProp) {
		System.out.println("prop element :"+addressProp);
		this.addressProp = addressProp;
	}
	@Override
	public String toString() {
		return "javaCollection [addressList=" + addressList + ", addressSet=" + addressSet + ", addressMap="
				+ addressMap + ", addressProp=" + addressProp + "]";
	}
}
```

```java
<bean id="javaCollection" class="com.pojo.javaCollection">
		<property name="addressList">
			<list>
				<value>india</value>
				<value>china</value>
				<value>USA</value>
				<value>USA</value>
			</list>
		</property>
		
		<property name="addressSet">
			<set>
				<value>india</value>
				<value>china</value>
				<value>USA</value>
				<value>USA</value>
			</set>
		</property>
		
		<property name="addressMap">
			<map>
				<entry key="1" value="india"/>
				<entry key="2" value="china"/>
				<entry key="3" value="USA"/>
				<entry key="4" value="USA"/>
			</map>
		</property>
		
		<property name="addressProp">
			<props>
				<prop key="1">india</prop>
				<prop key="2">china</prop>
				<prop key="3">USA</prop>
				<prop key="4">USA</prop>
			</props>
		</property>
	</bean>
```





### 6.spring自动装配

| byName                                 | byType                                                       | constructor                                                  |
| -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 将它的属性与bean名称相同的属性进行连接 | 它的**类型**匹配配置文件中的一个确切的 bean 名称，它将尝试匹配和连接属性的类型。如果存在不止一个这样的 bean，则一个致命的异常将会被抛出 | 类似于 byType，但该类型适用于构造函数参数类型。如果在容器中没有一个构造函数参数类型的 bean，则一个致命错误将会发生 |

###### byNameor

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
	
<bean id="textEditor" class="com.pojo.TextEditor" autowire="byName"></bean>
```

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
	
<bean id="textEditor" class="com.pojo.TextEditor">
	<property name="spellChecker" ref="spellChecker"></property>
</bean>
```



###### byType

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
	
<bean id="textEditor" class="com.pojo.TextEditor" autowire="byType"></bean
```

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
	
<bean id="textEditor" class="com.pojo.TextEditor">
	<property name="spellChecker" ref="spellChecker"></property>
</bean>
```



###### constructor

```java
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
	
<bean id="textEditor" class="com.pojo.TextEditor" autowire="constructor"></bean>
```

```java
public class TextEditor {
	
	private SpellChecker spellChecker;
	
	public TextEditor(SpellChecker spellChecker) {
		this.spellChecker=spellChecker;
	}
	public void spellCheck() {
		spellChecker.checkSpelling();
	}
}
```





### 7.spring基于注解的配置

注解连线在默认情况下在 Spring 容器中不打开。因此，在可以使用基于注解的连线之前，我们将需要在我们的 Spring 配置文件中启用它。所以如果你想在 Spring 应用程序中使用的任何注解，可以考虑到下面的配置文件。

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">
  
<context:annotation-config/>

</beans>
```

###### 1.context:annotation-config

当我们需要使用注解模式时，直接在Spring配置文件中定义这些Bean显得比较笨拙，例如：
使用@Autowired注解，必须事先在Spring容器中声明AutowiredAnnotationBeanPostProcessor的Bean:

```java
<bean class="org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor "/>

```

使用 @Required注解，就必须声明RequiredAnnotationBeanPostProcessor的Bean：

```java
<bean class="org.springframework.beans.factory.annotation.RequiredAnnotationBeanPostProcessor"/>

```

简单的说，用什么注解，就需要声明对应的BeanPostProcessor。这样的声明未免太不优雅，而Spring为我们提供了一种极为方便注册这些BeanPostProcessor的方式，即使用<context:annotation- config/>隐式地向 Spring容器注册AutowiredAnnotationBeanPostProcessor、RequiredAnnotationBeanPostProcessor、CommonAnnotationBeanPostProcessor以及PersistenceAnnotationBeanPostProcessor这4个BeanPostProcessor

另外，在我们使用注解时一般都会配置扫描包路径选项，即<context:component-scan/>。该配置项其实也包含了自动注入上述processor的功能，因此当使用<context:component-scan/>后，即可将<context:annotation-config/>省去。



| @Required                                 | @Autowired                                                   | @Qualifier                                                   | JSR-250 Annotations                                          |
| ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 应用于 bean 属性的 setter 方法,且必须赋值 | 应用到 bean 属性的 setter 方法，非 setter 方法，构造函数和属性。 | 通过指定确切的将被连线的 bean，@Autowired 和 @Qualifier 注解可以用来删除混乱 | Spring 支持 JSR-250 的基础的注解，其中包括了 @Resource，@PostConstruct 和 @PreDestroy 注解 |



###### @Required

```java
<bean id="person" class="com.pojo.Person">
	<property name="name" value="haha"></property>
	<!-- <property name="age" value="1"></property> -->
</bean>
```

```java
public class Person {
	
	private String name;
	private Integer age;
	
	public Person() {
		System.out.println("对象创建了");
	}
	
	public String getName() {
		return name;
	}
	
	@Required
	public void setName(String name) {
		this.name = name;
	}
	public Integer getAge() {
		return age;
	}
	
	@Required
	public void setAge(Integer age) {
		this.age = age;
	}
	
	@Override
	public String toString() {
		return "Person [name="+name+",age="+age+"]";
	}
}
```

```java
Caused by: org.springframework.beans.factory.BeanInitializationException: Property 'age' is required for bean 'person'
```





###### @Autowired

1.当 Spring遇到一个在 <span style="color:#f00;text-decoration:underline;">setter 方法</span>中使用的 @Autowired 注释，它会在方法中视图执行 **byType** 自动连接。

```java
public class TextEditor {
	
	private SpellChecker spellChecker;
	
    public SpellChecker getSpellChecker() {
		return spellChecker;
	}

	@Autowired
	public void setSpellChecker(SpellChecker spellChecker) {
		this.spellChecker = spellChecker;
	}
    
	public void spellCheck() {
		spellChecker.checkSpelling();
	}
}
```

```java
<context:annotation-config/>
	
<bean id="textEditor" class="com.pojo.TextEditor"></bean>
	
<bean id="spellChecker" class="com.pojo.SpellChecker"></bean>
```



2.你可以在属性中使用 **@Autowired** 注释来除去 setter 方法。当时使用 为自动连接属性传递的时候，Spring 会将这些传递过来的值或者引用自动分配给那些属性.

```java
public class TextEditor {
	
	@Autowired
	private SpellChecker spellChecker;
	
	public SpellChecker getSpellChecker() {
		return spellChecker;
	}

	public void spellCheck() {
		spellChecker.checkSpelling();
	}
}
```



3.构造函数中的 @Autowired

```java
public class TextEditor {
	
	private SpellChecker spellChecker;
	
	@Autowired
	public TextEditor(SpellChecker spellChecker) {
		this.spellChecker=spellChecker;
	}
	
	public SpellChecker getSpellChecker() {
		return spellChecker;
	}

	public void setSpellChecker(SpellChecker spellChecker) {
		this.spellChecker = spellChecker;
	}


	public void spellCheck() {
		spellChecker.checkSpelling();
	}

}
```





###### @Qualifier

可能会有这样一种情况，当你创建多个具有相同类型的 bean 时，并且想要用一个属性只为它们其中的一个进行装配，在这种情况下，你可以使用 **@Qualifier** 注释和 **@Autowired** 注释通过指定哪一个真正的 bean 将会被装配来消除混乱。

```java
<context:annotation-config/>
	
	<bean id="p1" class="com.pojo.Person">
		<property name="name" value="haha"></property>
		<property name="age" value="1"></property>
	</bean>
	
	<bean id="p2" class="com.pojo.Person">
		<property name="name" value="hehe"></property>
		<property name="age" value="2"></property>
	</bean>
	
	<bean id="testPerson" class="com.pojo.TestPerson"></bean>
```

```java
public class TestPerson {
	
	@Autowired
	@Qualifier("p1")
	private Person person;
	
	public void printMs() {
		System.out.println("name:"+person.getName());
	}

}
```



