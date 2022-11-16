---
title: Hadoop-Running a cluster-Configurate a cluster (Core, HDFS, MapReduce and YARN)
date: 2021-06-22 12:37:00
tags: ["core-site.xml","hdfs-site.xml","yarn-site.xml","mapred-site.xml"]
series: ["BDC","Hadoop"]
featured: false
---

3 Virtual machines were prepared already. JDK, Hadoop have installed and environmental variables have configurated on them. If I want to use `HDFS`, `MapReduce` and `YARN`, I need to configurate them appropriate.

<!--more-->

ResourceManager highly requires using of memory. So, do not install NameNode, SecondaryNameNode and ResourceManager in a server.

>NameNode and SecondaryNameNode should not install in a server. Otherwise, the design of SecondaryNameNode is failed.

||hadoop102|hadoop103|hadoop104|
|---|---|---|---|
|HDFS|NameNode, DataNode|DataNode|SecondaryNameNode, DataNode|
|YARN|NodeManager|ResourceManager, NodeManager|NodeManager|

Expand this table in another way.

||hadoop102|hadoop103|hadoop104|
|---|---|---|---|
|HDFS, YARN|NameNode|ResourceManager|SecondaryNameNode|
|HDFS|DataNode|DataNode|DataNode|
|YARN|NodeManager|NodeManager|NodeManager|

## configuration files
There are 2 kinds of configuration files on : default and custom-edited.

### Default configuration files
|deault fname|pdir on Hadoop env|
|---|---|
|core-deault.xml|hadoop-common-3.1.3.jar/core-default.xml|
|hdfs-deault.xml|hadoop-hdfs-3.1.3.jar/hdfs-default.xml|
|yarn-deault.xml|hadoop-yarn-common-3.1.3.jar/yarn-default.xml|
|mapred-deault.xml|hadoop-mapreduce-client-core-3.1.3.jar/mapred-default.xml|

### Custom-edited configuration files
core-site.xml、hdfs-site.xml、yarn-site.xml、mapred-site.xml are stored in `$HADOOP_HOME/etc/hadoop`.

## configurate a cluster

### core-site.xml
```sh
[jihang@hadoop102 ~]$ cd $HADOOP_HOME/etc/hadoop
[jihang@hadoop102 hadoop]$ vim core-site.xml
```
edit content:
```sh
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<!-- 指定NameNode的地址 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop102:9820</value>
</property>
<!-- 指定hadoop数据的存储目录 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/module/hadoop-3.1.3/data</value>
</property>

<!-- 配置HDFS网页登录使用的静态用户为jihang -->
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>jihang</value>
</property>

<!-- 配置该jihang(superUser)允许通过代理访问的主机节点 -->
    <property>
        <name>hadoop.proxyuser.jihang.hosts</name>
        <value>*</value>
</property>
<!-- 配置该jihang(superUser)允许通过代理用户所属组 -->
    <property>
        <name>hadoop.proxyuser.jihang.groups</name>
        <value>*</value>
</property>
<!-- 配置该jihang(superUser)允许通过代理的用户-->
    <property>
        <name>hadoop.proxyuser.jihang.groups</name>
        <value>*</value>
</property>

</configuration>
```

### hdfs-site.xml
```sh
[jihang@hadoop102 hadoop]$ vim hdfs-site.xml
```

edit content:
```sh
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<!-- nn web端访问地址-->
	<property>
        <name>dfs.namenode.http-address</name>
        <value>hadoop102:9870</value>
    </property>
	<!-- 2nn web端访问地址-->
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop104:9868</value>
    </property>
</configuration>
```

### yarn-site.xml
```sh
[jihang@hadoop102 hadoop]$ vim yarn-site.xml
```

edit content:
```sh
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<!-- 指定MR走shuffle -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
</property>
<!-- 指定ResourceManager的地址-->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop103</value>
</property>
<!-- 环境变量的继承 -->
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
<!-- yarn容器允许分配的最大最小内存 -->
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>512</value>
    </property>
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>4096</value>
</property>
<!-- yarn容器允许管理的物理内存大小 -->
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>4096</value>
</property>
<!-- 关闭yarn对物理内存和虚拟内存的限制检查 -->
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
    </property>
</configuration>
```

### mapred-site.xml
```sh
[jihang@hadoop102 hadoop]$ vim mapred-site.xml
```

edit content
```sh
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<!-- 指定MapReduce程序运行在Yarn上 -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

#### xsync configurated files on the cluster
```sh
[jihang@hadoop102 hadoop]$ xsync /opt/module/hadoop-3.1.3/etc/hadoop/
```
check if configurated files on hadoop102 were cpoied to hadoop103 and hadoop104.

```sh
[jihang@hadoop103 ~]$ cat /opt/module/hadoop-3.1.3/etc/hadoop/core-site.xml
[jihang@hadoop104 ~]$ cat /opt/module/hadoop-3.1.3/etc/hadoop/core-site.xml
```