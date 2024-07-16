# opencv相关
[TOC]

## contour的格式
```python
# -*- coding: utf-8 -*-

import os
import numpy as np
import cv2
import matplotlib.pyplot as plt

img = np.zeros((720, 1280, 3), dtype=np.uint8)
cont = np.zeros((4, 1, 2), dtype=np.int32)
cont[0][0] = (212, 69)
cont[1][0] = (1012, 102)
cont[2][0] = (989, 569)
cont[3][0] = (210, 526)
cc = [cont]
# cont = [cont1,cont2,cont3,cont4]
# cont = [np.array(((((212,69))),(((2012,102))),(((1989,769))),(((210,726)))))]
img2 = img.copy()
cv2.drawContours(img2, cc, -1, (0, 0, 255), cv2.FILLED)
img1 = cv2.addWeighted(img2, 0.5, img, 0.5, 0)
img1 = cv2.cvtColor(img1, cv2.COLOR_RGB2BGR)

plt.figure(figsize=(10, 10))
plt.subplot(121)
plt.imshow(img)
plt.subplot(122)
plt.imshow(img1)
plt.show()
```
## 将灰度图或者掩模扩展到3通道上，以便与彩色图片互操作

```python
mask3channel = np.expand_dims(mask,2).repeat(3,axis=2)
```

## 检查当前opencv的build信息

`print(cv2.getBuildInformation()`可以显示编译时使用的模块、依赖等信息，与cmake时输出的一致

## YUV和YCbCr转换公式的区别
YUV多用于模拟而YCbCr用于数字，数字图像处理中即使叫YUV，很多时候实际也是用的YCbCr。
```matlab
% YCbCr/YPbPr with Rec 470/601 luma
A = [0.299 0.587 0.114;-0.1687 -0.3313 0.5;0.5 -0.4187 -0.08131];
% YUV with Rec 470/601 luma
A = [0.299 0.587 0.114; -0.14713 -0.28886 0.436; 0.615 -0.51499 -0.10001];
```
In the first case, the nominal range of the chroma components are both 2\*0.5=1.  They're equal and normalized and so they easily scale to fit integer data range.  In the case of YUV, the chroma components have different widths (2\*0.436=0.872 and 2\*0.615=1.23).  If uniformly scaled to an integer data range, either U would make poor use of the range of an integer class, or V would get truncated.  These scaling factors are the same regardless of which luma constants are used.