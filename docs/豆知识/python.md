# python相关
[TOC]

## 官方提供的Windows portable版本
3.10之后官方提供了Windows下的便携版本，直接到https://www.python.org/downloads/windows/ ，选择Windows embeddable package下载并解压，使用时通过绝对路径调用python.exe。

### 安装pip
portable版本不带pip，如果需要，下载https://bootstrap.pypa.io/get-pip.py ，然后到python解压目录下执行`python.exe pathto\get-pip.py`即可，安装的所有包都会存储在解压目录下。

### 修改`python3xx._pth`
portable版本的搜索路径在`python3xx._pth`里指定（xx是python版本号），缺省只有`python3xx.zip`和`.`目录，所以pip装好之后运行`python -m pip xxx`还是会显示**ModuleNotFoundError: No module named 'pip'**。解决办法是修改`python3xx._pth`，在文件末尾添加一行`import site`，这样pip安装的包就会自动添加到搜索路径中。

## python打包工具
打包尽量用Python Embeddable版本，比较干净，不容易出依赖问题
- pyinstaller
```sh
pip install pyinstaller
```
- nuitka，需要安装c++编译器，生成的包运行速度通常更快
```sh
pip install nuitka
```

## 多python版本共存时使用pip

用`python版本号 -m pip`的方法可以保证在正确的python版本下操作：

```sh
python3.7 -m pip install numpy
```

## 豆瓣源地址

```sh
sudo -H pip install numpy -i http://pypi.douban.com/simple/ --trusted-host=pypi.douban.com/simple
# 或者
sudo -H pip install numpy -i https://pypi.doubanio.com/simple
# 清华源较慢但更稳定
sudo -H pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
```
也可将其设为缺省源
```sh
pip config set global.index-url https://pypi.doubanio.com/simple
```

## 用pip下载包并离线安装
`pip download -d ./packages/ -r requirements.txt`
把requirements.txt中指定的所有包及其依赖全部下载保存到./packages/目录下，然后使用
`pip install --no-index --find-links=./packages/ -r requirements.txt`
安装这些包，这样在离线环境下也能正常运行。


## 小知识和语法糖

### 字符串和列表通用的逆序方法

```python
a = [1,2,3,4]
b = a[::-1]
print(b)
c = 'how dare you'
d = c[::-1]
print(d)
```

### 有放回随机采样和无放回随机采样

```python
import random
random.choices(seq, k=1)  # 长度为k的list，有放回采样
random.sample(seq, k)     # 长度为k的list，无放回采样
```

### 大数字中间可以加下划线

```python
bn = 123_456_789 # bn = 123456789
```

### 三元运算符

```python
max = a if a > b else b 
```

### == 和 is

```python
x == y  # 两引用对象是否有相同值
x is y  # 两引用是否指向同一对象，所以判断x为空必须是x is None
```

### 无限值

```python
a = float('inf')
b = float('-inf')
```

### zip打包

```python
list1 = [1, 2, 3] 
list2 = ['a', 'b', 'c'] 
for x, y in zip(list1, list2):
    print(x, y)
```

### 把操作map到列表或可迭代对象上
```python
slist = [x**2 for x in range(1, 6)]     # 结果是列表[1, 4, 9, 16, 25]
sdict = {x: x**2 for x in range(1, 6)}  # 结果是字典{1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

### iter排序和排列组合

```python
sorted(iterable, key=None, reverse=False)

itertools.groupby(iterable, key=None)              # 按值分组，iterable需要先被排序
# groupby(sorted([1, 4, 6, 4, 1])) -> (1, iter1), (4, iter4), (6, iter6)

itertools.permutations(iterable, r=None)           # 排列，返回值是Tuple
# permutations('ABCD', 2) -> AB, AC, AD, BA, BC, BD, CA, CB, CD, DA, DB, DC

itertools.combinations(iterable, r=None)           # 组合，返回值是Tuple
itertools.combinations_with_replacement(...)
# combinations('ABCD', 2) -> AB, AC, AD, BC, BD, CD
```

### enumerate实现加索引的枚举

```python
fruits = ['apple', 'banana', 'mango'] 
for index, fruit in enumerate(fruits): 
    print(index, fruit)
```

### 调用外部命令并检查返回结果

```python
import subprocess
# 如果外部命令返回值非0，则抛出subprocess.CalledProcessError异常
result = subprocess.check_output(['cmd', 'arg1', 'arg2']).decode('utf-8')  
# 同时收集标准输出和标准错误
result = subprocess.check_output(['cmd', 'arg1', 'arg2'], stderr=subprocess.STDOUT).decode('utf-8')  
# 执行shell命令（管道、重定向等），可以使用shlex.quote()将参数双引号引起来
result = subprocess.check_output('grep python | wc > out', shell=True).decode('utf-8')
```

### 函数可以用 * 和 ** 运算符传递多个函数参数。* 包含所有不带名字的参数， ** 包含所有带名字的参数

```python
def print_arguments(*args, **kwargs):
    print(args)
    print(kwargs)

print_arguments(1, 2, 3, name='John', age=30)
# 输出：
# (1, 2, 3)
# {'name': 'John', 'age': 30}
```

### 用装饰器对函数进行封装可以不影响其它代码

```python
def log_function(func):
    def wrapper(*args, **kwargs):
        print(f'Running {func.__name__}')
        result = func(*args, **kwargs)
        print(f'{func.__name__} returned {result}')
        return result
    return wrapper

@log_function
def add(x, y):
    return x + y
```

### 错误、警告和调试信息

#### 错误

```python
import sys
sys.stderr.write('')
```

#### 警告

```python
import warnings
warnings.warn(message, category=UserWarning)  
# category的取值有DeprecationWarning, SyntaxWarning, RuntimeWarning, ResourceWarning, FutureWarning
```

#### 控制警告消息的输出

```shell
$ python -W all     # 输出所有警告，等同于设置warnings.simplefilter('always')
$ python -W ignore  # 忽略所有警告，等同于设置warnings.simplefilter('ignore')
$ python -W error   # 将所有警告转换为异常，等同于设置warnings.simplefilter('error')
```

#### 只在调试时执行的语句

```python
# 在代码中的debug部分
if __debug__:
    pass
```

通过在命令行执行 -O 选项，会忽略这部分代码
```shell
$ python -0 main.py
```

### 查看编译后的代码
```python
import dis

def add10(x):
    return x+10

dis.dis(add10)
```

### 代码耗时测试

```shell
$ python -m cProfile main.py
```
