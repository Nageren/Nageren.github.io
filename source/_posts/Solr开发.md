---
title: Solr开发
date: 2017-12-15 22:27:08
tags: [solr,solr集群]
---

Solr单机版的使用
===

在虚拟机上模拟solr单机版和集群的使用,和实际开发其实是差不多的

准备tomcat  solr的安装包

1.安装单机版的solr
---

 mkdir  singleSolr
 在singleSolr目录下 
 导入apache-tomcat-7.0.52  解压   重命名为tomcat-solr
 导入solr-4.10.3.tgz.tgz  tar zxvf  solr-4.10.3.tgz.tgz  

2.部署solr
---

```xml
把war包放入 tomcat  webapp
cp solr-4.10.3/example/webapps/solr.war  tomcat-solr/webapps/
```
3.导入依赖jar包
---

```xml
cd solr-4.10.3/example/lib/ext/
把ext下的所有jar包  复制到tomcat-solr/webapps/solr/WEB-INF/lib/
复制到cp -r solr-4.10.3/example/lib/ext/*  tomcat-solr/webapps/solr/WEB-INF/lib/ 
```
4.拷贝solr-4.10.3/example/solr到  singleSolr目录下
---

这一步是配置仓库的路径


	cp -r solr-4.10.3/example/solr   .    注意 . 代表当前目录
	windows下配置 catalina.bat  set JAVA_OPTS=-"Dsolr.solr.home=E:\solr"
	Linux下配置   catalina.sh   export JAVA_OPTS ="-Dsolr.solr.home=/usr/local/hadoop/singleSolr/solr/"

5.依赖类库
---

```xml
 cd solr-4.10.3/   
 拷贝solr-4.10./3 下的  contrib   contrib
 到4中的索引库目录下
 命令: cd  singleSolr/solr
	  cp  -r  ../solr-4.10.3/contrib/  ../solr-4.10.3/dist/  .    
	   不要忘记. 当前目录(singleSolr/solr)  ---> 索引库的目录
```
6.修改索引库下的配置(singleSolr/solr)
---

```xml
 mv collection1 item   --把索引库名字改成item
 cd  item/
 vim  core.properties   把name=collection1改为name=item
 这样索引库的名字才是修改成功了
 
 再修改配置文件
 cd  item/conf
 修改 item/conf下的   vim solrconfig.xml 
```
  修改引入jar包的位置 

  ```xml
  <lib dir="${solr.install.dir:..}/contrib/extraction/lib" regex=".*\.jar" />
  <lib dir="${solr.install.dir:..}/dist/" regex="solr-cell-\d.*\.jar" />

  <lib dir="${solr.install.dir:..}/contrib/clustering/lib/" regex=".*\.jar" />
  <lib dir="${solr.install.dir:..}/dist/" regex="solr-clustering-\d.*\.jar" />

  <lib dir="${solr.install.dir:..}/contrib/langid/lib/" regex=".*\.jar" />
  <lib dir="${solr.install.dir:..}/dist/" regex="solr-langid-\d.*\.jar" />

  <lib dir="${solr.install.dir:..}/contrib/velocity/lib" regex=".*\.jar" />
  <lib dir="${solr.install.dir:..}/dist/" regex="solr-velocity-\d.*\.jar" />
  ```

	这一步做个说明  这个是solr的索引库的位置
	export JAVA_OPTS ="-Dsolr.solr.home=/usr/local/hadoop/singleSolr/solr/"

7.启动solr服务  查看是否配置成功
---

```xml
 sh  tomcat-solr/bin/startup.sh       		-- 启动sol服务
 tail -f  tomcat-solr/logs/catalina.out 	-- 查看进程
```

8.安装ak分词器
---

```xml
首先需要在项目下导入jar包
cd  /usr/local/hadoop/singleSolr/tomcat-solr/webapps/solr/WEB-INF/lib

导入分词器的 jar包      IKAnalyzer2012FF_u1.jar

在/usr/local/hadoop/singleSolr/tomcat-solr/webapps/solr/WEB-INF下导入分词器的配置文件

创建  classes 文件夹(如过没有的话)
ext.dic
IKAnalyzer.cfg.xml
log4j.properties
stopword.dic

 Ik分词器的环境准备好了
```

9.再去配置索引仓库
---

	/usr/local/hadoop/singleSolr/solr/item/conf  下的 schema.xml 

```xml
<!-- 配置域字段类型,测试是否配置成功 -->  
<!-- 域字段  -->
<field name="username_ik" type="text_ik" indexed="true" stored="true"/>
<!-- 配置域类型 -->
<fieldType name="text_ik" class="solr.TextField">
      <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>
</fieldType>


<!-- 配置好 -->
 sh tomcat-solr/bin/shutdown.sh
<!--  让配置生效 -->
 sh tomcat-solr/bin/startup.sh
<!--  查看进程 -->
tail -f tomcat-solr/logs/catalina.out 
```

10.要导入索引库
---

问题1:

	首先要配置索引域字段(创建数据库字段)
	应该把参与搜索的数据库数据导入索引库
	确定参与搜索字段:
		商品表:id,title,sell_point,price,image
		商品描述表:item_desc
		商品分类表:category_name
把以上三张表数据导入索引库,实现搜索业务		
问题2:配置索引域字段
	一个字段对应需求导入索引库数据库的一个字段

导入数据的过程:
	1.查询数据库(三张表)
	2.把查询数据库写入索引库

注意:查询时要注意商品的状态
```sql
SELECT
	a.id,
	a.title,
	a.sell_point,
	a.price,
	a.image,
	b.item_desc,
	c.name category_name
FROM
  	tb_item a,
  	tb_item_desc b,
 	tb_item_cat c
WHERE
	a.id = b.item_id
AND
	a.cid = c.id 
AND
	a.status = 1;
```
索引域字段的配置

```xml-dtd
<field name="item_title" type="text_ik" indexed="true" stored="true"/>
<field name="item_sell_point" type="text_ik" indexed="true" stored="true"/>
<field name="item_price" type="long" indexed="true" stored="true"/>
<field name="item_image" type="string" indexed="false" stored="true" />
<field name="item_category_name" type="string" indexed="true" stored="true" />
<field name="item_desc" type="text_ik" indexed="true" stored="false" />
<field name="item_keywords" type="text_ik" indexed="true" stored="false" multiValued="true"/>
<copyField source="item_title" dest="item_keywords"/>
<copyField source="item_sell_point" dest="item_keywords"/>
<copyField source="item_category_name" dest="item_keywords"/>
<copyField source="item_desc" dest="item_keywords"/>
```

​	
工作1:配置索引域字段---(创建数据库表字段)
问题1:应该把哪些数据导入索引库?

	应该把参与搜索的数据库数据导入索引库
	确定参与搜索字段
	商品表:id,title,sell_point,price,image
	商品描述表:item_desc
	商品分类表:category_name
	
	以上三张表数据导入索引库,实现搜索业务
问题2:配置索引域字段
	一个字段对应需求导入索引库

	导入数据过程:	
1.创建SearchItemMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<!-- 查询索引库域字段对应数据库值写入索引库 -->
<mapper namespace="cn.e3.search.mapper.SearchItemMapper">
	<select id="findDataBaseToSolrIndex" resultType="searchItem">
		SELECT
		a.id,
		a.title,
		a.sell_point,
		a.price,
		a.image,
		b.item_desc,
		c.name category_name
		FROM
		tb_item a,
		tb_item_desc b,
		tb_item_cat c
		WHERE
		a.id = b.item_id
		AND
		a.cid = c.id
		AND
		a.status = 1;
	</select>
</mapper>
```

2.查询索引域字段对应数据值写入索引库   业务代码的实现

```java
	/**
	 * 需求:查询索引域字段对应数据值写入索引库
	 * 参数:无
	 * 返回值:E3mallResult 
	 * 发布服务
	 */
	@Override
	public E3mallResult findDataBaseToSolrIndex() {
		try {
			List<SearchItem> list = searchItemMapper.findDataBaseToSolrIndex();
			//循环集合数据,把数据封装到doc文档对象,实现索引库的写入
			for (SearchItem searchItem : list) {
				//创建一个文档对象,封装索引库域字段对应的值
				SolrInputDocument doc = new SolrInputDocument();
				//封装文档域所对应的值
				doc.addField("id", searchItem.getId());
				//标题
				doc.addField("item_title", searchItem.getTitle());
				//卖点
				doc.addField("item_sell_point", searchItem.getSell_point());
				//价格
				doc.addField("item_price", searchItem.getPrice());
				//图片地址
				doc.addField("item_image", searchItem.getImage());
				//商品分类名称  item_category_name
				doc.addField("item_category_name", searchItem.getCategory_name());
				//商品描述  item_desc  
				doc.addField("item_desc", searchItem.getItem_desc());
				//写入索引库
				solrServer.add(doc);
			}
			solrServer.commit();
		} catch (Exception e) {
			e.printStackTrace();
		}
		//返回值
		return E3mallResult.ok();
	}
```



Solr集群版的使用
===

zookeeper原理:
---

概念:zookeeper是一个服务协调者

### 1.注册中心(分布式服务)

​	把对象注册到注册中心,让服务消费者和服务提供者解耦合
​	让服务消费者和服务器提供者实现异步调用

### 2.配置中心(集群服务)   本地模拟实现:  

```shell
drwxr-xr-x.  6 root root      4096 12月 11 00:39 solr1
drwxr-xr-x.  6 root root      4096 12月 11 00:39 solr2
drwxr-xr-x.  6 root root      4096 12月 11 00:39 solr3
drwxr-xr-x.  6 root root      4096 12月 11 00:39 solr4
drwxr-xr-x.  9 root root      4096 12月 11 00:36 tomcat1
drwxr-xr-x.  9 root root      4096 12月 11 00:38 tomcat2
drwxr-xr-x.  9 root root      4096 12月 11 00:38 tomcat3
drwxr-xr-x.  9 root root      4096 12月 11 00:38 tomcat4
drwxr-xr-x. 10 git  games     4096 11月  5 2012 zookeeper1
drwxr-xr-x. 10 root root      4096 12月 11 00:35 zookeeper2
drwxr-xr-x. 10 root root      4096 12月 11 00:35 zookeeper3
```

配置集群redis集群
cd zookeeper1/conf  下
mv zoo_sample.cfg  zoo.cfg

修改这三行:

```
dataDir=/usr/local/hadoop/clusterSolr/zookeeper1/data
dataLogDir=/usr/local/hadoop/clusterSolr/zookeeper1/log
clientPort=2182
```

配置集群之间通信:

```
server.1=192.168.65.150:2881:3881
server.2=192.168.65.150:2882:3882
server.3=192.168.65.150:2883:3883
```

在zookeeper1下创建data和log
在data目录下
 touch myid
  vim myid    写入:1
在zookeeper2下创建data和log
在data目录下
 touch myid
 vim myid    写入:2
在zookeeper3下创建data和log
在data目录下
 touch myid
 vim myid    写入:3
三台同样操作

### 3.配置tomcat集群

1.tomcat1
    vim tomcat1/conf/server.xml
    修给端口:  8010 9000  8011

2.指定索引库的路径
设置tomcat启动时,启动solr,加载solr索引库的位置,还有zookeer的集群服务器地址IP
vim tomcat1/bin/catalina.sh
在catalina.sh中:
	export JAVA_OPTS ="-Dsolr.solr.home=/usr/local/hadoop/clusterSolr/solr1/ -DzkHost=192.168.65.150:2182,192.1
			68.65.150:2183,192.168.65.150:2184"	
在配置tomcat1中的solr对应索引库solr1下:
vim   solr1/solr.xml
让监控端口和tomcat的启动端口一致

```xml
<int name="hostPort">${jetty.port:9000}</int>
```

重复4次上面的操作  修改相应的位置
8010 9001  8011  
8012 9002  8013
8014 9003  8015
8016 9004  8017

把 solr 集群配置文件交给 Zookeeper 注册中心管理
把仓库核心配置文件放入 Zookeeper 注册中心， 当 solr 集群需要加载配置文件，只需要从 Zookeeper 中获取配置文件。
./zkcli.sh -zkhost 192.168.65.150:2182,192.168.65.150:2183,192.168.65.150:2184 -cmd upconfig -confdir /usr/local/hadoop/clusterSolr/solr1/item/conf/ -confname myconf

登录zookeeper集群:
命令:./zkCli.sh -server 192.168.65.150:2182
solr集群分片命令：
http://192.168.65.150:9000/solr/admin/collections?action=CREATE&name=products&numShards=2&replicationFactor=2&maxShardsPerNode=8&property.schema=schema.xml&property.config=solrconfig.xml
删除旧solrCloud集群分片：
http://192.168.65.150:9000/solr/admin/collections?action=DELETE&name=item

这样的话,solr集群就搭建完成了	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 
​	 