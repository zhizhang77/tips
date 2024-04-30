# OS相关
[TOC]
## windows+ubuntu双系统时间不一致
  - 问题：同一机器上安装windows+ubuntu双系统时，两个系统时间相差八小时
  - 原因：windows缺省使用系统时间（东八区），而Linux缺省使用UTC
  - 解决方案：windows下启动regedit，在 *HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/TimeZoneInformation/*下增加类型为 *REG_DWORD*的键值，命名为*RealTimeIsUniversal*，值为1， 保存后重启即可

## Ubuntu启动时自动执行脚本的几种方式

Ubuntu启动时自动执行脚本有几种方式：

- /etc/profile:在登录时,操作系统定制用户环境时使用的第一个文件 ,此文件为系统的每个用户设置环境信息,仅当用户**第一次**登录时,该文件被执行。该文件会遍历/etc/profile.d/目录下的每个文件并执行，所以通常不推荐直接修改profile本身，而是把要执行的脚本作为独立文件放到/etc/profile.d/里，这样需要恢复原状时只要删除该文件就可以了

- /etc/environment:在登录时操作系统使用的第二个文件, 系统在读取你自己的profile前,设置环境文件的环境变量。

- ~/.profile: 在登录时用到的第三个文件是.profile文件,每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。

- /etc/bashrc:为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被执行。

- ~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及**每次**打开新的shell时,该文件被执行。

## 不卸载程序的情况下防止其服务开关机启动

正常情况下，systemd开机启动的服务会被标为enabled，可以用systemctl列出：
```sh
systemctl list-unit-files --type=service | grep enabled
```
直接用`sudo systemctl disable xxx.service`就能防止其开机启动。但实际上，disable的服务进程仍然可能被另外一个服务进程唤起。如果希望完全禁止该进程启动，同时不卸载程序，可以把它mask起来：
```sh
sudo systemctl mask xxx.service
```
mask会把该服务的符号链接指向/dev/null，这样任何唤起请求都不生效。

某些程序如mysql还会在安装卸载时都强行启动服务状态检查，这样如果安装出问题，软件就无法卸载。这种情况下也可以先把其mask掉再卸。

另外一个开机自启动的途径是把链接放到 /etc/init.d 目录内。

## 设置打印机

Ubuntu的设置->打印机有时会出错打不开，这时可以直接通过CUPS（Common UNIX Printing System）的网页界面http://localhost:631 来实现管理，操作时如果提示输入用户名密码，用Linux账户即可

## win10下使用sfc(system file checker)修复系统文件

用admin账户打开命令行或者powershell，然后运行`sfc /scannow`，该命令会自动校验系统文件并修复，可以解决一些奇奇怪怪的异常现象。执行完之后在提示位置的CBS.log中可以看到受影响的文件列表。

另一个命令是`DISM.exe /Online /Cleanup-Image /Restorehealth`，$/Online$表示检查当前运行中的系统，$/Cleanup-Image$表示系统还原，$/Restorehealth$表示修复损坏的文件。

## windows下解决端口占用

在cmd中输入`netstat -ano|findstr 端口号`
```powershell
C:\Users\Administrator>netstat -ano|findstr 8080
  TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       1236
  TCP    0.0.0.0:8080           192.168.1.100:49706      ESTABLISHED     1236
```
  
  可以看到占用8080端口的进程号是1236，然后用`tasklist|findstr 1236`来查看该进程的名称，比如是`java.exe`，然后用`taskkill /f /im java.exe`来结束该进程。也可以直接`taskkill /pid 占用端口的进程号`。

