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


#Tomcat上部署SolrCloud

安装三个node的SolrCloud集群，配置3个zookeeper样例

SolrCloud 服务器： s1.solr.com,s2.solr.com,s3.solr.com
Zookeeper 服务器： z1.com,z2.com,z3.com
默认安装目录 /home/myuser

首先启动zookeeper集群

按照顺序启动 z1.com,z2.com,z3.com

1.从 http://hadoop.apache.org/zookeeper/releases.html 下载zookeeper
2.解压缩zookeeper到zookeeper目录 tar -xf -C /home/myuser/zookeeper/
3.编辑zookeeper配置文件
vi /home/myuser/zookeeper/conf/zoo.cfg and put something similar to
# The number of milliseconds of each tick tickTime=2000

# The number of ticks that the initial synchronization phase can take initLimit=10

# The number of ticks that can pass between # sending a request and getting an acknowledgement syncLimit=5

# the directory where the snapshot is stored. # Choose appropriately for your environment dataDir=/home/myuser/zookeeper/data/

# the port at which the clients will connect clientPort=2181

# the directory where transaction log is stored. # this parameter provides dedicated log device for ZooKeeper dataLogDir=/home/myuser/zookeeper/log/

# ZooKeeper server and its port no. # ZooKeeper ensemble should know about every other machine in the ensemble # specify server id by creating ‘myid’ file in the dataDir # use hostname instead of IP address for convenient maintenance server.1=z1.com:2888:3888 server.2=z2.com:2888:3888 server.3=z3.com:2888:3888

4. 为每个服务器建立一个id文件到data目录，配置合适的id到文件中，例如
vi /home/myuser/zookeeper/data/myid and write just 1 for z1.com in the file. For z2.com server write 2 in its myid and 3 in z3.com myid file. myid file is used to identify the zookeeper server.

启动所有zookeeper服务器

# cd /home/myuser/zookeeper/bin/
# ./zkServer.sh start
安装tomcat

Now once zookeeper cluster is ready lets setup Solr Cloud machines : s1.solr.com,s2.solr.com,s3.solr.com

现在zookeeper集群已经准备好了，我们接下来安装SolrCloud服务器：s1.solr.com,s2.solr.com,s3.solr.com

首先从一台solr服务器上加载配置文件

1.把solr.war放到s1.solr.com服务器
2.拷贝solr.war到solr-war目录
cp solr.war /home/myuser/solr-war
3.cd /home/myuser/solr-war
4.jar xvf solr.war
5.cp WEB-INF/lib/* /home/myuser/solr-war-lib/
6.吧所有配置文件例如solrconfig.xml,schema.xml 等等放到 /home/myuser/solr-config/
7.用solr zookeeper cli(zookeeper客户端)上传配置文件到zookeeper
java -classpath .:/home/myuser/solr-war-lib/* org.apache.solr.cloud.ZkCLI -cmd upconfig -zkhost z1.com:2181,z2.com:2181,z3.com:2181 -confdir /home/myuser/solr-config -confname myconf
8.链接上传的配置和collection（假设新的collection名字叫mycollection)
9.建立一个目录用于存储solr索引 /home/myuser/solr-cores/
10.在solr-cores里面建立solr.xml文件内容如下
<?xml version=”1.0″ encoding=”UTF-8″ ?> <solr persistent=”true” sharedLib=”lib”> <cores adminPath=”/admin/cores” zkClientTimeout=”20000″ hostPort=”8080″ hostContext=”solr”> </cores> </solr>
11.用下面的jvm参数启动tomcat
-DzkHost=z1.com:2181,z2.com:2181,z3.com:2181
12.把solr.war 放到 /home/myuser/tomcat/webapps/ 然后重启tomcat
13.编辑 /home/myuser/tomcat/webapps/solr/META-INF/context.xml 文件并且重启tomcat
<?xml version=”1.0″ encoding=”utf-8″?> <Context docBase=”solr.war” debug=”0″ crossContext=”false”> <Environment name=”solr/home” type=”java.lang.String” value=”/home/myuser/solr-cores” override=”true”/> </Context>
14.curl ‘http://s1.solr.com:8080/solr/admin/collections?action=CREATE&name=mycollection&numShards=3&replicationFactor=1′

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

