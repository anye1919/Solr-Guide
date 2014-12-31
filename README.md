Solr-Guide
==========

#什么是Solr

Solr是一个独立的企业级搜索应用服务器，它对外提供类似于Web-service的API接口。用户可以通过http请求，向搜索引擎服务器提交一定格式的XML，Json等文件，生成索引；也可以通过Http Get操作提出查找请求，并得到XML，JSONd等格式的返回结果，Solr是一个高性能，采用Java5开发，基于Lucene的全文搜索服务器。同时对其进行了扩展，提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展并对查询性能进行了优化，并且提供了一个完善的功能管理界面，是一款非常优秀的全文搜索引擎。

#如何使用Solr

##Solr的安装与配置

- [到此下载jdk1.6.0_27](http://java.sun.com/j2se/downloads.html)

- [到此下载solr-4.7.2](http://www.apache.org/dyn/closer.cgi/lucene/solr/)

- [到此下载apache-tomcat-6.0.43](http://tomcat.apache.org/)

- 复制solr-4.7.2/example下的solr文件夹到Tomcat的根目录下（其实可以放到任何目录，这个目录就是下面配置文件solr.xml中solr/home的值）

- 复制solr-4.7.2/dist下的solr-4.7.2.war到apache-tomcat-6.0.43/webapps文件夹下，并重命名为：solr.war

- 复制solr-4.7.2/example/lib/ext文件夹下的所有jar到apache-tomcat-6.0.43/lib下

- 复制solr-4.7.2/example/resources下的log4j.properties文件到apache-tomcat-6.0.43/lib下

- 在文件夹apache-tomcat-6.0.43/conf/Catalina/localhost下创建solr.xml，内容如下：
```
<?xml version="1.0" encoding="utf-8"?>
<Context docBase="webapps/solr.war" debug="0" crossContext="true">
  <!-- 设置环境变量solr/home的值为solr的目录：E:/apache-tomcat-6.0.43/solr -->
  <Environment name="solr/home" type="java.lang.String" value="E:/apache-tomcat-6.0.43/solr" override="true"/>
</Context>
```
- 重启Tomcat服务器，在浏览器输入http://localhost:8080/solr 能看到solr的页面就表示安装成功了

##安装IK分词器

- [点击下载IK分词器](https://code.google.com/p/ik-analyzer/downloads/detail?name=IK%20Analyzer%202012FF_hf1.zip&can=2&q=)

- 复制IKAnalyzer.cfg.xml、IKAnalyzer2012FF_u1.jar、stopword.dic三个文件到apache-tomcat-6.0.43/webapps/solr/WEB-INF/lib文件夹下

- 修改apache-tomcat-6.0.43/solr/collection1/conf文件夹下的schema.xml文件，在<types></types>中间增加以下内容：
```
<fieldType name="text_ik" class="solr.TextField">
    <analyzer type="index" isMaxWordLength="false" class="org.wltea.analyzer.lucene.IKAnalyzer"/>
    <analyzer type="query" isMaxWordLength="true" class="org.wltea.analyzer.lucene.IKAnalyzer"/>
</fieldType>
```
- 启动Tomcat服务器，在浏览器中输入http://localhost:8080/solr，在页面左侧Core Selector下拉框选择collection1，点击Analysis菜单，在右侧页面Field Value (Index)文本框中输入要测试分词的中文语句，在下方Analyse FieldName/FieldType中选择text_ik，然后点击左侧Analyse Values按钮就能看到分词结果了

##使用Documents模块导入数据

文件上传模式：文档类型选择File Upload，从D:\solr-4.7.2\example\exampledocs目录下选择定义好的文档money.xml，提交后Solr就会对该文档进行解析并建立索引

##使用Query模块查询数据

在money.xml中有name为manu的field，在q输入框里输入manu:bank，点击Execute Query按钮执行查询，就可以查询出manu这个field包含bank的所有文档

##配置多个core

- 将D:\solr-4.7.2\example\multicore文件夹下的core0和core1文件夹复制到E:\apache-tomcat-6.0.43\solr文件夹，因为此文件夹已经有一个名为collection1的core，所以现在有了3个core存在
- 修改solr.xml配置文件，文件内容如下：
```
<?xml version="1.0" encoding="UTF-8" ?>
<!-- 单core的配置 -->
<!-- <solr>

  <solrcloud>
    <str name="host">${host:}</str>
    <int name="hostPort">${jetty.port:8983}</int>
    <str name="hostContext">${hostContext:solr}</str>
    <int name="zkClientTimeout">${zkClientTimeout:30000}</int>
    <bool name="genericCoreNodeNames">${genericCoreNodeNames:true}</bool>
  </solrcloud>

  <shardHandlerFactory name="shardHandlerFactory"
    class="HttpShardHandlerFactory">
    <int name="socketTimeout">${socketTimeout:0}</int>
    <int name="connTimeout">${connTimeout:0}</int>
  </shardHandlerFactory>

</solr> -->

<!-- 多core的配置 -->
<solr persistent="true" sharedLib="lib">
  
  <property name="snapshooter" value="/home/solr-user/solr/bin/snapshooter.sh" />

  <cores adminPath="/admin/cores" host="${host:}" hostPort="${jetty.port:8080}" hostContext="${hostContext:solr}">
    <!-- name：code的名字，instanceDir：core对应的目录相对于主目录的路径 -->
    <core name="collection1" instanceDir="collection1">
      <!-- confDir：配置文件的目录路径，即conf目录，默认是core下的conf目录 -->
      <!-- <property name="confDir" value="E:\apache-tomcat-6.0.43\solr\collection1\conf" /> -->
      <!-- dataDir：数据文件的目录路径，即data目录，默认是core下的data目录 -->
      <!-- <property name="dataDir" value="E:\apache-tomcat-6.0.43\solr\collection1\data" /> -->
    </core>
    <core name="core0" instanceDir="core0">
      <!-- <property name="dataDir" value="E:\apache-tomcat-6.0.43\solr\core0\data" /> -->
    </core>
    <core name="core1" instanceDir="core1">
      <!-- <property name="dataDir" value="E:\apache-tomcat-6.0.43\solr\core1\data" /> -->
    </core>
  </cores>
  
  <shardHandlerFactory name="shardHandlerFactory" class="HttpShardHandlerFactory">
    <str name="urlScheme">${urlScheme:}</str>
  </shardHandlerFactory>
         
</solr>
```
- 重启Tomcat，打开Solr管理界面，就可以看到有3个core可供选择了

##使用Dataimport模块从mysql导入数据

- 在E:\apache-tomcat-6.0.43\solr\collection1\conf\solrconfig.xml中添加以下内容，增加导入数据功能
```
  <requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">   
    <lst name="defaults">   
      <str name="config">data-config.xml</str>   
    </lst>   
  </requestHandler>
```
- 在同一目录下添加数据源配置文件：data-config.xml，内容如下：
```
<?xml version="1.0" encoding="UTF-8" ?>
<dataConfig>
 
  <dataSource type="JdbcDataSource"

   driver="com.mysql.jdbc.Driver"
 
   url="jdbc:mysql://172.0.0.1:3306/testdb"
 
   user="root"
 
   password=""/>
 
    <document name="content">
 
        <!-- 
        entity的内容来自于“query”查询得到的结果
        field对应查询出的字段信息，“column”对应数据库字段名，“name”必须对应“schema.xml”中配置的field值 
        -->
        <entity name="member" query="select * from t_pf_member_details">
            <field column="id" name="id" />
            <field column="uid" name="uid" />
            <field column="email" name="email" />
            <field column="address" name="address" />
        </entity>
 
    </document>

</dataConfig>
```
- 修改schema.xml配置，添加相关字段的定义
```
<field name="uid" type="text_general" indexed="true" stored="true"/>
<field name="email" type="text_general" indexed="true" stored="true"/>
<field name="address" type="text_ik" indexed="true" stored="true"/>
```

- 导入相关jar包

因为使用mysql作为数据源，所以需要驱动包mysql-connector.jar；另外，使用dataimport功能还需要solr-dataimporthandler-4.7.2.jar和solr-dataimporthandler-extras-4.7.2.jar，这两个jar包不需要下载，在\dist目录下
复制这三个jar包到tomcat下的solr工程下的lib目录下（webapps\solr\WEB-INF\lib）


- 重启Tomcat，打开Solr管理界面的Dataimport功能，选择full-import全量导入，执行就行了

#Lucene/Solr常用资源

Solr官方wiki

[http://wiki.apache.org/solr/](http://wiki.apache.org/solr/)

Solrcloud官方wiki

[https://cwiki.apache.org/confluence/display/solr/SolrCloud](https://cwiki.apache.org/confluence/display/solr/SolrCloud)

Solr最新版本下载地址

[http://www.apache.org/dyn/closer.cgi/lucene/solr/](http://www.apache.org/dyn/closer.cgi/lucene/solr/)

Solr/Lucene历史版本下载地址

[http://archive.apache.org/dist/lucene/solr/](http://archive.apache.org/dist/lucene/solr/)

Solr使用手册下载地址

[http://archive.apache.org/dist/lucene/solr/ref-guide/](http://archive.apache.org/dist/lucene/solr/ref-guide/)

##分词器

mmseg4j

[https://code.google.com/p/mmseg4j/](https://code.google.com/p/mmseg4j/)

paoding

[http://code.google.com/p/paoding/](http://code.google.com/p/paoding/)

ictclas4j

[http://code.google.com/p/ictclas4j/](http://code.google.com/p/ictclas4j/)

ik-analyzer

[https://code.google.com/p/ik-analyzer/](https://code.google.com/p/ik-analyzer/)

ansj

[https://github.com/ansjsun/ansj_seg](https://github.com/ansjsun/ansj_seg)

