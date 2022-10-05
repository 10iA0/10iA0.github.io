- [How to use Anaconda and Jupyter?](#how-to-use-anaconda-and-jupyter)
  - [Anaconda](#anaconda)
  - [Jupyter](#jupyter)
    - [Kernels](#kernels)
      - [ipykernel](#ipykernel)
      - [ijava](#ijava)
      - [Adminstration](#adminstration)
    - [Erros](#erros)
    - [Tricks](#tricks)
      - [Shell](#shell)
      - [themes](#themes)
      - [nbextension](#nbextension)
      - [Qgrid](#qgrid)

# How to use Anaconda and Jupyter?
## Anaconda
We have installed Anaconda while we set up my development environment on WSL. In fact, there is no other operation we should take care on Anaconda except conda.

> Conda is an open-source package and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. It also easily creates, saves, loads, and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

Campared with original dispaly on command, there would be a (base) added on command after the installation Anaconda. If not, enter `conda activate` to activate conda environment (enter `conda deactivate` to deactivate conda environment in which the (base) symbol disappers). The (base) means from the time of being I am in base environment.

> `Note`: Please be informed that there is no reason to use base environment so as not to mess up our environment that we cannot recover. Otherwise, we should create other environments so that we can do everything we want to and remove them without any loss if we accidentlly destroy these environments.

Base on the statements, we should realize that using conda to administer developement is very important. The basic conda commands are really simple and useful:

Create an env:
```
conda create -n <env_name> <package1> <package2> <package3> ...
```

Activate an env:
```
conda activate <env_name>
```

Deactivate an env:
```
conda deactivate <env_name>
```

Install packages in an env:
```
conda install -n <env_name> <package1> <package2> <package3> ...
```

Update packages in an env:
```
conda update -n <env_name> <package1> <package2> <package3> ...
```

Delete an env:
```
conda remove -n <env_name> -all
```
## Jupyter
What is the difference between Anaconda and Jupyter? From an [answer](https://qr.ae/pvi18o) of Quora, we have a glance at their relation.

> Anaconda is a prepackaged distribution of Python which contains a number of Python modules and packages, including Jupyter.

Generally, we could run jupyter notebook once we installed Anaconda in base environment. Looks fine until now. However, if we create and activate an non-base environment with conda, then type `jupyter notebook` on command, we would find a comment of `jupyter notebook is not found`.

We could create and adminster development envionments with conda. Furthermore, we could install packages while we are creating conda environments or after we created conda environments. Jupyter core packages are installed in base environment after we installed Anaconda. Whereas, these packages are not installed in non-base environments. We need to install these packages manually in every non-base environments.

### Kernels
#### ipykernel
The priority of installing jupyter core packages is to install `kernel` at first. Normally, we choose the ipykernel which means a kernel for IPython. There are also other kernels available such as ijava for Java. After we installed ipykernl, we could run `jupyter --version` to check which jupyter core packages were installed. If jupyter notebook is uninstalled, we should run `conda install notebook`. 

We have not done yet. In the next step, we use ipykernel to install kernelspec for jupyter. Why we should take this step? The fact is that we could only choose an environment to run all package installed in this environment in a jupyter notebook. However, we could choose all available kernels to code in any environment we run a jupyter notebook. All kernelspecs are global configurations for jupyter in fact.

Install the IPython kernel package:
```
conda install -n <env_name> ipykernel
```
Install IPython kernelspec in selected environment with kernel: 
```
python -m ipykernel install --user --name <env_name>
```

If we have multiple IPython kernels for different environments, we need to specify unique names for every kernelspes:
```
python -m ipykernel install --user --name <env_name> --display-name "Python (env_name)"
```

> The `--name` value is used by Jupyter internally. These commands will overwrite any existing kernel with the same name. `--display-name` is what you see in the notebook menus.

#### [ijava](https://github.com/SpencerPark/IJava)


#### Adminstration
We usually use command lines below to adminster kernel and kernelspec.
```
jupyter kernelspec list
```

```
jupyter kernelspec remove 'kernelname'
```

```
conda remove -n <env_name> ipykernel
```

### [Erros](https://pythontechworld.com/article/detail/v4DOLJ3oZb2z)

Sometimes we encounter the problem that a packages is installed  and loaded successfully in a active einvironment, but cannot be loaded as well in jpuyter notebook. In this condition, We run `jupyter kernelspec list` to get each kernelspec's path and open the file `kernel.json` in the path of the kernelspec of selected environment. Be care to check if the argv is the same as the output of `sys.executalbe`.

`Note`: 属性sys.executable是一个字符串，在正常情况下，其值是当前运行的 Python 解释器对应的可执行程序所在的绝对路径。

### [Tricks](https://cloud.tencent.com/developer/article/1511752)

#### Shell

#### themes

#### nbextension
Besides using shell directly in jupter notebook, we can also add extensions by installing nbextensions. nbextensions is a module of JavaScript. The nbextension basiclly changes jupyter's UI to make its features flourish and robust.

Install nbextensions packgae:
```
pip install jupyter_contrib_nbextensions
```

Install features with nbextensions:
```
jupyter contrib nbextension install --user
```

#### Qgrid 