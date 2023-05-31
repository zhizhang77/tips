# colab小技巧记录
[TOC]

## 交互

### 上传文件
colab官方的file.upload方法使用举例：
```python
from google.colab import files

uploaded = files.upload()
filepath = list(uploaded.keys())[0]

image = cv2.imread(filepath)
```