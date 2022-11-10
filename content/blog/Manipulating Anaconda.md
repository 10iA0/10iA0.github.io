---
title: Manipulating Anaconda
date: 2018-02-01 18:21:43
tags: ["Anaconda"]
# series: ["WSL"]
featured: false
---
The key points of using anaconda in data analysis.
<!--more-->

I have installed Anaconda. There is no other operation I should take care on Anaconda except `conda`. I past its introduction on Anaconda's website here:

> Conda is an open-source package and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. It also easily creates, saves, loads, and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

Campared with original dispaly on command, there would be a (base) added on command after the installation Anaconda. If not, enter `conda activate` to activate conda environment (enter `conda deactivate` to deactivate conda environment in which the (base) symbol disappers). 

The (base) means that, from the time of being, I am in base environment. However, there is no reason to use base environment as it has big possbilities to mess up development environment that cannot be recovered. Normally, I create new environments and use them so that I could do everything on them. If I accidentlly make these environment shit, I could remove them without any loss. 

The basic conda commands are really simple and useful:

## Creating an environment
```
conda create -n <env_name> <package1> <package2> <package3> ...
```

## Activating an environment
```
conda activate <env_name>
```

## Deactivating an environment
```
conda deactivate <env_name>
```

## Installing packages in an environment
```
conda install -n <env_name> <package1> <package2> <package3> ...
```

## Updating packages in an environment
```
conda update -n <env_name> <package1> <package2> <package3> ...
```

## Deleting an environment
```
conda remove -n <env_name> -all
```