---
title: Python in 10 Minutes
date: 2017-09-25 16:43:00
tags: ["os"]
series: ["Python"]
featured: false
---

Python.os

<!--more-->

## `__name__` and `main()`

A Python file can be executed as a script or imported as a module. When a Python file is ran as a script, its variable `__name__`equals to `'__main__'`. Otherwise, `__name__` does not equals to `'__main__'`. 

Normally, we define a function `main()` in which we write what we want the script to do. For example,

```python
def main():
    print(Hello World)

if __name__=='__main__':
    main()
```

When we execute a Python file as a script (run `Python Hello.py` in console), as the variable `__name__`equals to `'__main__'`, the console shall print `Hello World`. However, if we import the Python file as a module, all classes and functions except `main` would be imported and executed as `__name__` does not equals to `'__main__'`.

## Python os.walk()

I cite the pharagraph below form this link:
[https://www.geeksforgeeks.org/os-walk-python/](https://www.geeksforgeeks.org/os-walk-python/).

>**How to traverse file system in Python?**
>
>Suppose we have given below file structure in our system and we want to traverse all it’s branches completely from top to bottom ?
>
>**How does os.walk() work in python?**
>
>OS.walk() generate the file names in a directory tree by walking the tree either top-down or bottom-up. For each directory in the tree rooted at directory top (including top itself), it yields a 3-tuple (dirpath, dirnames, filenames).
>
>- root : Prints out directories only from what you specified.
>- dirs : Prints out sub-directories from root.
>- files : Prints out all files from root and directories.

## Python os.path()

[https://www.geeksforgeeks.org/python-os-path-join-method/](https://www.geeksforgeeks.org/python-os-path-join-method/).