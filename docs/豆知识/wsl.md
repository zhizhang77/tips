# WSL相关
[TOC]
## 从WSL1升级到WSL2

参考https://docs.microsoft.com/zh-cn/windows/wsl/install-win10#set-your-distribution-version-to-wsl-1-or-wsl-2。基本流程：

1. 启用WSL：用管理员权限打开powershell并输入

   ```powershell
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   ```

   

2. 确定win10已升级到版本 1903 或更高版本

3. 安装 WSL 2 之前，必须启用“虚拟机平台”可选功能。 计算机需要[虚拟化功能](https://docs.microsoft.com/zh-cn/windows/wsl/troubleshooting#error-0x80370102-the-virtual-machine-could-not-be-started-because-a-required-feature-is-not-installed)才能使用此功能。以管理员身份打开 PowerShell 并运行：

   ```powershell
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```

   

4. 下载并安装[适用于 x64 计算机的 WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

5. 将 WSL 2 设置为默认：

   ```powe
   wsl --set-default-version 2
   ```

   

6. 使用`wsl -l -v`可以看到当前安装的系统名称和使用的wsl版本，如果想修改所用的wsl版本，可以使用命令`wsl --set-version linuxname wslversion`，其中linuxname要跟`wsl -l -v`显示的name一致，wslversion就是数字1或者2。转换过程需要一些时间。

   

## win10访问WSL2文件

WSL2使用虚拟机实现，启动后可以在文件管理器里访问\\\wsl$来操作其内部文件，其它方式都应避免。wsl访问windows文件可以直接访问/mnt/\<drivename>，启动时缺省都已经mount。

## powershell执行wsl

powershell可以直接运行linux命令，在命令前面加wsl即可，例如`wsl sudo apt update`，如果有多个发行版，可以用`wslconfig /setdefault <DistributionName>`设置缺省运行的版本。

也可以把windows和linux指令混合使用，如`dir | wsl grep git`



## locale错误

现有WSL里没有自带中文，安装某些包（如flask）时会因为locale错误而失败，装上就好了


    sudo apt install language-pack-zh-hans

## VSCode远程不工作

在wsl里的工作目录输入code .提示命令code不存在，这时候在win10系统变量的path里加上 **(vscode所在目录)/bin** 就可以了

## 在WSL下使用Windows的字体库

在WSL中/etc/fonts下创建文件`local.conf`：
```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
    <dir>/mnt/c/Windows/Fonts</dir>
</fontconfig>
```

## 将指定的wsl系统迁移到其它目录

```powershell
PS D:\> wsl --shutdown
PS D:\> wsl --list --verbose
  NAME                   STATE           VERSION
* Ubuntu-20.04           Stopped         2
  docker-desktop         Stopped         2
  docker-desktop-data    Stopped         2
PS D:\> wsl --export Ubuntu-20.04 D:\tools\wsl\u.tar
正在导出，这可能需要几分钟时间。
操作成功完成。
PS D:\> wsl --unregister Ubuntu-20.04 #注意该操作会完全删除原有的数据，操作前应确认备份已成功完成。
正在注销。
操作成功完成。
PS D:\> wsl --import Ubuntu-20.04 D:\tools\wsl\ D:\tools\wsl\u.tar
正在导入，这可能需要几分钟时间。
操作成功完成。
PS D:\> ubuntu2004.exe config --default-user yourusername # 迁移后缺省的登录用户是root，用这个改为你想要的用户，不同发布版文件名不同。
```
如果原系统集成了docker desktop，迁移后需要在docker desktop的Settings->Resources->WSL integration里重新打开。