# Python性能优化

## Cyphon

## Numba

    pip install numba tbb

主要用于循环，很多场景下一个@njit（等价于@jit(nopython=True)）装饰符就可以有很好的效果，用于显式并行的@vectorize甚至可以加速numpy，见numba的notebook

## Taichi

    pip install taichi

对taichi scope（也就是@ti.func和@ti.kernel修饰的函数）内部的代码先编译再运行，对GPU后端调用更加友好，自称对非矩阵的加速效果也比numba更好。几点hint：
- 用arch=ti.xx(xx可以是cpu gpu cuda metal opengl vulkan dx11)在ti.init中指定后端，`arch=ti.gpu`会自动选择可用的gpu后端并在没有可用选项时回退到cpu
- taichi有自己的数据类型，包括标量（ti.i32, ti.f32之类），ti.Matrix和ti.Vector
- taichi scope中可以使用python的全局变量，但实际上，taichi只是在初次调用某个kernel时将其复制给kernel内部的同名变量，如果kernel运行后该全局变量发生变化，该kernel内的值不会跟着变，反过来也一样，见下例：
```python
import taichi as ti
ti.init(arch=ti.gpu)

a = 1

@ti.kernel
def kernel_1() -> int:
    a = a+2
    return a

@ti.kernel
def kernel_2() -> int:
    return a

print(kernel_1()) # 3
a = 2
print(kernel_1()) # 3 注意kernel_1中的a = a+2和前一句的a = 2都没有生效
print(kernel_2()) # 2
```
- 每个@ti.kernel都独立编译执行，其入参和出参的类型都必须显式指明
- 每个@ti.kernel最多只能有一个返回出口和一个返回值（可以没有），类似`if a return b else return c`或者`return x1,x2`都是错误的
- @ti.kernel入参数量有限制，不同后端支持的数量不同
- @ti.function只能被@ti.kernel调用，对外部python代码不可见
- 前面几条对@ti.kernel的限制对@ti.function都不存在，但依然推荐显式指明入参和出参的类型

例程：
```python
## 用##开头的是使用numba的加速代码，方便对比，实测使用cpu时两者速度相当
## from numba import njit, config, prange, int32
## config.NUMBA_DEFAULT_NUM_THREADS=4 # 并行度不是越高越好，通常不超过CPU核数

import taichi as ti
ti.init(arch=ti.gpu)

## @njit(int32(int32))
@ti.func
def is_prime(n: int):
    result = True
    for k in range(2, int(n ** 0.5) + 1): ## numba下改成prange
        if n % k == 0:
            result = False
            break
    return result

## @njit(int32(int32),parallel=True,nogil=True) # 开启并行
@ti.kernel
def count_primes(n: int) -> int:
    count = 0
    for k in range(2, n):  ## numba下改成prange
        if is_prime(k):
            count += 1
    return count

%time count_primes(5000000)
```

## map函数

map() 将提供的函数应用于指定序列的所有元素。相比循环快非常多，python3之后返回iterable，可以用list()转换成列表。  
注意很多时候使用map优化后，numba的效果就很有限了


```python
# from numba import jit,njit

# @njit
def a(x):
    sum = 0
    for i in range(1,x):
        kk = i**(-0.5)
        sum += kk
    return sum

n = 10001
%timeit a(n)
%timeit map(a,(n,n))

z = map(a,(n,n,n,n,n))
list(z)
```

## reduce函数

reduce() 函数对参数序列（链表，元组等）中的元素进行累积：先用传给 reduce 中的函数 function（有两个参数）对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，直到结束。有一定加速效果，且相比循环更为清晰可读


```python
from functools import reduce
def a1(x,y):
    return x+y**(-0.5)
%timeit reduce(a1,range(1,101))

def a2():
    t = a1(1,2)
    for i in range(3,101):
        t = a1(t,i)
%timeit a2()
```

