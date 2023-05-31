# Docker安装与容器配置

[TOC]

## Docker安装

### 原版docker安装

参考https://docs.docker.com/engine/install/ubuntu/和https://docs.docker.com/engine/install/linux-postinstall/，其中前者必需，后者的如下操作基本也是必需，否则所有docker指令都要sudo才能执行：

- 添加docker用户组

      sudo groupadd docker

- 将用户加入该 group 内。

      sudo gpasswd -a ${USER} docker

- 重启 docker 服务

      sudo service docker restart

- 切换当前会话到新 group（有时不生效，这时直接重启即可）

      newgrp - docker

以后启动docker就不用加sudo了。

### Nvidia container toolkit

Nvidia container toolkit就是之前的nvidia docker，在容器内用到CUDA时必需。安装过程见https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker。原始repo在https://github.com/NVIDIA/nvidia-docker。



## 使用docker配置机器无关的项目工作环境

如果希望保证不同机器的工作完全一致，docker镜像是更安全的方式，然而体积也更大。

### Dockerfile

自定义image建议基于dockerhub上已有的官方镜像添加内容。以人脸检测项目为例，首先在当前目录下建立一个名叫Dockerfile的文件，内容如下：

```dockerfile
FROM pytorch/pytorch:1.2-cuda10.0-cudnn7-runtime

MAINTAINER zhizhang "zhizhang@tusvisiontech.com"

ADD pytorch_multracking /workspace/pytorch_multracking/

RUN apt-get update \
    && apt-get install -y apt-utils libglib2.0-0 libjpeg-dev libtiff5-dev \
    && apt-get install -y libjasper-dev libpng12-dev \
    && apt-get install -y libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
    && apt-get install -y libxvidcore-dev libx264-dev libx265-dev \
    && apt-get install -y libatlas-base-dev gfortran

RUN pip install -r /workspace/pytorch_multracking/req3.txt -i  http://pypi.douban.com/simple/ --trusted-host pypi.douban.com

RUN pip install -r /workspace/pytorch_multracking/requirements.txt -i  http://pypi.douban.com/simple/ --trusted-host pypi.douban.com

RUN pip install -r /workspace/pytorch_multracking/req2.txt -i  http://pypi.douban.com/simple/ --trusted-host pypi.douban.com

RUN cd /workspace/pytorch_multracking/utils/lib && python setup.py build develop

WORKDIR "/workspace"
CMD ["/bin/bash"]
```

其次在Dockerfile所在目录下编译镜像（如果之前未下载过pytorch1.2镜像会自动下载）：

```sh
docker build -t myprojectname:0.01 . # 0.01是tag，可选
```

完成后用docker images命令可以看到名为myprojectname的新docker镜像。

### 运行

可以用如下命令启动镜像并将当前目录映射为docker系统内的usr/src/test文件夹，exit退出环境：

```sh
# Starting a GPU enabled container on specific GPUs
docker run --gpus '"device=1,2"' -ti --rm -v $PWD:/workspace/test  -w /workspace/test myprojectname:0.01
```

### 迁移

编译好的镜像可以直接打包：

```sh
docker save myprojectname > /home/myprojectname.tar
```

压缩文件拷贝到其他机器后解压即用：

```sh
docker load < myprojectname.tar
```

## docker compose协同多个镜像
docker compose使用yaml定义行为，缺省文件名为docker-compose.yml，也可以用`-f`参数指定文件名。以下是docker-compose.yml的一个示例：
```yaml
version: "3.5"
services:
  web-fe:
    build: .
    command: python app.py
    ports:
      - target: 5000
        published: 5000
    networks:
      - counter-net
    volumes:
      - type: volume
        source: counter-vol
        target: /code
  redis:
    image: "redis:alpine"
    networks:
      counter-net:

networks:
  counter-net:

volumes:
  counter-vol:
```
当前目录下存在docker-compose.yml时，可以用`docker compose up`启动。

### `docker compose up`和`docker compose up -d`的区别
`docker compose up`等价于`docker compose logs -f`，它会收集所有容器的日志输出直到退出`docker compose up`命令，或者容器都停止运行。`docker compose up -d`则以后台方式运行容器。不会在终端上打印运行日志。


### docker compose官方示例集
https://github.com/docker/awesome-compose