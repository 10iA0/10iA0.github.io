---
title: Hadoop-Prerequisites-Prepare a server as blueprint 
date: 2021-06-15 13:38:00
tags: ["epel-release", "psmisc", "nc", "net-tools", "rsync", "vim", "lrzsz", "ntp", "libzstd", "openssl-static", "tree", "iotop", "git","firewall"]
series: ["BDC","Hadoop"]
featured: false
---

Setting up the development environment for big data computing.

<!--more-->

## Set up the development environment

### Prepare a blueprint

Prepare a development enviroment on virtual machine as  a blueprint for further learning Hadoop. The buleprint is named as `hadoop100` and configurated as:
- DRAM: 4G
- HDD: 50G

#### Install essential tools

Install essential tools from yum repos for this operation system.
```sh
# Test if the Internet is connected or not.
[root@hadoop100 ~]$ ping www.google.com

[root@hadoop100 ~]$ yum install -y epel-release
[root@hadoop100 ~]$ yum install -y psmisc nc net-tools rsync vim lrzsz ntp libzstd openssl-static tree iotop git
```

#### Stop and disable the firewall
Turn off the firewall and disable its auto-start.
```sh
[root@hadoop100 ~]$ systemctl stop firewalld
[root@hadoop100 ~]$ systemctl disable firewalld
```

#### Create and authorize users
Create a user `jihang` for myself and change the password.
```sh
[root@hadoop100 ~]$ useradd jihang
[root@hadoop100 ~]$ passwd jihang
```
Authorize user `jihang` as `root` to exectute `sudo` commands.
```sh
[root@hadoop100 ~]$ vim /etc/sudoers
```
Edit the file `/etc/sudoers`, add a line on it as below:
```sh
## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
jihang   ALL=(ALL)     NOPASSWD:ALL
```
#### Create and authorize folders

Make folders `module` and `software` on `/opt`
```sh
[root@hadoop100 ~]$ mkdir /opt/module
[root@hadoop100 ~]$ mkdir /opt/software
```

Edit folders `module` and `software`'s owner and group from `root` to `jihang` 
```sh
[root@hadoop100 ~]$ chown jihang:jihang /opt/module 
[root@hadoop100 ~]$ chown jihang:jihang /opt/software
```

Check folders `module`, `software`'s owner and group
```sh
[root@hadoop100 ~]$ cd /opt/
[root@hadoop100 opt]$ ll
总用量 12
drwxr-xr-x. 2 jihang jihang 4096 5月  28 17:18 module
drwxr-xr-x. 2 root    root    4096 9月   7 2017 rh
drwxr-xr-x. 2 jihang jihang 4096 5月  28 17:18 software
```

#### Uninstall the defalut `open JDK` on system
```sh
[root@hadoop100 ~]$ rpm -qa | grep -i java | xargs -n1 rpm -e --nodeps
```

#### Reboot the server
```sh
[root@hadoop100 ~]$ reboot
```



### 3. Install JDK and Hadoop on cloned system

Take `hadoop102` as an example below

#### 3.1 Install JDK

Uninstall default JDK
```sh
[jihang@hadoop102 ~]$ rpm -qa | grep -i java | xargs -n1 sudo rpm -e --nodeps
```

I have downloaded tar `jdk` on `opt/software/`
```sh
[jihang@hadoop102 ~]$ ls /opt/software/
hadoop-3.1.3.tar.gz  jdk-8u212-linux-x64.tar.gz
```

Unzip JDK to `/opt/module`
```sh
[jihang@hadoop102 software]$ tar -zxvf jdk-8u212-linux-x64.tar.gz -C /opt/module/
```

Configurate JDK environmental variable
```sh
[jihang@hadoop102 ~]$ sudo vim /etc/profile
```
add as below
```sh
#JAVA_HOME
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin
```
save and quit
```sh
:wq
```

Refresh file `/etc/profile`, acitivate new environmental variable `PATH`
```sh
[jihang@hadoop102 ~]$ source /etc/profile
```
Check if JDK is installed well

```sh
[jihang@hadoop102 ~]$ java -version
java version "1.8.0_212"
```

#### 3.2 Install Hadoop

I have downloaded tar `hadoop` on `opt/software/`
```sh
[jihang@hadoop102 ~]$ ls /opt/software/
hadoop-3.1.3.tar.gz  jdk-8u212-linux-x64.tar.gz
```

Unzip Hadoop to `/opt/module`
```sh
[jihang@hadoop102 software]$ tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module/
```

Check if Hadoop is unzip well
```sh
[jihang@hadoop102 software]$ ls /opt/module/
hadoop-3.1.3
```

Print present working dictionary
```sh
[jihang@hadoop102 hadoop-3.1.3]$ pwd
/opt/module/hadoop-3.1.3
```

Add Hadoop to environmental variable
```sh
sudo vim /etc/profile
```

add this content
```sh
#HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

save and quit
```sh
:wq
```

Refresh file /etc/profile, acitivate new environmental variable `PATH`
```sh
[jihang@hadoop102 hadoop-3.1.3]$ source /etc/profile
```

Check if Hadoop is installed well
```sh
[jihang@hadoop102 hadoop-3.1.3]$ hadoop version
Hadoop 3.1.3
```

### 4. Introduction of Hadoop's filesystem hierarchy
Check Hadoop' FHS
```sh
[jihang@hadoop102 hadoop-3.1.3]$ ll
总用量 52
drwxr-xr-x. 2 jihang jihang  4096 5月  22 2017 bin
drwxr-xr-x. 3 jihang jihang  4096 5月  22 2017 etc
drwxr-xr-x. 2 jihang jihang  4096 5月  22 2017 include
drwxr-xr-x. 3 jihang jihang  4096 5月  22 2017 lib
drwxr-xr-x. 2 jihang jihang  4096 5月  22 2017 libexec
-rw-r--r--. 1 jihang jihang 15429 5月  22 2017 LICENSE.txt
-rw-r--r--. 1 jihang jihang   101 5月  22 2017 NOTICE.txt
-rw-r--r--. 1 jihang jihang  1366 5月  22 2017 README.txt
drwxr-xr-x. 2 jihang jihang  4096 5月  22 2017 sbin
drwxr-xr-x. 4 jihang jihang  4096 5月  22 2017 share
```
Key files
- bin: store scripts running Hadoop's service, like HDFS, YARN
- etc: store configuration files on Hadoop
- lib: store local library on Hadoop, like zip and unzip data
- sbin: store scripts to start or stop Hadoop's service
- share: store Hadoop's dependent jar packages, documentations, and official examples