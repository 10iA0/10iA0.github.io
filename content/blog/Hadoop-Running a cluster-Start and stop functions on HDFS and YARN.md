---
title: Hadoop-Running a cluster-Start and stop functions on HDFS and YARN
date: 2021-06-22 18:55:00
tags: ["HDFS","YARN"]
series: ["BDC","Hadoop"]
featured: false
---

<!--more-->

## Start HDFS

`Attention!` The NameNode should be formatted only and only if the HDFS is firstlly started. 

> Once the HDFS was started and NameNode need to be formatted again, a new cluster id would be created. Then the new cluster id on NameNode and old cluster id on DataNode would be different so that it would report an erro. Hence, if the NameNode need be formatted again, the processing of NameNode and DataNode should be stopped before. Besides, the dictionary `data` and `logs` should be deleted before.

```sh
[jihang@hadoop102 ~]$ hdfs namenode -format
```

Start NameNode on hadoop102
```sh
[jihang@hadoop102 hadoop-3.1.3]$ hdfs --daemon start namenode
```

Start DataNode on hadoop102 hadoop103 hadoop104
```sh
[jihang@hadoop102 hadoop-3.1.3]$ hdfs --daemon start datanode
```
```sh
[jihang@hadoop103 hadoop-3.1.3]$ hdfs --daemon start datanode
```
```sh
[jihang@hadoop104 hadoop-3.1.3]$ hdfs --daemon start datanode
```

Start SecondaryNameNode on hadoop104
```sh
[jihang@hadoop104 hadoop-3.1.3]$ hdfs --daemon start secondarynamenode
```

## Stop HDFS
Replace `stop` with `start` on commands above.

## Start YARN

Start ResourceManager on hadoop103
```sh
[jihang@hadoop103 hadoop-3.1.3]$ yarn --daemon start resourcemanager
```

Start NodeManager on hadoop102 hadoop103 hadoop104
```sh
[jihang@hadoop102 hadoop-3.1.3]$ hdfs --daemon start nodemanager
```
```sh
[jihang@hadoop102 hadoop-3.1.3]$ hdfs --daemon start nodemanager
```
```sh
[jihang@hadoop102 hadoop-3.1.3]$ hdfs --daemon start nodemanager
```

## Stop YARN

Replace `stop` with `start` on commands above.

