---
title: Jupyter Installation
date: 2018-02-02 18:52:10
tags: ["Jupyter","ipykernel","ijava","nbextension"]
series: ["Anaconda"]
featured: false
---
<!--more-->

# Updated date: 09/13/2022
## Kernels
There are kernels in other languages available such as [ijava](https://github.com/SpencerPark/IJava) for Java. 


# Updated date: 05/03/2018
## [Plugins](https://cloud.tencent.com/developer/article/1511752)

There are plugins about Shell, themes, nbextension and Qgrid.

#### nbextension
>the `nbextensions` is a module of JavaScript, which changes jupyter's UI to make its features flourish and robust.

Install nbextensions
```
pip install jupyter_contrib_nbextensions
```

# Initialized date: 02/02/2018

What is the difference between Anaconda and Jupyter? From an [answer](https://qr.ae/pvi18o) on Quora, I had a glance at their relation.

> Anaconda is a prepackaged distribution of Python which contains a number of Python modules and packages, including Jupyter.

I ran jupyter notebook well in base environment once I installed Anaconda. However, Once I created and activated an non-base environment with conda, then typed `jupyter notebook` on command, I found a comment of `jupyter notebook is not found`.

>Jupyter core packages and a ipykernel space would be automatically installed in base environment after the installation of Anaconda. Whereas, there is not an such automatization in non-base environments. users need to install these packages and configurate kernel spaces manually in every non-base environments.

## Kernels
I installed `kernel` at first and choosed the ipykernel which means a kernel for IPython. After the installation of ipykernl, I ran `jupyter --version` to check which jupyter core packages were installed. On the next step, I made the configuration on kernelspec which is the relation between a kernel and an environment.

### Install ipykernel
```
conda install -n <env_name> ipykernel
```
### Configurate ipykernel space
```
python -m ipykernel install --user --name <env_name>
```

>If there are multiplied IPython kernels for different environments, users need to specify unique names for every kernelspes
```
python -m ipykernel install --user --name <env_name> --display-name "Python (env_name)"
```

The `--name` value is used by Jupyter internally. These commands will overwrite any existing kernel with the same name. `--display-name` is what you see in the notebook menus.

### Basic command lines
The command lines to adminster kernels and kernelspecs:
```
jupyter kernelspec list
```

```
jupyter kernelspec remove 'kernelname'
```

```
conda remove -n <env_name> ipykernel
```

### [Exception](https://pythontechworld.com/article/detail/v4DOLJ3oZb2z)

I encountered a problem that a packages was installed and loaded successfully in an active einvironment, but reported an erro as I run code in jpuyter notebook. I ran `sys.executalbe` and got an output `/home/jihang/anaconda3/envs/kong/bin/python`. However, I was in env Sunny in that time. In this situation, I ran `jupyter kernelspec list` to get Sunny's path and opened its file `kernel.json`. As a result, I found the argv in `kernel.json` was wrong which is `/home/jihang/anaconda3/envs/kong/bin/python` instead of `/home/jihang/anaconda3/envs/sunny/bin/python`. After I corrected argv, everything gone well.

`Note`: ??????sys.executable?????????????????????????????????????????????????????????????????? Python ?????????????????????????????????????????????????????????
