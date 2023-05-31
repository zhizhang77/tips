[TOC]

原文链接：https://blog.csdn.net/afei__/article/details/81201039



## 一、Cmake 简介

cmake 是一个**跨平台、开源的构建系统**。它是一个**集软件构建、测试、打包于一身的软件**。它使用与平台和编译器独立的配置文件来对软件编译过程进行控制。



## 二、常用命令

### 1. 指定 cmake 的最小版本

```cmake
cmake_minimum_required(VERSION 3.4.1)
```

这行命令是**可选的**，我们可以不写这句话，但在有些情况下，如果 CMakeLists.txt 文件中使用了一些高版本 cmake 特有的一些命令的时候，就需要加上这样一行，**提醒用户升级到该版本之后再执行 cmake**。



### 2. 设置项目名称

```cmake
project(demo)
```

这个命令不是强制性的，但最好都加上。



**它会引入两个变量 demo_BINARY_DIR 和 demo_SOURCE_DIR**，

同时，cmake 自动定义了两个等价的变量 PROJECT_BINARY_DIR 和 PROJECT_SOURCE_DIR。



### 3. 设置编译类型

```cmake
add_executable(demo demo.cpp) # 生成可执行文件
add_library(common STATIC util.cpp) # 生成静态库
add_library(common SHARED util.cpp) # 生成动态库或共享库
```

add_library **默认生成是静态库**，通过以上命令生成文件名字，



**在 Linux 下是：**

demo

libcommon.a

libcommon.so



**在 Windows 下是：**

demo.exe

common.lib

common.dll



### 4. 指定编译包含的源文件

#### 4.1 明确指定包含哪些源文件

```cmake
add_library(demo demo.cpp test.cpp util.cpp)
```



#### 4.2 搜索所有的 cpp 文件

aux_source_directory(dir VAR) **发现一个目录下所有的源代码文件**并将列表存储在一个变量中。

```cmake
aux_source_directory(. SRC_LIST) # 搜索当前目录下的所有.cpp文件

add_library(demo ${SRC_LIST})
```



#### 4.3 自定义搜索规则

```cmake
file(GLOB SRC_LIST "*.cpp" "protocol/*.cpp")
add_library(demo ${SRC_LIST})
# 或者
file(GLOB SRC_LIST "*.cpp")
file(GLOB SRC_PROTOCOL_LIST "protocol/*.cpp")
add_library(demo ${SRC_LIST} ${SRC_PROTOCOL_LIST})
# 或者
file(GLOB_RECURSE SRC_LIST "*.cpp") #递归搜索
FILE(GLOB SRC_PROTOCOL RELATIVE "protocol" "*.cpp") # 相对protocol目录下搜索
add_library(demo ${SRC_LIST} ${SRC_PROTOCOL_LIST})
# 或者
aux_source_directory(. SRC_LIST)
aux_source_directory(protocol SRC_PROTOCOL_LIST)
add_library(demo ${SRC_LIST} ${SRC_PROTOCOL_LIST})
```



### 5. 查找指定的库文件

**find_library(VAR name path)** 查找到指定的预编译库，并将它的路径存储在变量中。

**默认的搜索路径为 cmake 包含的系统库**，因此如果是 NDK 的公共库只需要指定库的 name 即可。

```cmake
find_library( # Sets the name of the path variable.
              log-lib

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log )
```

类似的命令还有 find_file()、find_path()、find_program()、find_package()。



### 6. 设置包含的目录

```cmake
include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)
```

Linux 下还可以通过如下方式设置包含的目录

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I${CMAKE_CURRENT_SOURCE_DIR}")
```


### 7. 设置链接库搜索目录

```cmake
link_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/libs
)
```



Linux 下还可以通过如下方式设置包含的目录

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${CMAKE_CURRENT_SOURCE_DIR}/libs")
```



### 8. 设置 target 需要链接的库

```cmake
target_link_libraries( # 目标库
                       demo
                       # 目标库需要链接的库
                       # log-lib 是上面 find_library 指定的变量名
                       ${log-lib} )
```

在 Windows 下，系统会根据链接库目录，搜索xxx.lib 文件，Linux 下会搜索 xxx.so 或者 xxx.a 文件，如果都存在会优先链接动态库（so 后缀）。



#### 8.1 指定链接动态库或静态库

```cmake
target_link_libraries(demo libface.a) # 链接libface.a
target_link_libraries(demo libface.so) # 链接libface.so
```



#### 8.2 指定全路径

```cmake
target_link_libraries(demo ${CMAKE_CURRENT_SOURCE_DIR}/libs/libface.a)
target_link_libraries(demo ${CMAKE_CURRENT_SOURCE_DIR}/libs/libface.so)
```



#### 8.3 指定链接多个库

```cmake
target_link_libraries(demo
    ${CMAKE_CURRENT_SOURCE_DIR}/libs/libface.a
    boost_system.a
    boost_thread
    pthread)
```



### 9. 设置变量

#### 9.1 set 直接设置变量的值

```cmake
set(SRC_LIST main.cpp test.cpp)
add_executable(demo ${SRC_LIST})
```

#### 9.2 set 追加设置变量的值

```cmake
set(SRC_LIST main.cpp)
set(SRC_LIST ${SRC_LIST} test.cpp)
add_executable(demo ${SRC_LIST})
```

#### 9.3 list 追加或者删除变量的值

```cmake
set(SRC_LIST main.cpp)
list(APPEND SRC_LIST test.cpp)
list(REMOVE_ITEM SRC_LIST main.cpp)
add_executable(demo ${SRC_LIST})
```

#### 9.4 编译时设置宏定义
**target_compile_definitions**在编译target对应的依赖文件时自动加上`-DMY_DEFINITION`

```cmake
target_compile_definitions(target PRIVATE MY_DEFINITION)
```


### 10. 条件控制

#### 10.1 if…elseif…else…endif


**逻辑判断和比较：**

if (expression) ：expression 不为空（0,N,NO,OFF,FALSE,NOTFOUND）时为真

if (not exp) ：与上面相反

if (var1 AND var2)

if (var1 OR var2)



if (COMMAND cmd) ：如果 cmd 确实是命令并可调用为真

if (EXISTS dir) if (EXISTS file) ：如果目录或文件存在为真

if (file1 IS_NEWER_THAN file2) ：当 file1 比 file2 新，或 file1/file2 中有一个不存在时为真，文件名需使用全路径



if (IS_DIRECTORY dir) ：当 dir 是目录时为真

if (DEFINED var) ：如果变量被定义为真

if (var MATCHES regex) ：给定的变量或者字符串能够匹配正则表达式 regex 时为真，此处 var 可以用 var 名，也可以用 ${var}

if (string MATCHES regex)



**数字比较：**

if (variable LESS number)：LESS 小于

if (string LESS number)

if (variable GREATER number)：GREATER 大于

if (string GREATER number)

if (variable EQUAL number)：EQUAL 等于

if (string EQUAL number)



**字母表顺序比较：**

if (variable STRLESS string)

if (string STRLESS string)

if (variable STRGREATER string)

if (string STRGREATER string)

if (variable STREQUAL string)

if (string STREQUAL string)



示例：

```cmake
if(MSVC)
    set(LINK_LIBS common)
else()
    set(boost_thread boost_log.a boost_system.a)
endif()
target_link_libraries(demo ${LINK_LIBS})
# 或者
if(UNIX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -fpermissive -g")
else()
    add_definitions(-D_SCL_SECURE_NO_WARNINGS
    D_CRT_SECURE_NO_WARNINGS
    -D_WIN32_WINNT=0x601
    -D_WINSOCK_DEPRECATED_NO_WARNINGS)
endif()

if(${CMAKE_BUILD_TYPE} MATCHES "debug")
    ...
else()
    ...
endif()
```



#### 10.2 while…endwhile

```cmake
while(condition)
    ...
endwhile()
```



#### 10.3 foreach…endforeach

```cmake
foreach(loop_var RANGE start stop [step])
    ...
endforeach(loop_var)
```

start 表示起始数，stop 表示终止数，step 表示步长，示例：

```cmake
foreach(i RANGE 1 9 2)
    message(${i})
endforeach(i)
# 输出：13579
```



### 11. 打印信息

```cmake
message(${PROJECT_SOURCE_DIR})
message("build with debug mode")
message(WARNING "this is warnning message")
message(FATAL_ERROR "this build has many error") # FATAL_ERROR 会导致编译失败
```



### 12. 包含其它 cmake 文件

```cmake
include(./common.cmake) # 指定包含文件的全路径
include(def) # 在搜索路径中搜索def.cmake文件
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake) # 设置include的搜索路径
```



## 三、常用变量



### 1. 预定义变量

**PROJECT_SOURCE_DIR**：工程的根目录

**PROJECT_BINARY_DIR**：运行 cmake 命令的目录，通常是 ${PROJECT_SOURCE_DIR}/build

**PROJECT_NAME**：返回通过 project 命令定义的项目名称

**CMAKE_CURRENT_SOURCE_DIR**：当前处理的 CMakeLists.txt 所在的路径

**CMAKE_CURRENT_BINARY_DIR**：target 编译目录

**CMAKE_CURRENT_LIST_DIR**：CMakeLists.txt 的完整路径

**CMAKE_CURRENT_LIST_LINE**：当前所在的行

**CMAKE_MODULE_PATH**：
定义自己的 cmake 模块所在的路径，
```cmake
SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
```
，然后可以用INCLUDE命令来调用自己的模块

**EXECUTABLE_OUTPUT_PATH**：重新定义目标二进制可执行文件的存放位置

**LIBRARY_OUTPUT_PATH**：重新定义目标链接库文件的存放位置



### 2. 环境变量

使用环境变量
```cmake
 $ENV{Name}
```

写入环境变量
```cmake
 set(ENV{Name} value) # 这里没有“$”符号
```



### 3. 系统信息

­CMAKE_MAJOR_VERSION：cmake 主版本号，比如 3.4.1 中的 3

­CMAKE_MINOR_VERSION：cmake 次版本号，比如 3.4.1 中的 4

­CMAKE_PATCH_VERSION：cmake 补丁等级，比如 3.4.1 中的 1

­CMAKE_SYSTEM：系统名称，比如 Linux-­2.6.22

­CMAKE_SYSTEM_NAME：不包含版本的系统名，比如 Linux

­CMAKE_SYSTEM_VERSION：系统版本，比如 2.6.22

­CMAKE_SYSTEM_PROCESSOR：处理器名称，比如 i686

­UNIX：在所有的类 UNIX 平台下该值为 TRUE，包括 OS X 和 cygwin

­WIN32：在所有的 win32 平台下该值为 TRUE，包括 cygwin


### 4. 主要开关选项

**BUILD_SHARED_LIBS**：这个开关用来控制默认的库编译方式，如果不进行设置，使用 add_library 又没有指定库类型的情况下，默认编译生成的库都是静态库。**如果 set(BUILD_SHARED_LIBS ON) 后，默认生成的为动态库**

### 5. 常用编译选项配置

**CMAKE_C_FLAGS**：设置 C 编译选项，也可以通过指令 add_definitions() 添加，对它的修改会对所有build variant生效

**CMAKE_CXX_FLAGS**：同CMAKE_C_FLAGS，但影响 C++ 编译选项

```cmake
add_definitions(-DENABLE_DEBUG -DABC) # 参数之间用空格分隔
```

### 6. 交叉编译配置示例（基于VSCode加CMake tools插件）
#### 6.1 toolchain文件：
下面的文件用于windows下嵌入式MIPS处理器的交叉编译
```cmake
set(CMAKE_SYSTEM_NAME Generic) # 嵌入式用generic，其它系统还包括Linux，win32等
set(CMAKE_SYSTEM_PROCESSOR MIPS)

set(TOOLCHAIN_PREFIX nanomips-elf-)

set(BINUTILS_PATH "D:\\crosscompile\\nanomips-elf\\2019.03-06\\bin\\nanomips-elf-gcc")

get_filename_component(MIPS_TOOLCHAIN_DIR ${BINUTILS_PATH} DIRECTORY)
# Without that flag CMake is not able to pass test compilation check
if (${CMAKE_VERSION} VERSION_EQUAL "3.6.0" OR ${CMAKE_VERSION} VERSION_GREATER "3.6")
    set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)
else()
    set(CMAKE_EXE_LINKER_FLAGS_INIT "--specs=nosys.specs")
endif()

set(CMAKE_C_COMPILER ${MIPS_TOOLCHAIN_DIR}/${TOOLCHAIN_PREFIX}gcc.exe)
set(CMAKE_ASM_COMPILER ${CMAKE_C_COMPILER})
set(CMAKE_CXX_COMPILER ${MIPS_TOOLCHAIN_DIR}/${TOOLCHAIN_PREFIX}g++.exe)

# Default C compiler flags 后面所有variant编译时都会自动加上这些
set(CMAKE_C_FLAGS " -EL -march=i7200 -mclib=tiny -G128 -fgcse-sm -fgcse-las -T uhi32.ld -fdata-sections -ffunction-sections -Wl,-gc-sections -Wl,--defsym,__getargs=0 -Wl,--defsym,__exception_handle_verbose=0" CACHE STRING "" FORCE)

# CMAKE_C_FLAGS_DEBUG_INIT的内容会被自动包含到CMAKE_C_FLAGS_DEBUG里，下同
set(CMAKE_C_FLAGS_DEBUG_INIT "-g3 -Og -Wall -pedantic -DDEBUG")
set(CMAKE_C_FLAGS_RELEASE_INIT "-O3 -Wall")
set(CMAKE_C_FLAGS_MINSIZEREL_INIT "-Os -Wall")
set(CMAKE_C_FLAGS_RELWITHDEBINFO_INIT  "-O2 -g -Wall")
# Default C++ compiler flags
set(CMAKE_CXX_FLAGS_DEBUG_INIT "-g3 -Og -Wall -pedantic -DDEBUG")
# set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG_INIT}" CACHE STRING "" FORCE)
set(CMAKE_CXX_FLAGS_RELEASE_INIT "-O3 -Wall")
set(CMAKE_CXX_FLAGS_MINSIZEREL_INIT "-Os -Wall")
set(CMAKE_CXX_FLAGS_RELWITHDEBINFO_INIT  "-O2 -g -Wall")

set(CMAKE_OBJCOPY ${MIPS_TOOLCHAIN_DIR}/${TOOLCHAIN_PREFIX}objcopy.exe CACHE INTERNAL "objcopy tool")
set(CMAKE_SIZE_UTIL ${MIPS_TOOLCHAIN_DIR}/${TOOLCHAIN_PREFIX}size.exe CACHE INTERNAL "size tool")

set(CMAKE_SYSROOT ${MIPS_TOOLCHAIN_DIR}/../nanomips-elf)
set(CMAKE_FIND_ROOT_PATH ${MIPS_TOOLCHAIN_DIR})
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
```

#### 6.2 添加编译kit
```json
[
  {
    "name": "GCC 8.1.0 x86_64-w64-mingw32",
    "compilers": {
      "C": "D:\\tools\\MinGW\\bin\\x86_64-w64-mingw32-gcc.exe",
      "CXX": "D:\\tools\\MinGW\\bin\\x86_64-w64-mingw32-g++.exe"
    },
    "preferredGenerator": {
      "name": "MinGW Makefiles"
    },
    "environmentVariables": {
      "CMT_MINGW_PATH": "D:\\tools\\MinGW\\bin"
    }
  },
  {
    "name": "ARM-none-EABI",
    "toolchainFile": "D:\\crosscompile\\gcc-arm-none-eabi-10.3-2021.07\\armtoolchain.cmake"
  },
  {
      # 前文的toolchain文件路径，name可以随便起
    "name": "nanoMIPS",
    "toolchainFile": "D:\\crosscompile\\nanomips-elf\\mipstoolchain.cmake"
  }
]
```

#### 6.3 CMakeLists.txt示例
下面的CMakeLists.txt支持Windows/ARM/MIPS的编译，并在MIPS下修改编译选项，增加额外的文件和宏定义：
```cmake
cmake_minimum_required(VERSION 3.0.0)
project(nrtest VERSION 0.1.0)

include(CTest)
enable_testing()

set(DIR_LIST AFC AGC ATC COMMON MEAS PAL RRC SYNC TEST)

set(ALL_SRCS)

foreach(_module ${DIR_LIST})
    include_directories(${PROJECT_SOURCE_DIR}/${_module}/INC)
    aux_source_directory(${PROJECT_SOURCE_DIR}/${_module}/SRC ${_module}_SRC)
    set(ALL_SRCS ${ALL_SRCS} ${${_module}_SRC})
    message ("files in ${_module}: ${${_module}_SRC}")
endforeach(_module)

if (${CMAKE_SYSTEM_PROCESSOR} MATCHES MIPS)
    include_directories(${PROJECT_SOURCE_DIR}/MIPS)
    aux_source_directory(${PROJECT_SOURCE_DIR}/MIPS MIPS_SRC)
    set(ALL_SRCS ${ALL_SRCS} ${MIPS_SRC})
    message ("MIPS specific: ${MIPS_SRC}")
else()
    message ("system：${CMAKE_SYSTEM_PROCESSOR}")
endif()

add_executable(nrtest  ${ALL_SRCS})

if (${CMAKE_SYSTEM_PROCESSOR} MATCHES MIPS)
    target_compile_definitions(nrtest PRIVATE MIPS_DEBUG)
    message ("MIPS_DEBUG enabled!")
endif()


set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)

```