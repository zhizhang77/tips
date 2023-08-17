## 获取处理器信息
```sh
# 逐个胪列CPU信息
cat /proc/cpuinfo
# 多核CPU信息汇总显示
lscpu
```

## 获取处理器状态
```sh
# 系统支持的CPU核心数
cat /sys/devices/system/cpu/present
# 在线的核心数
cat /cat sys/devices/system/cpu/online
# cpu0的硬件线程数
cat /sys/devices/system/cpu/cpu0/topology/thread_siblings_list
# 当前工作频率（部分系统里不支持，下同）
cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_cur_freq
# 最大工作频率
cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_max
# 最小工作频率
cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_min
```

## 获取各个进程状态和资源占用
```sh
ps aux
top
```

## 基于Non-Uniform Memory Access (NUMA) 的CPU配置

### taskset(ubuntu自带)
```sh
# 查看mytaskid可用的核心列表，分号枚举格式
taskset -pc mytaskid
# 查看mytaskid可用的核心列表，掩码格式（0b1表示cpu0，0b10表示cpu1，等等）
taskset -pc mytaskid
# 设置mytaskid只能跑在#1，#2核心上
taskset -pc 1,2 mytaskid
# 设置mytaskid只能跑在#1~#3上
taskset -p 7 mytaskid
# 启动新任务mytask并限制其只能跑在cpu0和2上
taskset --cpu_list 0,2 mytask
```

### numactl可以控制的内容更广，但需要额外安装
```sh
numactl --membind=0,1 application.exe
```

