---
title: Hadoop-Prerequisites-Preparation on servers 
date: 2021-06-15 13:38:00
tags: ["Linux minimal installation","firewall","IP","hostname","hosts"]
series: ["BDC","Hadoop"]
featured: false
---

Prepare 3 servers and set up the development environment on each servers for big data computing. I have no configurated instances on Cloud Server, like AWS, Alibaba, etc., as my student discount was expired. Fortunatly, I have a new computer which is good enough to run multiple virtual machines.

<!--more-->

## Prepare a server as blueprint

Prepare a virtual machine as a blueprint and set up the development environment on it for further learning Hadoop. The buleprint is named as `hadoop100` and has `DRAM(4G)` and `HDD(50G)`.

### Fix bugs for Linux minimal installation

The minimal installation leads some lack of essential extentions. Fortunately, these packages can be installed from yum repository.
```sh
# Test if the Internet is connected or not.
[root@hadoop100 ~]$ ping www.google.com

[root@hadoop100 ~]$ yum install -y epel-release
[root@hadoop100 ~]$ yum install -y psmisc nc net-tools rsync vim lrzsz ntp libzstd openssl-static tree iotop git
```

### Stop and disable the firewall
Turn off disable the firewall in case it automatically start.
```sh
[root@hadoop100 ~]$ systemctl stop firewalld
[root@hadoop100 ~]$ systemctl disable firewalld
```

### Create and authorize a user
Create a user `jihang` for myself and change its password.
```sh
[root@hadoop100 ~]$ useradd jihang
[root@hadoop100 ~]$ passwd jihang
```

Authorize user `jihang` as `root` to exectute `sudo` commands.
```sh
[root@hadoop100 ~]$ vim /etc/sudoers
```

Edit the file `/etc/sudoers`, and add content below on the file:
```sh
## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
jihang   ALL=(ALL)     NOPASSWD:ALL
```
### Create and authorize folders

Make folders `module` and `software` on `/opt`
```sh
[root@hadoop100 ~]$ mkdir /opt/module
[root@hadoop100 ~]$ mkdir /opt/software
```

Change folders `module` and `software`'s owner and group from `root` to `jihang` 
```sh
[root@hadoop100 ~]$ chown jihang:jihang /opt/module 
[root@hadoop100 ~]$ chown jihang:jihang /opt/software
```

```sh
[root@hadoop100 ~]$ cd /opt/
[root@hadoop100 opt]$ ll
总用量 12
drwxr-xr-x. 2 jihang jihang 4096 5月  28 17:18 module
drwxr-xr-x. 2 root    root    4096 9月   7 2017 rh
drwxr-xr-x. 2 jihang jihang 4096 5月  28 17:18 software
```

### Install JDK and Hadoop
#### Install JDK

Uninstall default JDK
```sh
[jihang@hadoop100 ~]$ rpm -qa | grep -i java | xargs -n1 sudo rpm -e --nodeps
```

The tar of `jdk` is downloaded on `opt/software/`
```sh
[jihang@hadoop100 ~]$ ls /opt/software/
hadoop-3.1.3.tar.gz  jdk-8u212-linux-x64.tar.gz
```

Unzip JDK to `/opt/module`
```sh
[jihang@hadoop100 software]$ tar -zxvf jdk-8u212-linux-x64.tar.gz -C /opt/module/
```

Configurate JDK environmental variable
```sh
[jihang@hadoop100 ~]$ sudo vim /etc/profile.d/xenv.sh
```
- add content as below:
```sh
#JAVA_HOME
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin
```
- save and quit:
```sh
:wq
```

Refresh file `/etc/profile` and check if JDK was installed well
```sh
[jihang@hadoop100 ~]$ source /etc/profile
[jihang@hadoop100 ~]$ java -version
java version "1.8.0_212"
```

#### Install Hadoop

The tar of `hadoop` is downloaded on `opt/software/`
```sh
[jihang@hadoop100 ~]$ ls /opt/software/
hadoop-3.1.3.tar.gz  jdk-8u212-linux-x64.tar.gz
```

Unzip Hadoop to `/opt/module`
```sh
[jihang@hadoop100 software]$ tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module/
```

Configurate Hadoop environmental variable
```sh
sudo vim /etc/profile
```
- add this content
```sh
#HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```
- save and quit
```sh
:wq
```

Refresh file /etc/profile and check if Hadoop is installed well
```sh
[jihang@hadoop100 hadoop-3.1.3]$ source /etc/profile
[jihang@hadoop100 hadoop-3.1.3]$ hadoop version
Hadoop 3.1.3
```

Introduce Hadoop's filesystem hierarchy

```sh
[jihang@hadoop100 hadoop-3.1.3]$ ll
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

### Edit the file `hosts`
#### Linux
```sh
[root@hadoop100 ~]$ vim /etc/hosts
```
add this content
```sh
192.168.1.100 hadoop100
192.168.1.101 hadoop101
192.168.1.102 hadoop102
192.168.1.103 hadoop103
192.168.1.104 hadoop104
192.168.1.105 hadoop105
192.168.1.106 hadoop106
192.168.1.107 hadoop107
192.168.1.108 hadoop108
```

#### Windows (win10)
```sh
code C:\Windows\System32\drivers\etc\hosts
```
add this content
```sh
192.168.1.100 hadoop100
192.168.1.101 hadoop101
192.168.1.102 hadoop102
192.168.1.103 hadoop103
192.168.1.104 hadoop104
192.168.1.105 hadoop105
192.168.1.106 hadoop106
192.168.1.107 hadoop107
192.168.1.108 hadoop108
```

## Clone 3 servers (from the blueprint)

Clone 3 servers `hadoop102`, `hadoop103`, `hadoop104` from the blueprint `hadoop100`

### Edit IP

Take `hadoop102` as an example below

Edit the static IP
```sh
[root@hadoop102 ~]$ vim /etc/sysconfig/network-scripts/ifcfg-ens33
```
- add this content
```sh
DEVICE=ens33
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=static
NAME="ens33"
IPADDR=192.168.1.102
PREFIX=24
GATEWAY=192.168.1.2
DNS1=192.168.1.2
```
More details be refered to slides on Classes.

### Edit hostname 
Take `hadoop102` as an example below

```sh
[root@hadoop102 ~]$ vim /etc/hostname
hadoop102
```
