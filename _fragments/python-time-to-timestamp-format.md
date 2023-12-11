---
layout: fragment
title: Python实现日期时间yyyy-MM-dd HH:mm:ss格式转时间戳格式
tags: [python]
description: Python实现日期时间yyyy-MM-dd HH:mm:ss格式转时间戳格式
keywords: python
---



**Python实现日期时间 `yyyy-MM-dd HH:mm:ss` 格式转时间戳格式**

------




```python
import time

time_list = ['2023-07-01 16:14:00','2023-07-01 10:17:00']

if __name__ == '__main__':
    for t in time_list:
        st = time.strptime(t, "%Y-%m-%d %H:%M:%S")
        mkt = int(time.mktime(st))
        print(mkt)
    pass
```

