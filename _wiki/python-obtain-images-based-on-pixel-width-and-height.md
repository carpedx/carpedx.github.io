---
layout: wiki
title: 根据像素宽高筛选获取图片
cate1: python
cate2:
description: Python实现根据像素宽高筛选获取图片
keywords: python
---



**Python实现根据像素宽高筛选获取图片**

------




```python
from PIL import Image
import os

folder_path = "E:\\test"

for filename in os.listdir(folder_path):
    # 判断文件是否是图片
    if filename.endswith(".jpg") or filename.endswith(".jpeg") or filename.endswith(".png"):
        # 打开图片
        with Image.open(os.path.join(folder_path, filename)) as img:
            # 获取图片宽度
            width, height = img.size
            # 判断图片宽度是否大于
            if width > 768:
                print(filename)
```

