# DL 相关工具

[TOC]

## 运算量统计

### pytorch 网络运算量统计 torchstat (需要 3.6+)

```sh
pip install torchstat
```
```python
from torchstat import stat
import torchvision.models as models

model = models.alexnet()
stat(model, (3, 224, 224))
```

## 数据增强

### albumentations

```sh
pip install albumentations
```

可以同步转换 boundingbox：  
https://albumentations.ai/docs/getting_started/bounding_boxes_augmentation/

还有 segmentation：  
https://albumentations.ai/docs/getting_started/mask_augmentation/

### 生成各种背景和字体的文本图渲染工具text_renderer
https://github.com/oh-my-ocr/text_renderer 支持中文

## 可视化工具

### 网络结构图形化工具

#### 简单的卷积网络：

- https://github.com/gwding/draw_convnet
- http://alexlenail.me/NN-SVG/LeNet.html

#### 较通用：

- https://github.com/HarisIqbal88/PlotNeuralNet

#### 各框架专用：

- TF：https://www.tensorflow.org/tensorboard/graphs
- caffe：https://github.com/BVLC/caffe/blob/master/python/caffe/draw.py
- matlab：http://www.mathworks.com/help/nnet/ref/view.html
- keras：https://transcranial.github.io/keras-js/#/inception-v3
- 基于 Keras： https://conx.readthedocs.io/en/latest/index.html

#### 三维可视化工具

- https://github.com/julrog/nn_vis

### Saliency maps（显著图） & activation maps

- https://github.com/Zetane/viewer
- https://github.com/MisaOgura/flashtorch
  > 参考：
  > https://towardsdatascience.com/feature-visualisation-in-pytorch-saliency-maps-a3f99d08f78a
  > https://towardsdatascience.com/gaining-insights-on-transfer-learning-with-flashtorch-de344df0f410
- https://microscope.openai.com/models **包含了 CLIP 的结果，OpenAI 官方出品**

## 资源管理工具

### GPU 管理和分布式训练

- Determined：支持通过网页和 cli 管理，主页 https://docs.determined.ai/latest/ ，参考 https://zhuanlan.zhihu.com/p/422462131
- OpenPAI：微软出品，除开 GPU，还支持 TPU，FPGA 等的资源管理，带 vscode 扩展，有中文文档 https://openpai.readthedocs.io/zh_CN/latest/ ，由于国内连接奇慢，有专门的国内合作服务器，见 https://openpai.readthedocs.io/zh_CN/latest/manual/cluster-admin/configuration-for-china.html

## 训练辅助工具

- w&b：使用嵌入API实现在线监控 https://wandb.ai/quickstart/pytorch