# 数据集相关
[TOC]

## MNIST和FashionMNIST的格式

图像和label文件都是big endian的字节流
图像文件的前4个32位整数分别是magic number，图像总数，单张图像行数和列数，之后每784个bytes表示一张图片
label文件的前2个32位整数分别是magic number和图像整数，之后每个byte表示一张图片

以训练集为例：
image: magic 2051, numImages 60000, numRows 28, numColumns 28 
label: magic 2049, numImages 60000

```python
import numpy as np
import struct
from PIL import Image
import scipy.misc
import os

if not os.path.exists('train'):
    os.mkdir('train')

labelfn = r'raw/train-labels-idx1-ubyte'
imgfn = r'raw/train-images-idx3-ubyte'
imgfile = open(imgfn, 'rb')
labelfile = open(labelfn, 'rb')
imgbuf = imgfile.read()
labelbuf = labelfile.read()
imgfile.close()
labelfile.close()
 
index = 0
magic, numImages, numRows, numColumns = struct.unpack_from('>IIII', imgbuf, index)
index += struct.calcsize('>IIII')
print ('image: magic %d, numImages %d, numRows %d, numColumns %d'%(magic, numImages, numRows, numColumns))

labelindex = 0
magicl, numImagesl = struct.unpack_from('>II', labelbuf, labelindex)
labelindex += struct.calcsize('>II')
print ('label: magic %d, numImages %d'%(magicl, numImagesl))

# num of images u wanna dump
numImages = 70

for i in range(numImages):
    im = struct.unpack_from('>784B', imgbuf, index)  # 28*28=784 bytes, big endian
    index += struct.calcsize('>784B')
    im = np.array(im)
    im = im.reshape(28, 28)

    l = struct.unpack_from('>B', labelbuf, labelindex)
    l = l[0]
    labelindex += struct.calcsize('>B')

    scipy.misc.imsave('train/'+"train" + str(i)+'_'+str(l) + ".png", im)
```
