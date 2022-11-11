---
title: Fundamental development environment on Hadoop
date: 2021-06-15 13:38:00
tags: [""]
series: ["Hadoop"]
featured: false
---
Setting up the development environment on Hadoop is the key point in big data computing.
<!--more-->

## Set up the development environment on Hadoop

### 1. Prepare a blueprint

Prepare a development enviroment on virtual machine as  a blueprint for further learning Hadoop. The buleprint is named as `hadoop100` and configurated as:
- DRAM: 4G
- HDD: 50G

#### 1.1 Install essential tools

Install essential tools from yum repos for this operation system.
```
# Test if the Internet is connected or not.
[root@hadoop100 ~]# ping www.google.com

[root@hadoop100 ~]# yum install -y epel-release
[root@hadoop100 ~]# yum install -y psmisc nc net-tools rsync vim lrzsz ntp libzstd openssl-static tree iotop git
```

#### 1.2 Stop and disable the firewall
Turn off the firewall and disable its auto-start.
```
[root@hadoop100 ~]# systemctl stop firewalld
[root@hadoop100 ~]# systemctl disable firewalld
```

#### 1.3 Create and authorize a user
Create a user `jihang` for myself and change the password.
```
[root@hadoop100 ~]# useradd jihang
[root@hadoop100 ~]# passwd jihang
```
Authorize user `jihang` as `root` to exectute `sudo` commands.
```
[root@hadoop100 ~]# vim /etc/sudoers
```
Edit the file `/etc/sudoers`, add a line on it as below:
```
## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
jihang   ALL=(ALL)     NOPASSWD:ALL
```
#### 1.4 Make and authorize folders

Make folders `module` and `software` on `/opt`
```
[root@hadoop100 ~]# mkdir /opt/module
[root@hadoop100 ~]# mkdir /opt/software
```

Edit folders `module` and `software`'s owner and group from `root` to `jihang` 
```
[root@hadoop100 ~]# chown jihang:jihang /opt/module 
[root@hadoop100 ~]# chown jihang:jihang /opt/software
```

Check folders `module`, `software`'s owner and group
```
[root@hadoop100 ~]# cd /opt/
[root@hadoop100 opt]# ll
总用量 12
drwxr-xr-x. 2 jihang jihang 4096 5月  28 17:18 module
drwxr-xr-x. 2 root    root    4096 9月   7 2017 rh
drwxr-xr-x. 2 jihang jihang 4096 5月  28 17:18 software
```
#### 1.5 Uninstall the defalut `open JDK` on system
```
[root@hadoop100 ~]# rpm -qa | grep -i java | xargs -n1 rpm -e --nodeps
```
#### 1.6 Reboot Hadoop100
```
[root@hadoop100 ~]# reboot
```

### 2. Clone 3 systems

Clone 3 systems `hadoop102`, `hadoop103`, `hadoop104` from blueprint `hadoop100`

#### 2.1 Edit IP on cloned systems 

Take `hadoop102` as an example below

Edit the static IP
```
[root@hadoop100 ~]# vim /etc/sysconfig/network-scripts/ifcfg-ens33
```
add this content
```
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

#### 2.2 Edit hostname 
Take `hadoop102` as an example below

```
[root@hadoop100 ~]# vim /etc/hostname
hadoop102
```

#### 2.3 Edit file `hosts`
```
[root@hadoop100 ~]# vim /etc/hosts
```
add this content
```
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

Reboot hadoop102 
```
[root@hadoop100 ~]# reboot
```

Edit file `hosts` on windows10
```
code C:\Windows\System32\drivers\etc\hosts
```
add this content
```
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
### 3. Install JDK and Hadoop

Take `hadoop102` as an example below

#### 3.1 Install JDK

Uninstall default JDK
```
[jihang@hadoop102 ~]$ rpm -qa | grep -i java | xargs -n1 sudo rpm -e --nodeps
```

I have downloaded tar `jdk` on `opt/software/`
```
[jihang@hadoop102 ~]$ ls /opt/software/
hadoop-3.1.3.tar.gz  jdk-8u212-linux-x64.tar.gz
```

Unzip JDK to `/opt/module`
```
[jihang@hadoop102 software]$ tar -zxvf jdk-8u212-linux-x64.tar.gz -C /opt/module/
```

Configurate JDK environmental variable
```
[jihang@hadoop102 ~]$ sudo vim /etc/profile
```
add as below
```
#JAVA_HOME
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin
```
save and quit
```
:wq
```
Refresh file `/etc/profile`, acitivate new environmental variable `PATH`
```
[jihang@hadoop102 ~]$ source /etc/profile
```
Check if JDK is installed well
```
[jihang@hadoop102 ~]$ java -version
java version "1.8.0_212"
```

#### 3.2 Install Hadoop

I have downloaded tar `hadoop` on `opt/software/`
```
[jihang@hadoop102 ~]$ ls /opt/software/
hadoop-3.1.3.tar.gz  jdk-8u212-linux-x64.tar.gz
```

Unzip Hadoop to `/opt/module`
```
[jihang@hadoop102 software]$ tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module/
```

Check if Hadoop is unzip well
```
[jihang@hadoop102 software]$ ls /opt/module/
hadoop-3.1.3
```

Print present working dictionary
```
[jihang@hadoop102 hadoop-3.1.3]$ pwd
/opt/module/hadoop-3.1.3
```

Add Hadoop to environmental variable
```
sudo vim /etc/profile
```

add this content
```
#HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

save and quit
```
:wq
```

Refresh file /etc/profile, acitivate new environmental variable `PATH`
```
[jihang@hadoop102 hadoop-3.1.3]$ source /etc/profile
```

Check if Hadoop is installed well
```
[jihang@hadoop102 hadoop-3.1.3]$ hadoop version
Hadoop 3.1.3
```

### 4. Hadoop's FHS(Filesystem Hierarchy Standard)
Check Hadoop' FHS
```
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