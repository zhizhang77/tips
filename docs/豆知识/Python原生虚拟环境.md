# Python虚拟环境

[TOC]

## 以下全已过时！不要折腾pip和conda了，直接用uv！

## 虚拟环境创建

python3以上可以使用自带的venv工具创建虚拟环境，首先进入想保存虚拟环境的目录，然后执行以下命令：

```sh
python3 -m venv --system-site-packages myenv
```

在当前目录下建立名为myenv的虚拟环境，**--system-site-packages**是为了复用系统已有的包，避免重复安装（如果虚拟环境可能需要在不同机器间拷贝，则不建议用该选项以免不同机器上工作表现不一致。如果虚拟环境中要装的包依赖比较多或者对依赖的版本有要求，也不建议用这个选项）。

如果新环境中pip一直出错，可以加上 **--without-pip**这个选项（ubuntu16.04下似乎很容易出现，新版系统未见）。

如果需要在进出虚拟环境时做其它设置，可以在虚拟环境bin目录下的activate和deactivate脚本中配置，以切换CUDA版本为例（所有activate中的修改记得在deactivate中还原）：

```sh
sudo rm -rf /usr/local/cuda
sudo ln -s /usr/local/cuda-10.1 /usr/local/cuda
ORIGINAL_CUDA_HOME=$CUDA_HOME
ORIGINAL_LD_LIBRARY_PATH=$LD_LIBRARY_PATH
export CUDA_HOME=/usr/local/cuda-10.1
export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
```

## 使用虚拟环境

linux下改好后进入虚拟环境并安装pip:

```sh
source ./myenv/bin/activate
curl https://bootstrap.pypa.io/get-pip.py | python
```

windows下用
```powershell
.\myenv\Scripts\activate #需要开启运行自定义脚本权限，不想开的话请用cmd执行.\myenv\Scripts\activate.bat
```

在虚拟环境内安装项目需要的其它包：

```sh
pip install -r requirements.txt
```

之后就可以正常工作了。

退出虚拟环境用deactivate命令

```sh
deactivate
```

## 虚拟环境迁移

虚拟环境所在的目录可以在两台机器间直接拷贝，前提是两台机器都装有相同的python版本，拷贝后编辑bin/activate文件，找到

```sh
VIRTUAL_ENV="oldpath/myenvname"
export VIRTUAL_ENV
```

把oldpath改成当前机器的目录即可，如果用其它shell，可以修改activate.csh，activate.fish等文件的相应位置

## 删除虚拟环境

直接删除对应子目录就可以

## 在vscode中应用虚拟环境

如果需要在vscode中使用虚拟环境，可以在文件夹的.vscode目录中建立settings.json文件，内容如下（目录名改成自己的即可）：

```json
{
    "python.pythonPath":
    "/home/zhangzhi/img_nna_2_0/ndk_rel_2_0/nnvm_ndk_tools_env/bin/python"
    }
```

