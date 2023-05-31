# VSCode相关
[TOC]

## 程序调试时带输入参数

1. 菜单“调试->打开配置”打开launch.json
2. 在configurations中增加args，参数中的每个用空格隔开的都用双引号括起，如下例：

    > "args": [ "--model", "EDSR", "--scale", "2", "--save", "edsr_x2", "--n_resblocks", "32", "--n_feats", "256", "--res_scale", "0.1", "--reset"]
## vscode编译和调试C++

- 首先在F1面板里输入关键词"C/C++ edit"，选择匹配到的命令"C/C++: Edit Configurations (UI)"，确认编译程序和intelliSenseMode都是正确的。如果之前从未配置过，需要选择一个配置名称比如win32，设置其编译器路径等参数
- 打开所在文件夹之后选择菜单栏【运行】->【添加配置…】->"C++ (GDB/LLDB)"->"g++.exe - 生成和调试活动文件"(后两者按实际情况选)
- 上述操作完成后会生成task.json和launch.json两个文件，打开task.json，增加你想要的配置，或者把原有配置改成想要的即可。比方说原有配置是 **"command": "gcc",** ，而我们需要用makefile编译，那就把它改成 **"command": "make"** 
- 通常不用改launch.json，需要注意的是cwd和program的目录，根据情况可能要设为工作目录${workspaceFolder}$或${fileDirname}$
- 需要调试gcc/g++的话编译时要在文件名前加-g标志，如下。如果-g放到$<之后是无效的。

```makefile
    %.o: %.cpp; $(CXX) -g $< -o $@ $(CXXFLAGS)
```

## vscode中使用cmake
- 首先确认系统中有安装cmake并且已添加系统路径（或手动设置vscode设置项中的cmake.cmakepath为正确的cmake路径），否则vscode内的插件运行会出错。
- 其次安装cmake(作者twxs)和cmake tools（作者microsoft）两个vscode插件，微软官方的C++ extension pack内已包含。安装后会自动扫描可以用的工具链（kits），win10下扫描结果保存在C:\Users\zhi.zhang\AppData\Local\CMakeTools\cmake-tools-kits.json中，之后也可以在f1命令面板中选择cmake: scan for kits来添加新的工具链，注意工具链的c编译器所在目录要在当前系统路径里，否则搜索不到。
- 新建工程时可以用f1面板的cmake: quick start生成CMakeLists.txt，但这样会自动生成main.cpp文件，不需要的话可以手动删掉
- 手动修改CMakeLists.txt添加所需的文件，如以下代码把当前目录和sub1子目录下所有后缀名匹配的文件加入需要编译的文件列表，并把sub1子目录加入.h文件的搜索列表（当前目录缺省包含在搜索列表内），生成文件名为zztest的可执行文件
```cmake
aux_source_directory(. DIR_SRCS)
aux_source_directory(sub1 TEST_SRCS)

include_directories(sub1)
# message可用于检查变量的值
message ("files in .: ${DIR_SRCS}, files in sub1: ${TEST_SRCS}")

add_executable(zztest  ${DIR_SRCS} ${TEST_SRCS})
```
- 配置完成后，可以在左下角选择build生成可执行文件，选bug图标启动debug，三角图标执行，以及选择使用的kit，编译选项等等。
- 通过f1面板调出cmake: edit Cmake Cache(UI)，可以在不修改CMakeLists.txt的情况下，通过直接编辑build目录下的CMakeCache.txt，临时修改各种参数如编译选项（CMAKE_C_FLAGS_RELEASE，CMAKE_C_FLAGS_DEBUG等）设置，以及CMakeLists.txt中用option命令自定义的变量开关等等。

