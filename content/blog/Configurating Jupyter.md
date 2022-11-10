---
title: Configurating Jupyter
date: 2018-02-02 08:52:10
tags: ["Jupyter","ipykernel","kernel","ijava","nbextension"]
# series: ["Anaconda"]
featured: false
---
<!--more-->

What is the difference between Anaconda and Jupyter? From an [answer](https://qr.ae/pvi18o) on Quora, I had a glance at their relation.

> Anaconda is a prepackaged distribution of Python which contains a number of Python modules and packages, including Jupyter.

I ran jupyter notebook well in base environment once I installed Anaconda. However, Once I created and activated an non-base environment with conda, then typed `jupyter notebook` on command, I found a comment of `jupyter notebook is not found`.

>Jupyter core packages and a ipykernel space would be automatically installed in base environment after the installation of Anaconda. Whereas, there is not an such automatization in non-base environments. users need to install these packages and configurate kernel spaces manually in every non-base environments.

## Kernels
I installed `kernel` at first and choosed the ipykernel which means a kernel for IPython. There were also other kernels available such as [ijava](https://github.com/SpencerPark/IJava) for Java. After the installation of ipykernl, I ran `jupyter --version` to check which jupyter core packages were installed. On the next step, I made the configuration on kernelspec which is the relation between a kernel and an environment.

### Installing the IPython kernel
```
conda install -n <env_name> ipykernel
```
### Configurate IPython kernelspec
```
python -m ipykernel install --user --name <env_name>
```

>If there are multiplied IPython kernels for different environments, users need to specify unique names for every kernelspes
```
python -m ipykernel install --user --name <env_name> --display-name "Python (env_name)"
```

The `--name` value is used by Jupyter internally. These commands will overwrite any existing kernel with the same name. `--display-name` is what you see in the notebook menus.

### Adminstration
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

Sometimes we encounter the problem that a packages is installed  and loaded successfully in a active einvironment, but cannot be loaded as well in jpuyter notebook. In this condition, We run `jupyter kernelspec list` to get each kernelspec's path and open the file `kernel.json` in the path of the kernelspec of selected environment. Be care to check if the argv is the same as the output of `sys.executalbe`.

`Note`: 属性sys.executable是一个字符串，在正常情况下，其值是当前运行的 Python 解释器对应的可执行程序所在的绝对路径。

### [Tricks](https://cloud.tencent.com/developer/article/1511752)

There are tricks about Shell, themes, nbextension and Qgrid.

#### nbextension
>Besides using shell directly in jupter notebook, we can also add extensions by installing nbextensions. nbextensions is a module of JavaScript. The nbextension basiclly changes jupyter's UI to make its features flourish and robust.

Installing nbextensions
```
pip install jupyter_contrib_nbextensions
```