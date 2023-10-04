---
layout: wiki
title: 从文件夹中筛选指定的图片取出
cate1: python
cate2:
description: Python实现从文件夹中筛选指定的图片取出
keywords: python
---



**Python实现从文件夹中筛选指定的图片取出**

------




```python
import os
import shutil

imgs = ['a.jpg','b.jpg','c.jpg','d.jpg']
if __name__ == '__main__':
    for img in imgs:
        path = 'D:\\test\\images\\' + img
        if os.path.exists(path):
            shutil.move(path, 'D:\\test\\screen')
        else:
            print(img)
    pass
```

