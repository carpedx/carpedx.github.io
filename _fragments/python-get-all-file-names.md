---
layout: fragment
title: Python实现获取文件下所有文件名
tags: [python]
description: Python实现获取文件下所有文件名
keywords: python
---



**Python实现获取文件下所有文件名**

------




```python
import os

path = "D:\\test"
names = os.listdir(path)
for name in names:
    print(name)
```

