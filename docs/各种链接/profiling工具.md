# profiling工具：


## python

### cprofile

cprofile是python自带，不需要安装，可以用如下命令行执行，也可以在python代码内部执行

```sh
  # 直接把分析结果打印到控制台
  python -m cProfile test.py
  # 把分析结果保存到文件中
  python -m cProfile -o result.out test.py
  # 增加排序方式
  python -m cProfile -o result.out -s cumulative test.py
```


```python
import time
def func1():
    sum = 0
    for i in range(100000):
        sum += i
def func2():
    time.sleep(10)

def test():
    func1()
    func2()
```


```python
import cProfile

cProfile.run("test()")
#cProfile.run("test()", filename="result.out") # 结果保存到文件
# cProfile.run("test()", filename="result.out", sort="cumulative") # 结果保存到文件并排序
```

### pstats

pstats也是内置模块，用于解析cProfile的输出文件，可以用

    python -m pstats result.out

交互式使用，也可以在python代码中使用


```python
import pstats

# 创建Stats对象
p = pstats.Stats("result.out")

# strip_dirs(): 去掉无关的路径信息
# sort_stats(): 排序，支持的方式和上述的一致
# print_stats(): 打印分析结果，可以指定打印前几行

# 和直接运行cProfile.run("test()")的结果是一样的
p.strip_dirs().sort_stats(-1).print_stats()

# 按照函数名排序，只打印前3行函数的信息, 参数还可为小数,表示前百分之几的函数信息 
p.strip_dirs().sort_stats("name").print_stats(3)

# 按照运行时间和函数名进行排序，实际支持的排序方法包括calls, cumulative, file, line, module, name, nfl, pcalls, stdname, time
p.strip_dirs().sort_stats("cumulative", "name").print_stats(0.5)

# 如果想知道有哪些函数调用了sum_num
p.print_callers(0.5, "sum_num")

# 查看test()函数中调用了哪些函数
p.print_callees("test")
```

### snakeviz

cProfile输出的可视化工具

    pip install snakeviz

直接用它打开输出文件即可

    snakeviz result.out

## C++

### gperftools

https://github.com/gperftools/gperftools

### gprof
[GNU binutils](https://www.gnu.org/software/binutils/)自带，[手册地址](https://sourceware.org/binutils/docs-2.39/gprof.html)。

## 通用

### perf
强大但太复杂不好摘要，建议现用现搜……