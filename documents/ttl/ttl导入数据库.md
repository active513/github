### 一.导出ttl文件

##### 1.查看当前导入条数日志

​	tail -f status.log | grep At

##### 2.批量导入

1.在root用户下进入相应目录
​		cd /ssd/r2rml-parser-0.8-alpha-new	

2.修改相应的modify.properties

​	![1540793162419](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540793162419.png)

​	![1540793200375](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540793200375.png)

​	![1540793239031](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540793239031.png)

​	![1540793264283](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1540793264283.png)



3.将ttl文件放入至dspace目录下

4.执行modify.jar
​		java -jar modify.jar

##### 3.单独导入

1.在root用户下进入相应目录
​		cd /ssd/r2rml-parser-0.8-alpha-new

2.修改相应的r2rml.properties

3.将ttl文件放入至dspace目录下

4.在当前目录下建相应的graph
​		http://nari.org/data/T_SB_ZLSB_HLF(最后面的是相应的表名)

3.运行r2rml-parser.sh
​		./r2rml-parser.sh

4.将ttl导入数据库

​	1.将要导入的ttl放入指定目录

 	2.进入248的vir用户
​        	su - vir

​	3.cd lqvpz2zz_2/bin

4.进入isql数据库
​	./isql 1111 dba dba	

5.查看数据库有没有东西
​	select * from db.dba.load_list;

​	如果有则删掉
​		delete from db.dba.load_list;

6.根据位置ttl所在位置，选择相应的ttl导入路径
​	SQL> ld_dir('/home/vir/owl_metamodel','*.owl','http://nari.org');

​	SQL> ld_dir('/data/r2rml-parser-0.8-alpha-new','*.ttl','http://nari.org');

​	SQL> ld_dir('/ssd/r2rml-parser-0.8-alpha-new','*.ttl','http://nari.org');

​	SQL> ld_dir('/home/vir/tf_xml','*.xml','http://nari.org/tf_kx');

7.SQL> rdf_loader_run();

8.SQL> checkpoint;

9.删除已经导入数据库的数据

​	 SPARQL CLEAR GRAPH <http://nari.org/data/SA_ORG>;   //删除元模型

### 三.数据库地址

http://192.168.204.248:8890   