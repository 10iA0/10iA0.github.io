---
title: os
date: 2019-07-25T16:43:00
tags: ["os"]
series: ["Python"]
featured: false
---

Python.os

<!--more-->

# Python os
## Python os.walk()

引用自：[https://www.runoob.com/python/os-walk.html](https://www.runoob.com/python/os-walk.html).

>遍历文件夹：
>``` Python
>for root, dirs, files in os.walk(path):
>
>    # root 表示当前正在访问的文件夹路径
>    # dirs 表示该文件夹下的文件夹
>    # files 表示该文件夹下的文件
>
>    # 遍历文件
>    for f in files:
>        print(os.path.join(root, f))
>
>    # 遍历所有的文件夹
>    for d in dirs:
>        print(os.path.join(root, d))
>```

## Python os.path()

[https://www.runoob.com/python/python-os-path.html](https://www.runoob.com/python/python-os-path.html).