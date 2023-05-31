# qt
[TOC]

## qt项目生成makefile：
在.pro所在目录下使用qmake可以生成makefile：
```sh
qmake -makefile *.pro
```
有时候会提示 `Could not find qmake spec 'linux-g++'.` 之类，这是因为系统引用了错误位置的qmake，用：
```sh
which -a qmake
```
看看有多少个qmake，加上正确的路径就行了，正确路径通常是
```sh
/usr/bin/qmake *.pro -o makefile
```

另外，用`qmake -v`可以看到其对应的qt版本和位置，类似：
> QMake version 2.01a  
> Using Qt version 4.8.7 in /usr/lib/x86_64-linux-gnu

`qmake -query`则可以显示当前的配置和目录信息
