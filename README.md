Solr-Guide
==========

#什么是Solr

Solr是一个独立的企业级搜索应用服务器，它对外提供类似于Web-service的API接口。用户可以通过http请求，向搜索引擎服务器提交一定格式的XML，Json等文件，生成索引；也可以通过Http Get操作提出查找请求，并得到XML，JSONd等格式的返回结果，Solr是一个高性能，采用Java5开发，基于Lucene的全文搜索服务器。同时对其进行了扩展，提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展并对查询性能进行了优化，并且提供了一个完善的功能管理界面，是一款非常优秀的全文搜索引擎。

#如何使用Solr

##Solr的安装与配置

1、安装jdk1.6.0_27

2、下载solr-4.7.2

3、下载apache-tomcat-6.0.43

4、复制solr-4.7.2/example下的solr文件夹到Tomcat的根目录下

5、复制solr-4.7.2/dist下的solr-4.7.2.war到apache-tomcat-6.0.43/webapps文件夹下，并重命名为：solr.war

6、复制solr-4.7.2/example/lib/ext文件夹下的所有jar到apache-tomcat-6.0.43/lib下

7、复制solr-4.7.2/example/resources下的log4j.properties文件到apache-tomcat-6.0.43/lib下

8、在文件夹apache-tomcat-6.0.43/conf/Catalina/localhost下创建solr.xml，内容如下：

```
<?xml version="1.0" encoding="utf-8"?>
<Context docBase="webapps/solr.war" debug="0" crossContext="true">
  <Environment name="solr/home" type="java.lang.String" value="c:/Program Files/Apache Software Foundation /Tomcat7/solr" override="true"/>
</Context>
```
9、重启Tomcat服务器，在浏览器输入http://localhost:8080/solr 能看到solr的页面就表示安装成功了




