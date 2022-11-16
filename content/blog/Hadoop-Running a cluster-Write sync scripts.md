---
title: Hadoop-Running a cluster-Write sync scripts
date: 2021-06-21 02:18:00
tags: ["scp","rsync","xsync"]
series: ["BDC","Hadoop"]
featured: false
---

I have prepared 3 virtual machines already in which JDK, Hadoop have installed and environmental variables have configurated. When I do big data computing in a cluster, I need to copy files in a loop from a server to other servers. On the other hand, the existed shell comands are not perfectlly satisfied with my demand. So, I need wite a script `xync` with shell.

<!--more-->

## scp (secure copy)
copy files from a server to another server.

### Basic grammaring

|command|parameter|dir and fname|destinated dir and fname|
|---|---|---|---|
|scp|-r|$pdir/$fname|$user@hadoop$host:$pdir/$fname
|命令|递归|要拷贝的文件路径/名称|目的用户@主机:目的路径/名称

### Three examples
Prerequisites: make sure `/opt/module` and `/opt/software` are in `hadoop102`, `hadoop103` and `hadoop104`, and own:group are `jihang:jihang`

```sh
sudo chown jihang:jihang -R /opt/module
```

1. On hadoop102, copy /opt/module/jdk1.8.0_212 from hadoop102 to hadoop103
```sh
[jihang@hadoop102 ~]$ scp -r /opt/module/jdk1.8.0_212  jihang@hadoop103:/opt/module
```

2. On hadoop103, copy /opt/module/hadoop-3.1.3 from hadoop102 to hadoop103
```sh
[jihang@hadoop103 ~]$ scp -r jihang@hadoop102:/opt/module/hadoop-3.1.3 /opt/module/
```

3. On hadoop103, copy all files on /opt/module from hadoop102 to hadoop104
```sh
[jihang@hadoop103 opt]$ scp -r jihang@hadoop102:/opt/module/* jihang@hadoop104:/opt/module
```

## rsync

Difference between `rsync` and `scp`: rsync is faster and only update edited files.

### Basic grammaring

|command|parameter|dir and fname|destinated dir and fname|
|---|---|---|---|
|rsync|-av|$pdir/$fname|$user@hadoop$host:$pdir/$fname
|命令|参数|要拷贝的文件路径/名称|目的用户@主机:目的路径/名称

|parameter|explanation|
|---|---|
|-a|copy|
|-v|dispaly processing|

### One example
1. copy all files on /opt/software from hadoop102 to hadoop103
```sh
[jihang@hadoop102 opt]$ rsync -av /opt/software/* jihang@hadoop103:/opt/software
```

## xsync
There is no existed demand called `xsync`. I use a shell script to achieve my demand on copying files form a server to other servers. Why call this script xsync? Cause I think digit x is cool :)

### demands
copy files from a server to other servers on a loop as I wish.

1. use `rsync` to copy files：
```sh
rsync -av /opt/module root@hadoop103:/opt/
```
2. use files I want to copy as argument(s)
```sh
xsync [pdir/fname1] [pdir/fname2]
```
3. store script on /home/jihang/bin so that I, the user `jihang`, could run `xsync` on system everywhere.

### Write script
1. create a file `xsync` on /home/jihang/bin
```sh
[jihang@hadoop102 opt]$ cd /home/jihang
[jihang@hadoop102 ~]$ mkdir bin
[jihang@hadoop102 ~]$ cd bin
[jihang@hadoop102 bin]$ vim xsync
```
and add content below
```sh
#!/bin/bash
#1. 判断参数个数
if [ $# -lt 1 ]
then
  echo Not Enough Arguement!
  exit;
fi
#2. 遍历集群所有机器
for host in hadoop102 hadoop103 hadoop104
do
  echo ====================  $host  ====================
  #3. 遍历所有目录，挨个发送
  for file in $@
  do
    #4. 判断文件是否存在
    if [ -e $file ]
    then
      #5. 获取父目录
      pdir=$(cd -P $(dirname $file); pwd)
      #6. 获取当前文件的名称
      fname=$(basename $file)
      ssh $host "mkdir -p $pdir"
      rsync -av $pdir/$fname $host:$pdir
    else
      echo $file does not exists!
    fi
  done
done
```

2. authorize xsync
```sh
[jihang@hadoop102 bin]$ chmod +x xsync
```

3. move script to /bin
```sh
[jihang@hadoop102 bin]$ sudo cp xsync /bin/
```

4. test
```sh
[jihang@hadoop102 ~]$ xsync /home/jihang/bin
[jihang@hadoop102 bin]$ sudo xsync /bin/xsync
```
