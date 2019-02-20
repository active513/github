### 一.mybatis基本配置

##### 1.创建项目

##### 2.导入jar包

​	mybatis-3.4.5.jar

​	mysql-connect-java-6.0.5.jar

##### 3.创建实体类Category

```java
public class Category {
	
	private Integer id;
	private String name;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	@Override
	public String toString() {
		return "Category [id=" + id + ", name=" + name + "]";
	}
}
```

##### 4.创建mybatis配置文件

###### (1).mybatis-config.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <configuration>
  
 
 <!--  加载classpath路径下的jdbc.propweties -->
  <properties resource="jdbc.properties"/>
  	
  	<!--  自动扫描com.pojo下的类型，使得在后续配置文件mybatis-mapper.xml中使用resultType的时候，可以直接使用Category,而不必写全com.pojo.Category -->
  	 <typeAliases>
  		<package name="com.pojo"/>
  	</typeAliases>
  	
  	<environments default="development">
  		<environment id="development">
  			<transactionManager type="JDBC"/>
  			<dataSource type="POOLED">
  				<property name="driver" value="${driver}"/>
  				<property name="url" value="${url}"/>
  				<property name="username" value="root"/>
  				<property name="password" value="root"/>
  			</dataSource>
  		</environment>
  	</environments>
  	
  	<mappers>
  		<mapper resource="mybatis-mapper.xml"/>
  	</mappers>
  </configuration>
```

###### (2).mybatis-mapper.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.pojo">
  	<select id="selectCategory" resultType="Category">
  		select * from category_
  	</select>
  </mapper>
```

##### 5.测试类(sqlSessionFactory)

```java
public class test {
	public static void main(String[] args) throws Exception {
		// TODO Auto-generated method stub
		String resource = "mybatis-config.xml";
		InputStream in = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlf = new SqlSessionFactoryBuilder().build(in);
		SqlSession session = sqlf.openSession();
		List<Category> categoryList = session.selectList("selectCategory");
		for(Category category:categoryList) {
			System.out.println(category);
		}
	}

}
```



### 二.if标签

若id=null就执行select * from category_，若id!=null,就执行select * from category_ where id=#{id}

```java
<select id="selectCategory" parameterType="Category" resultType="Category">
  		select * from category_
  		<if test="id!=null">
  			where id=#{id}
  		</if>
</select>
```



### 三.where标签

进行多条件判断，若只用if标签则会写成这样:

```java
<select id="listProduct" resultType="Product">
	select * from product_
	<if test="name!=null">
		where name like concat('%',#{name},'%')
	</if>		 	
	<if test="price!=0">
		and price > #{price}
	</if>		 	
</select>
```

这么写的问题是：当没有name参数，却有price参数的时候，执行的sql语句就会是:

```java
select * from product_ and price > 10
```

这样执行就会报错



这个问题可以通过<where>标签来解决，如代码所示

```java
<select id="listProduct" resultType="Product">
        select * from product_
        <where>
            <if test="name!=null">
                and name like concat('%',#{name},'%')
            </if>        
            <if test="price!=null and price!=0">
                and price > #{price}
            </if>
        </where>     
    </select>
```

<where>标签会进行自动判断
如果任何条件都不成立，那么就在sql语句里就不会出现where关键字
如果有任何条件成立，会自动去掉多出来的 and 或者 or。



在测试代码这样写

```java
System.out.println("多条件查询");
        Map<String,Object> params = new HashMap<>();
//        params.put("name","a");
        params.put("price","10");
        List<Product> ps2 = session.selectList("listProduct",params);
        for (Product p : ps2) {
            System.out.println(p);
        }      
         
        session.commit();
        session.close();
```



### 四.set标签

与[where标签](http://how2j.cn/k/mybatis/mybatis-where/1114.html#step4271)类似的，在update语句里也会碰到多个字段相关的问题。 在这种情况下，就可以使用set标签：

```java
    <set>
     	<if test="name != null">name=#{name},</if>
     	<if test="price != null">price=#{price}</if>
    </set>
```



### 五.when标签

Mybatis里面没有else标签，但是可以使用when otherwise标签来达到这样的效果

```java
 
<select id="listProduct" resultType="Product">
	  SELECT * FROM product_ 
	  <where>
	  	<choose>
		  <when test="name != null">
		    and name like concat('%',#{name},'%')
		  </when>			  
		  <when test="price !=null and price != 0">
		    and price > #{price}
		  </when>			  		
	  	  <otherwise>
	  	  	and id >1
	  	  </otherwise>
	  	</choose>
	  </where>
</select>
```

起作用是： 提供了任何条件，就进行条件查询，否则就使用id>1这个条件。



### 六.通过注解方式实现增删改查CRUD

新增加接口CategoryMapper ，并在接口中声明的方法上，加上注解对比[配置文件Category.xml](http://how2j.cn/k/mybatis/mybatis-crud/1088.html#step4213)，其实就是把SQL语句从XML挪到了注解上来



mybatis-config.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <configuration>
  
 
 <!--  加载classpath路径下的jdbc.propweties -->
  <properties resource="jdbc.properties"/>
  	
  	<!--  自动扫描com.pojo下的类型，使得在后续配置文件mybatis-mapper.xml中使用resultType的时候，可以直接使用Category,而不必写全com.pojo.Category -->
  	 <typeAliases>
  		<package name="com.pojo"/>
  	</typeAliases>
  	
  	<environments default="development">
  		<environment id="development">
  			<transactionManager type="JDBC"/>
  			<dataSource type="POOLED">
  				<property name="driver" value="${driver}"/>
  				<property name="url" value="${url}"/>
  				<property name="username" value="root"/>
  				<property name="password" value="root"/>
  			</dataSource>
  		</environment>
  	</environments>
  	
  	<mappers>
  		<mapper class="com.mapper.CategoryMapper" />
  	</mappers>
  </configuration>
```

CategoryMapper

```java
package com.mapper;

import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import com.pojo.Category;

public interface CategoryMapper {
	
	@Insert("insert into category_(name) values(#{name})")
	public int addCategory(Category category);
	
	@Delete("delete from category_ where id=#{id}")
	public void deleteCategory(Category category);
	
	@Select("select * from category_ where id=#{id}")
	public Category getCategory(Integer id);
	
	@Update("update category_ set name=#{name} where id=#{id}")
	public int updateCategory(Category category);
	
	@Select("select * from category_")
	public List<Category> selectCategory();

}

```

测试类

```java
package com.demo;

import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.mapper.CategoryMapper;
import com.pojo.Category;

public class test {
	
	public static void main(String[] args) throws Exception {
		String resources="mybatis-config.xml";
		InputStream in = Resources.getResourceAsStream(resources);
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
		SqlSession session = factory.openSession();
		CategoryMapper mapper = session.getMapper(CategoryMapper.class);
		getCategory(mapper);
	}
	
	public static void selectCategory(CategoryMapper mapper){
		List<Category> categoryList = mapper.selectCategory();
		for(Category category : categoryList) {
			System.out.println(category);
		}
	}
	
	public static void getCategory(CategoryMapper mapper) {	
		Integer id = 1;
		Category c = mapper.getCategory(id);
		System.out.println(c);
	}
	
	public static void updateCategory(CategoryMapper mapper) {
		Category category = new Category();
		category.setId(2);
		category.setName("llllll");
		mapper.updateCategory(category);
		System.out.println(category);
	}

}

```



