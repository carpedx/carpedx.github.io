---
layout: wiki
title: 批量下载指定链接图片后改名
cate1: python
cate2:
description: 批量下载指定链接图片后改名
keywords: python
---



**Python实现批量下载指定链接图片后改名**

------




```
import requests

urls = ['https://carpedx.com/images/5eb4d75agw1e28yri0h5fa.jpg','https://carpedx.com/images/5eb4d75agw1e28yri0h5fb.jpg']
names = ['a.jpg','b.jpg']

for i, url in enumerate(urls):
    print(i)
    response = requests.get(url)
    with open(names[i], 'wb') as f:
        f.write(response.content)
```

