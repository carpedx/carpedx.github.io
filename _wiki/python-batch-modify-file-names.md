---
layout: wiki
title: 批量修改对应的文件名
cate1: python
cate2:
description: 批量修改对应的文件名
keywords: python
---



**Python实现批量修改对应的文件名**

------




```python
import os

old_names = ['1.mp4','2.mp4']
new_names = ['a.mp4','b.mp4']
path = 'D:\\test'

if len(old_names) == len(new_names):
    for i in range(len(old_names)):
        old_file = os.path.join(path, old_names[i])
        new_file = os.path.join(path, new_names[i])
        if os.path.exists(old_file):
            os.rename(old_file, new_file)
            print(f"Renamed {old_names[i]} to {new_names[i]}")
        else:
            print(f"File {old_names[i]} does not exist.")
```

