---
title: Hadoop-Prerequisites-Clone 3 systems from a blueprint 
date: 2021-06-15 13:38:00
tags: ["IP","hostname","hosts"]
series: ["BDC","Hadoop"]
featured: false
---

Setting up the development environment for big data computing.

<!--more-->


## Clone 3 systems (from a blueprint)

Clone 3 systems `hadoop102`, `hadoop103`, `hadoop104` from blueprint `hadoop100`

### Edit IP on cloned systems 

Take `hadoop102` as an example below

Edit the static IP
```sh
[root@hadoop100 ~]$ vim /etc/sysconfig/network-scripts/ifcfg-ens33
```
add this content
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
[root@hadoop100 ~]$ vim /etc/hostname
hadoop102
```

### Edit file `hosts`
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

Reboot hadoop102 
```sh
[root@hadoop100 ~]$ reboot
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