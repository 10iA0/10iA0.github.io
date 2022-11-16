---
title: Development Environment on WSL
date: 2022-08-03 09:27:00
tags: ["Anaconda","Postgresql","Cassandra","GUI apps on WSL"]
series: ["WSL"]
featured: false
---

After configuration of WSL, install Git, Anaconda, PostgreSQL and GUI apps used in WSL.

<!--more-->

# Set up a WSL development environment

The [original document](https://learn.microsoft.com/en-us/windows/wsl/setup/environment) have detail description on how to set up [VS Code](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode), [Git](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git), [Database](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-database), [GUI apps](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) and [NodeJS](https://learn.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-wsl). However, even I did what th original document asked me to do, I still encoutered some problems.

## Run [GUI apps](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) on WSL

I can't run google-chrome or other GUI apps on Bash command. Fortunately, there are Solutions on wesite and I think [these](ishttps://stackoverflow.com/a/66645230) is one of the best answers. There are also some optinoal configs on [DPI](https://super-unix.com/superuser/blurry-fonts-on-using-windows-default-scaling-with-wsl-gui-applications-hidpi/) and [Cursor](https://superuser.com/a/1372052), which help us use GUI apps better by XServer.

## Install Anaconda
I followed with this [page](https://www.how2shout.com/how-to/install-anaconda-wsl-windows-10-ubuntu-linux-app.html) to install Anaconda in WSL. Here are some brief demonstrations and descriptions base on my environment. Obviously, there are some difference compared with original essay.

## Install Database
### Postgresql
I installed postgresql with this [link](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-database#install-postgresql) and have not met problems.

### Cassandra
There is not content on official documentation installing Cassandra. But I have found the way install Cassandra in two essays: [English version](https://linuxhint.com/install_cassandra_on_ubuntu_20-04/) and [Chinese version](https://www.lsbin.com/9482.html). Unfortunately, these 2 essays all have typos, so I combined these essays to make sure installation successfully.

#### Prerequisites
Install Java JDK

#### get GPG key and add repository to sources list
Firstly, type this commands using `wget` to fetch the repository’s GPG key from the URL below:
```
wget -q -O - https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add --
```

Then, enter the command below to add the Apache Cassandra repository(存储库) to sources.list.d(源列表):
```
sudo sh -c 'echo "deb http://www.apache.org/dist/cassandra/debian 40x main" \ > /etc/apt/sources.list.d/cassandra.list'
```
>`Note`: The latest main version was 4.0 while I installed Cassandra, which is the reason I use `40x main` in this command. If you want to download old versions, e.g. 3.9, replace 40x with 39x.

#### Install Cassandra
```
sudo apt update
```
Be aware of no typing `cassandra` as `Cassandra` as capital matters in bash command.
```
sudo apt install cassandra
```

#### The basic commands
Check the status of Cassandra
```
nodetool status
```
```
sudo systemctl status cassandra
```

Start Cassandra
```
sudo systemctl start cassandra
```

Restart Cassandra
```
sudo systemctl restart cassandra
```

stop Cassandra
```
sudo systemctl stop cassandra
```

Set up Cassandra automatically start whille we turn on system 
```
sudo systemctl enable cassandra
```