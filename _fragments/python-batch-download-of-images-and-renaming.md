---
layout: fragment
title: Python实现批量下载指定链接图片后改名
tags: [python]
description: Python实现批量下载指定链接图片后改名
keywords: python
---



**Python实现批量下载指定链接图片后改名**

------




```python
import requests

urls = ['https://carpedx.com/images/5eb4d75agw1e28yri0h5fa.jpg','https://carpedx.com/images/5eb4d75agw1e28yri0h5fb.jpg']
names = ['a.jpg','b.jpg']

for i, url in enumerate(urls):
    print(i)
    response = requests.get(url)
    with open(names[i], 'wb') as f:
        f.write(response.content)
```

