---
layout: fragment
title: Python实现判断图片相似度
tags: [python]
description: Python实现判断图片相似度
keywords: python
---



**Python实现判断图片相似度**

------




```python
from PIL import Image
from numpy import average, dot, linalg

# 对图片进行统一化处理
def get_thum(image, size=(64, 64), greyscale=False):
    image = image.resize(size, Image.ANTIALIAS)
    if greyscale:
        image = image.convert('L')
    return image

# 计算图片的余弦距离
def image_similarity_vectors_via_numpy(image1, image2):
    image1 = get_thum(image1)
    image2 = get_thum(image2)
    images = [image1, image2]
    vectors = []
    norms = []
    for image in images:
        vector = []
        for pixel_tuple in image.getdata():
            vector.append(average(pixel_tuple))
        vectors.append(vector)
        norms.append(linalg.norm(vector, 2))
    a, b = vectors
    a_norm, b_norm = norms
    res = dot(a / a_norm, b / b_norm)
    return res

# 判断imgs1的图片和imgs2的图片相似度
temps = []
imgs1 = ['1.jpg','2.jpg']
imgs2 = ['a.jpg','b.jpg','c.jpg','d.jpg','e.jpg','f.jpg','g.jpg']
for i in imgs1:
    image1 = Image.open('../dataset/images/' + i)
    for j in imgs2:
        temp1 = i + '------' + j
        temp2 = j + '------' + i
        if i == j or temp1 in temps or temp2 in temps:
            continue
        image2 = Image.open('../dataset/images/' + j)
        cosin = image_similarity_vectors_via_numpy(image1, image2)
        # print('图片余弦相似度', cosin)
        if cosin > 0.99:
            temp = i + '------' + j
            temps.append(temp)
            print(i.replace('.jpg','.webp'), '\t', j.replace('.jpg','.webp'), '\t', '图片余弦相似度', cosin)
```

