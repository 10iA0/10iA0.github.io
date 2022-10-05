- [How to use Linux on Windows?](#how-to-use-linux-on-windows)
  - [Overview](#overview)
  - [Install Linux on Windows](#install-linux-on-windows)
    - [Prepare prerequisites for WSL](#prepare-prerequisites-for-wsl)
    - [Install Linux distro on Win](#install-linux-distro-on-win)
    - [Set up Linux username and passwd](#set-up-linux-username-and-passwd)
    - [Update & upgrade all packages](#update--upgrade-all-packages)
    - [Move WSL from disk C to disk D](#move-wsl-from-disk-c-to-disk-d)
# How to use Linux on Windows?
`Attention!` I just record how I installed and set up my developemnt environment and **provide my solutions to each special case I encountered on this process** at this blog. However, It is a subset of the [WSL documentation](https://learn.microsoft.com/en-us/windows/wsl/) after all when it comes to installation of WSL and setting up developemnt envirionment. 

## Overview
What is WSL? Let's check the [introduction](https://learn.microsoft.com/en-us/windows/wsl/about) on microsoft's documents:

>The Windows Subsystem for Linux lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup.

The normal commands we could operate WSL refer to [basic wsl commands](https://learn.microsoft.com/en-us/windows/wsl/basic-commands) on microsoft's documents. 

## Install Linux on Windows
>Please be informed that WSL is a abbr of 'The Windows Subsystem for Linux', which explains the WSL is a Linux system on Windows system. In fact, Microsoft Store provides many distros of Linux, such as Ubuntu, CentOS, etc, for developers like you and me. We are free to choose any distros we'd like to install and use.

### Prepare prerequisites for WSL
The prerequisites for WSL are shown below:
1. (Compulsory) Control Panel --> Programs --> Turn Windows features on or off --> Check box next to: "Windows Subsystem for Linux" --> Windows will re-boot.
2. (Optional) Set up Windows Terminal. I stronglly suggest who want to enjoy WSL to use Windows Terminal as it is really convenient. Also, the processes as fllow are all based on Windows Terminal. More details refer to [set up Windows Terminal](https://learn.microsoft.com/en-us/windows/terminal/get-started).

### Install Linux distro on Win
There are 2 ways in installing Linux distros on Win. By default, we install a Linux distro as Ubuntu. But, we can choose the distro we'd like to install. As we'd like to use command to config and install apps, the PowerShell or Command Line on Win will be used.

We can install Linux at default by just entering `wsl --install` on command. On the other hand, We can install Linux at custom: 1. Get list of available distros: `wsl --list --online`; 2. Choose the distro name we'd like to install; 3. Download the distro we'd like to install: `wsl --install -d <Distribution Name>`.

### Set up Linux username and passwd
After finishing of installation of Linux, Bash command would open automatically and remind us to set up the Linux user name and passwd. 

### Update & upgrade all packages
We should enter update and upgrade command line on Bash command after the installation of Linux distro to make sure evert packages latest.

```Bash
sudo apt update && sudo apt upgrade
```

### Move WSL from disk C to disk D
We install WSL successfully and use it smoothly. It's cool! But, the WSL is installed at disk C at default. As we gradually install apps in WSL in development, the storage of disk C will be warning. So, The early we move WSL from disk C to disk D, the less unexpected troubles we will encounter.

There 3 ways in moving WSL from disk C to a new disk. I tried all these methods and got only one success.

- [Using the WSL command line tool](https://stackoverflow.com/a/51767786)
- [Using LxRunOffline](https://learnku.com/articles/46234)
- [Moving ext4.vhd and re-editing BasePath](https://www.msftnext.com/how-to-move-the-wsl-linux-subsystem-to-another-disk-or-partition/)

[Using the WSL command line tool](https://stackoverflow.com/a/51767786) led to some werid results that I can't explain here in detail. I also tried the LxRunOffline that is introduced on [Using the WSL command line tool](https://stackoverflow.com/a/51767786) and [Using LxRunOffline](https://learnku.com/articles/46234). Their descriptions have little different but both lead a erro that some users have found and haven't been fixed. Finially, I made it by [Moving ext4.vhd and re-editing BasePath](https://www.msftnext.com/how-to-move-the-wsl-linux-subsystem-to-another-disk-or-partition/) without any erro.

