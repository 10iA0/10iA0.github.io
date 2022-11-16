---
title: Hadoop-Running a cluster-Start and stop HDFS and YARN automatically
date: 2021-06-23 22:08:00
tags: ["ssh","HDFS","YARN"]
series: ["BDC","Hadoop"]
featured: false
---

3 virtual machines were prepated, and JDK, Hadoop have installed and environmental variables have configurated on them. When I do big data computing, the servers shoud be communicated with each other. If I use `ssh` directelly, it will report `Host key verification failed`. So, I need make ssh login without password. Futhermore, I would like to start and stop cluster manually. So, I need write a script that could start and stop cluster automatically.

<!--more-->

## Configurate SSH

I can login other sever by `ssh [other server's IP]`:
```sh
[jihang@hadoop102 ~]$ ssh hadoop103
```
However, there would report an erro: `host key verification failed`.

```sh
The authenticity of host '192.168.1.103 (192.168.1.103)' can't be established.
RSA key fingerprint is cf:1e:de:d7:d0:4c:2d:98:60:b4:fd:ae:b1:2d:ad:06.
Are you sure you want to continue connecting (yes/no)? 
```

Though this connection could be established by printing `yes`, I want to configure ssh to login without password.

### Create id_rsa and id_rsa.pub
```sh
[jihang@hadoop102 .ssh]$ ssh-keygen -t rsa
```
Explanation of files about SSH:
|fnames|explanation|
|---|---|
|id_rsa|密钥|
|id_rsa.pub|公钥|
|known_hosts|访问其它服务器，获得它们的公钥|
|authorized_keys|其它服务器访问，记录它们的公钥|

### Copy id_rsa.pub to servers
#### Hadoop102
```sh
[jihang@hadoop102 .ssh]$ ssh-copy-id hadoop103
[jihang@hadoop102 .ssh]$ ssh-copy-id hadoop104
```

#### Hadoop103
```sh
[jihang@hadoop103 .ssh]$ ssh-copy-id hadoop102
[jihang@hadoop103 .ssh]$ ssh-copy-id hadoop104
```

#### Hadoop104
```sh
[jihang@hadoop104 .ssh]$ ssh-copy-id hadoop102
[jihang@hadoop104 .ssh]$ ssh-copy-id hadoop103
```

## Configure workers
When the start and stop shell scripts were ran, the file `etc/hadoop/workers` will be analyzed. The content on this files are address of all servers. These scripts would run HDFS and YARN automatically on servers.

```sh
[jihang@hadoop102 hadoop]$ vim /opt/module/hadoop-3.1.3/etc/hadoop/workers
```

edit content (do not leave space on content):
```sh
hadoop102
hadoop103
hadoop104
```

Sync `workers`.
```sh
[jihang@hadoop102 hadoop]$ xsync /opt/module/hadoop-3.1.3/etc/hadoop/
```

## Operate HDFS and YARN

`Attention!` The NameNode should be formatted only and only if the HDFS is firstlly started. 

> Once the HDFS was started and NameNode need to be formatted again, a new cluster id would be created. Then the new cluster id on NameNode and old cluster id on DataNode would be different so that it would report an erro. Hence, if the NameNode need be formatted again, the processing of NameNode and DataNode should be stopped before. Besides, the dictionary `data` and `logs` should be deleted before.

### Start and stop HDFS
start HDFS.
```sh
[jihang@hadoop102 hadoop-3.1.3]$ sbin/start-dfs.sh
```
Enter http://hadoop102:9870 on web to check storage information on HDFS.

stop HDFS.
```sh
[jihang@hadoop102 hadoop-3.1.3]$ sbin/stop-dfs.sh
```

### Start and stop YARN
Start YARN.
```sh
[jihang@hadoop103 hadoop-3.1.3]$ sbin/start-yarn.sh
```
Enter http://hadoop103:8088 on web to check Jobs on YARN.

stop Yarn.
```sh
[jihang@hadoop103 hadoop-3.1.3]$ sbin/stop-yarn.sh
```

## Write and `xsync` shell scripts
### jps_cluster.sh
Write a script that check Java's processings on servers of a cluster: `jps_cluster.sh`
```sh
[jihang@hadoop102 ~]$ cd /home/jihang/bin
[jihang@hadoop102 ~]$ touch jps_cluster.sh
[jihang@hadoop102 ~]$ sudo vim jps_cluster.sh
```

add content:
```sh
#!/bin/bash
for host in hadoop102 hadoop103 hadoop104
do
        echo =============== $host ===============
        ssh $host jps $@ | grep -v Jps
done
```

Authorize `jps_cluster.sh`.
```sh
[jihang@hadoop102 bin]$ chmod +x jps_cluster
```

Sync shell scripts.
```sh
[jihang@hadoop102 ~]$ xsync /home/jihang/bin/
```

### hadoop_cluster.sh
Write a script that start and stop HDFS, YARN and historyserver on a cluster: `hadoop_cluster.sh`.
```sh
[jihang@hadoop102 ~]$ cd /home/jihang/bin
[jihang@hadoop102 ~]$ touch hadoop_cluster.sh
[jihang@hadoop102 ~]$ sudo vim hadoop_cluster.sh
```

add content:

```sh
#!/bin/bash
if [ $# -lt 1 ]
then
    echo "No Args Input..."
    exit ;
fi
case $1 in
"start")
        echo " =================== 启动 hadoop集群 ==================="

        echo " --------------- 启动 hdfs ---------------"
        ssh hadoop102 "/opt/module/hadoop-3.1.3/sbin/start-dfs.sh"
        echo " --------------- 启动 yarn ---------------"
        ssh hadoop103 "/opt/module/hadoop-3.1.3/sbin/start-yarn.sh"
        echo " --------------- 启动 historyserver ---------------"
        ssh hadoop102 "/opt/module/hadoop-3.1.3/bin/mapred --daemon start historyserver"
;;
"stop")
        echo " =================== 关闭 hadoop集群 ==================="

        echo " --------------- 关闭 historyserver ---------------"
        ssh hadoop102 "/opt/module/hadoop-3.1.3/bin/mapred --daemon stop historyserver"
        echo " --------------- 关闭 yarn ---------------"
        ssh hadoop103 "/opt/module/hadoop-3.1.3/sbin/stop-yarn.sh"
        echo " --------------- 关闭 hdfs ---------------"
        ssh hadoop102 "/opt/module/hadoop-3.1.3/sbin/stop-dfs.sh"
;;
*)
    echo "Input Args Error..."
;;
esac
```

Authorize myhdoop.sh.
```sh
[jihang@hadoop102 bin]$ chmod +x hadoop_cluster.sh
```

Sync shell scripts.
```sh
[jihang@hadoop102 ~]$ xsync /home/jihang/bin/
```

## Run example `wordcount`   
Run the official example `wordcount` on cluster.
```sh
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount file:///wcinput file:///wcoutput
```

`/` in `file:///wcinput` and `file:///wcoutput` means root path in HDFS, which is configurated by `core-site.xml`:
```sh
<!-- 指定NameNode的地址 -->
<property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop102:9820</value>
</property>
```
`file:///` equals `hdfs://hadoop102:9820/`. So, The command below is equivalent to command above.

```sh
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount hdfs://hadoop102:9820/wcinput hdfs://hadoop102:9820/wcoutput
```
