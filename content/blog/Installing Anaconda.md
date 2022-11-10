---
title: Installing Anaconda
date: 2018-02-01 16:43:00
tags: ["Anaconda"]
# series: ["WSL"]
featured: false
---
The steps of installing anaconda.
<!--more-->

What are Anaconda and conda? What is the relation between Anaconda and conda? It seems that Anaconda have not officially made defifino on itself at its official wesit. Whereas I find a definition about Anaconda on Wikipedia and a definition about conda on Anaconda's wesite. 

> Anaconda is a distribution of the Python and R programming languages for scientific computing that aims to simplify package management and deployment.

> Conda is an open-source package and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. It also easily creates, saves, loads, and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

## Getting link address of Installer
Firstly, I get the [link address](https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh) of Linux 64-Bit (x86) Installer at Anaconda's [main page](https://www.anaconda.com/products/distribution). 

## Downloading Installer
At bash command, I enter:
```
wget get https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh
```

## Running installer
At bash command, I enter:
```
bash Anaconda*-*-Linux-x86_64.sh
```
`bash` is a command whihch run shell scripts.

## Accepting License terms
The installer got activated and ask me to press the `Enter` key to start the process. When the License Term appears, I could either read it by pressing the `Enter` key or skip it by pressing the `q` key on the keyboard. Then, I type `Yes` to accept the terms and conditions.

## Selecting the Installation directory
By default, the Anaconda on Linux will create the installation directory under the home directory of the system. I use this directoy directly instead of setting another path. At final, I press the `Return` key.

### Reload the Shell
Ather the installation of Anaconda, I reloaded the shell by entering on command:
```
source ~/.bashrc
```