---
title: Anaconda Installation
date: 2018-02-01 16:43:00
tags: ["Anaconda"]
series: ["Linux"]
featured: false
---
The steps of installing anaconda.
<!--more-->

What are Anaconda and conda? What is the relation between Anaconda and conda? It seems that Anaconda have not officially made defifino on itself at its official wesit. Whereas I find a definition about Anaconda on Wikipedia and a definition about conda on Anaconda's wesite. 

> Anaconda is a distribution of the Python and R programming languages for scientific computing that aims to simplify package management and deployment.

> Conda is an open-source package and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. It also easily creates, saves, loads, and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

## Get link address of Installer
Firstly, I get the [link address](https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh) of Linux 64-Bit (x86) Installer at Anaconda's [main page](https://www.anaconda.com/products/distribution). 

## Download Installer
At bash command, I enter:
```sh
wget get https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh
```

## Run installer
At bash command, I entered:
```sh
bash Anaconda*-*-Linux-x86_64.sh
```
`bash` is a command whihch run shell scripts.

## Accept License terms
The installer got activated and asked me to press the `Enter` key to start the process. When the License Term appeared, I could either read it by pressing the `Enter` key or skip it by pressing the `q` key on the keyboard. I type `Yes` to accept the terms and conditions.

## Select the Installation directory
By default, the Anaconda on Linux will create the installation directory under the home directory of the system. I used this directoy directly instead of setting another path. At final, I pressed the `Return` key.

### Reload the Shell
The code of conda initialization will be automatically loaded in ~/.bashrc after the installation of Anaconda.

```sh
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/jihang/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/jihang/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/jihang/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/jihang/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
conda activate sunny
# <<< conda initialize <<<
```
Thus, I reloaded the shell by entering on command.
```sh
source ~/.bashrc
```