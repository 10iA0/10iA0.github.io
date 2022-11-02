---
title: How to set up a WSL development environment
date: 2022-08-14 16:47:12
tags: ["Anaconda", "PostgreSQL", "Cassandra"]
series: ["WSL", "Linux"]
featured: true
---

After installation of WSL, we should set up a development environment user-friendly. For me, my environment always contain Git, Anaconda, PostgreSQL and GUI apps used in WSL.

<!--more-->
# How to set up a WSL development environment

The [original document](https://learn.microsoft.com/en-us/windows/wsl/setup/environment) have detail description on how to set up [VS Code](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode), [Git](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git), [Database](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-database), [GUI apps](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) and [NodeJS](https://learn.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-wsl). However, even I did what th original document asked me to do, I still encoutered some problems.

## Run [GUI apps](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) on WSL

I can't run google-chrome or other GUI apps on Bash command. Fortunately, there are Solutions on wesite and I think [these](ishttps://stackoverflow.com/a/66645230) is one of the best answers. There are also some optinoal configs on [DPI](https://super-unix.com/superuser/blurry-fonts-on-using-windows-default-scaling-with-wsl-gui-applications-hidpi/) and [Cursor](https://superuser.com/a/1372052), which help us use GUI apps better by XServer.

## Install Anaconda
First at all, what are Anaconda and conda? and what is the relation between Anaconda and conda?

It seems that Anaconda have not officially made defifino on itself on tis official wesit. Whereas we could find a definition about Anaconda on Wikipedia. 
> Anaconda is a distribution of the Python and R programming languages for scientific computing that aims to simplify package management and deployment.

Even though we have not found a definition on Anaconda's official westie, we found a definition on conda. 
> Conda is an open-source package and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. It also easily creates, saves, loads, and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

I followed with this [page](https://www.how2shout.com/how-to/install-anaconda-wsl-windows-10-ubuntu-linux-app.html) to install Anaconda in WSL. Here are some brief demonstrations and descriptions base on my environment. Obviously, there are some difference compared with original essay.

### Install Anaconda
we should firstly get in Anaconda [main page](https://www.anaconda.com/products/distribution) and find the link address of Linux 64-Bit (x86) Installer. In my installation, the link is https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh. At bash command, we enter:
```
wget get https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh
```

### Run the installer script
On bash command, `bash` is a command whihch run shell scripts. we enter:
```
bash Anaconda*-*-Linux-x86_64.sh
```

### Accept the License terms
The installer will get activated and ask we to press the `Enter` key to start the process. When the License Term appears, we either read it by pressing the `Enter` key or skip it by pressing the `q` key on the keyboard. After that, we can type `Yes` to accept the terms and conditions.

### Select the Installation directory
By default, the Anaconda on Linux will create the installation directory under the home directory of the system. Thus, we can use this directoy directly unless we want to set other path on the system. After that, press the `Return` key.

### Reload the Shell
Ather the installation of Anaconda, we either close the Ubuntu WSL or reload the shell by entering:
```
source ~/.bashrc
```

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