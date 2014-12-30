Solr-Guide
==========

#什么是Solr

Solr是一个独立的企业级搜索应用服务器，它对外提供类似于Web-service的API接口。用户可以通过http请求，向搜索引擎服务器提交一定格式的XML，Json等文件，生成索引；也可以通过Http Get操作提出查找请求，并得到XML，JSONd等格式的返回结果，Solr是一个高性能，采用Java5开发，基于Lucene的全文搜索服务器。同时对其进行了扩展，提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展并对查询性能进行了优化，并且提供了一个完善的功能管理界面，是一款非常优秀的全文搜索引擎。

#如何使用Solr

##Solr的安装与配置

1、[到此下载jdk1.6.0_27](http://java.sun.com/j2se/downloads.html)

2、[到此下载solr-4.7.2](http://www.apache.org/dyn/closer.cgi/lucene/solr/)

3、[到此下载apache-tomcat-6.0.43](http://tomcat.apache.org/)

4、复制solr-4.7.2/example下的solr文件夹到Tomcat的根目录下（其实可以放到任何目录，这个目录就是下面配置文件solr.xml中solr/home的值）

5、复制solr-4.7.2/dist下的solr-4.7.2.war到apache-tomcat-6.0.43/webapps文件夹下，并重命名为：solr.war

6、复制solr-4.7.2/example/lib/ext文件夹下的所有jar到apache-tomcat-6.0.43/lib下

7、复制solr-4.7.2/example/resources下的log4j.properties文件到apache-tomcat-6.0.43/lib下

8、在文件夹apache-tomcat-6.0.43/conf/Catalina/localhost下创建solr.xml，内容如下：
```
<?xml version="1.0" encoding="utf-8"?>
<Context docBase="webapps/solr.war" debug="0" crossContext="true">
  <!-- 设置环境变量solr/home的值为solr的目录：E:/apache-tomcat-6.0.43/solr -->
  <Environment name="solr/home" type="java.lang.String" value="E:/apache-tomcat-6.0.43/solr" override="true"/>
</Context>
```
9、重启Tomcat服务器，在浏览器输入http://localhost:8080/solr 能看到solr的页面就表示安装成功了

##安装IK分词器

1、[点击下载IK分词器](https://code.google.com/p/ik-analyzer/downloads/detail?name=IK%20Analyzer%202012FF_hf1.zip&can=2&q=)

2、复制IKAnalyzer.cfg.xml、IKAnalyzer2012FF_u1.jar、stopword.dic三个文件到apache-tomcat-6.0.43/webapps/solr/WEB-INF/lib文件夹下

3、修改apache-tomcat-6.0.43/solr/collection1/conf文件夹下的schema.xml文件，在<types></types>中间增加以下内容：
```
<fieldType name="text_ik" class="solr.TextField">
    <analyzer type="index" isMaxWordLength="false" class="org.wltea.analyzer.lucene.IKAnalyzer"/>
    <analyzer type="query" isMaxWordLength="true" class="org.wltea.analyzer.lucene.IKAnalyzer"/>
</fieldType>
```

4、启动Tomcat服务器，在浏览器中输入http://localhost:8080/solr，在页面左侧Core Selector下拉框选择collection1，点击Analysis菜单，在右侧页面Field Value (Index)文本框中输入要测试分词的中文语句，在下方Analyse FieldName/FieldType中选择text_ik，然后点击左侧Analyse Values按钮就能看到分词结果了


