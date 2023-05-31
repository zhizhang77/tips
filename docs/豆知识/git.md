# git相关
[TOC]
## 快速下载github repo
### 导入国内
以opencv为例，在码云用户“Gitee 极速下载”repo中找到对应连接的镜像（如果没有就自己导入一个），然后clone：
```sh
git clone https://gitee.com/mirrors/opencv.git
```
进入opencv目录，删除原origin并把github设为origin：
```sh
git remote remove origin
git remote add origin https://github.com/opencv/opencv.git
```
更新并强制覆盖：
```sh
git fetch --all
git reset --hard origin/master
git pull
```
### 设置代理
设置127.0.0.1：1080为全局代理：
```sh
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```
代理服务器需要鉴权配置：
```sh
git config --global https.proxy https://username:password@proxy.baidu.com:8080
```
注意如果密码中有@等特殊字符，会出错。此时要使用百分比编码(Percent-encoding)对特殊字符进行转换，转换列表如下：
> ! --> %21       # --> %23     $ --> %24     & --> %26     ' --> %27  
> ( --> %28       ) --> %29      * --> %2A     + --> %2B     , --> %2C  
> / --> %2F      : --> %3A      ; --> %3B      = --> %3D     ? --> %3F  
> @ --> %40     [ --> %5B     ] --> %5D  

删除代理：
```sh
git config --global --unset http.proxy
git config --global --unset https.proxy
```



## submodule

### 添加
首先在主项目内添加子项目：
```sh
git submodule add git@192.168.8.41:zhizhang/subtest1.git algorithm
git submodule add git@192.168.8.41:zhizhang/subtest2.git toolset
```

运行后，git会将相应代码clone到本地。之后进入相应目录，切换到所需的分支或标签即可。
```sh
cd algorithm
git checkout tag1_master
```

### 更新
commit和加tag时会将子模块版本锁定在当前位置，除非特意更新。可以用foreach更新所有submodule：
```sh
git submodule foreach 'git pull origin master'
```

针对单个子模块，可以进入对应目录内pull
```sh
cd project-sub-1
git pull origin master
```
之后子模块目录下的代码版本会发生变化，这时回到主目录git status可以看到子模块有新的提交，使用 git add/commit 将其添加到主项目的代码提交中即可，实际改动就是.gitmodules文件中保存的子模块版本信息。

### 获取
通常主项目使用clone 操作并不会拉取到子模块中的实际代码，如果需要，可以用*--recurse-submodules*参数
```sh
git clone https://github.com/username/project-main.git --recurse-submodules
```

如果主项目已下载，可以用*submodule update --init*(等价于*submodule init*加*submodule update*)，如果子模块目录中已经有内容，该命令也会把其更新到.gitmodules中指定的版本
```sh
git submodule update --init
```

### 卸载
根据官方文档的说明，应该使用 *git submodule deinit* 命令卸载一个子模块。这个命令如果添加上参数 *--force*或者 *-f*，则子模块工作区内即使有本地的修改，也会被移除。

然鹅实测只靠*git submodule deinit*是删不干净的，参考 https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule/36593218#36593218 ，需要手动删除.git/modules目录下的对应文件夹然后再*git rm*，如下：
```sh
# Remove the submodule entry from .git/config
git submodule deinit project-sub-1
# Remove the submodule directory from the superproject's .git/modules directory
rm -rf .git/modules/project-sub-1
# Remove the entry in .gitmodules and remove the submodule directory located at path/to/submodule
git rm -f project-sub-1
```

## 只下载最新的x个版本
### --depth
一个repo维护时间久了之后，会有大量的历史提交，使得整个版本库体积变的很大。但绝大部分时候，开发者其实并不关注历史更改，只要下载最新版本就行了，这时候可以用 *--depth x*来控制只拉取最近的x层提交：
```sh
git clone --depth 1 --branch ttt https://github.com/xxx/yyy.git
```
以上命令会clone yyy项目在ttt分支上的最新版本。需要注意的是1是层数而非总数，例如，若最后一次提交时merge了三个提交，那么 *--depth 2*一共会拉取4次提交而不是2次。
### --unshallow
如果用 *--depth*浅层克隆之后又需要下载完整版本，可以用 *--unshallow*恢复：
```sh
git fetch --unshallow
```

## 合并不必要的提交

大量零碎的commit会使版本历史变得混乱，不利于查找，这时可以用rebase来合并多次commit的历史。
```sh
# 包括HEAD版本在内，最近的3个版本会被拉出来询问是否合并
git rebase -i HEAD~3

# 合并指定版本号（不包含此版本）
# 注意commitid应该是要合并的提交中最早的，例如连续三次提交id分别为111，222，333，要合并它们，需要
git rebase -i 111
```
运行后会进入vi编辑器(如果是vscode会跳出编辑窗口)，如下，将不需要的commit从pick改为s(quash)或f(ixup)，然后:wq保存退出
```sh
# 指定要合并版本号，cf7e875 不参与合并，进入 vi 编辑器
$ git rebase -i cf7e875 
pick 17cb931 fix && add batch del
pick e57b0e6 fix && add batch del
pick 2db6ad3 add clear logs scripts
pick 3a51aaa fix shellcheck problem
pick 9536dab add dingtalk script
pick 1d795e6 fix && update clear-logs.sh 0.0.2
pick fc36a2a add links
pick 3759b84 update clear-logs.sh
pick 1693a6f update clear-logs.sh version
pick 8c8f3f4 update website

# Rebase cf7e875..291e427 onto cf7e875 (10 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```
然后会进入commit信息修改界面，改完:wq就可以了。

> “No one shall rebase a shared branch”——否则其他人合并时会很痛苦

## Rebase和merge的区别

Rebase means *reapply commits **on top of** another base tip*。也就是在新base上把自己的改动重新apply一遍
![rebase](../images/rebase.png)
Merge means *Join two or more development histories together*。原先的改动都保留不变，产生一个新的提交把两边的改动都合进去
![merge](../images/merge.png)

## 使用stash暂存和恢复当前工作

`git stash`命令用于暂时保存没有提交的工作。运行该命令后，所有已提交到暂存区，以及没有提交的修改都将被内部保存，工作区恢复到上一次commit的状态。它处于`git reset --hard`（完全放弃还修改了一半的代码）与`git commit`（提交代码）命令之间，很类似于“暂停”按钮。

同时维护多个分支的情况下，用stash可以实现多个任务间的灵活切换。例如：在分支A改到一半时暂存，切换到分支B修改并commit，然后pop继续工作。

```shell
# 暂时保存没有提交的工作
$ git stash
warning: LF will be replaced by CRLF in 豆知识/jupyter.ipynb.
The file will have its original line endings in your working directory
Saved working directory and index state WIP on main: 6645a0b add readme.md

# 列出所有暂时保存的工作
$ git stash list
stash@{0}: WIP on main: 6645a0b add readme.md
stash@{1}: WIP on main: 6645a0b add readme.md

# 恢复某个暂时保存的工作，恢复时会与当前工作区的代码合并，注意保存越早的stash数字越大，0是最新缓存的那个
$ git stash apply stash@{1}

# 恢复最近一次stash的文件，它会与当前工作区的代码合并
$ git stash pop

# 丢弃最近一次stash的文件
$ git stash drop

# 丢弃倒数第二个stash
$ git stash drop stash@{1}

# 删除所有的stash
$ git stash clear

# 展示目前存在的stash，如果加 -p ，会显示具体改动内容并进入vi编辑页面
$ git stash show
```

## 同一机器上使用多个github账号
假设要添加的github账号名是my2ndgitaccount，注册邮箱为my2ndname@companyname.com.cn。

- 首先为新账号生成ssh key，起名为id_companyname并添加
```sh
# 生成key时建议与github注册时的邮件地址一一对应，方便查找
$ ssh-keygen -t ed25519 -C "my2ndname@companyname.com.cn"
# /c/Users/myname/.ssh/id_companyname是刚刚生成的key文件名和路径
$ ssh-add  /c/Users/myname/.ssh/id_companyname
```

- 在my2ndgitaccount的github用户页面找到SSH and GPG keys，选择new SSH key，把生成的id_companyname.pub内容完整粘贴进去保存。
- 下一步，编辑本地.ssh目录中的config文件，添加以下段落
```text
Host host2nd # 名字随便起
    HostName github.com # 这行和下行不要改
    User git
    IdentityFile C:\Users\myname\.ssh\id_companyname # 使用刚刚生成的key文件名和路径，注意windows和linux路径格式不同
```

- 对于要使用这个账号操作的repo，首先看全局设置中的user.name和user.email是否与该账号一致，如果不一致，则使用$git config --local$设置仅对当前目录生效的user.name和user.email，
```sh
git config --list
git config --local user.email my2ndname@companyname.com.cn
git config --local user.name my2ndgitaccount
git config --list --local
```

- 之后就可以使用$host2nd$的名字添加远程并发布了
```sh
git remote add origin git@host2nd:my2ndgitaccount/myrepo.git
# 对比：用缺省账号发布时就不需要加git@host2nd：
# git remote add origin https://github.com/my2ndgitaccount/myrepo.git
git push -u origin main
```

- 如果克隆my2ndgitaccount的repo时指明使用$host2nd$，后续可以直接修改和push。但如果没有设置user.name和user.email，github上看到的修改记录会是来自主账号而非my2ndgitaccount，这点需要注意
```sh
git clone git@host2nd:my2ndgitaccount/myrepo.git
```

## 同一机器上连接github和其它服务器
首先确认全局用户名，email等已清除：

```sh
git config --system --unset user.name
git config --system --unset user.email
git config --system --unset credential.helper
```

配完后通过git config -l检查这三项确实没了。然后为每个服务器生成密钥并起不同的名字，将其放到ssh目录（windows下在C:\Users\用户名\.ssh）下：

```sh
ssh-keygen -t rsa -C "youremail@example.com"
```

在服务器侧添加对应的ssh密钥。最后在ssh目录下创建config文件，内容示例：

```
    # 该配置用于工作
    # Host 服务器别名
    Host 192.168.8.41
    # HostName 服务器ip地址或机器名
    HostName 192.168.8.41
    # User连接服务器的用户名
    User zhizhang
    # IdentityFile 密匙文件的具体路径
    IdentityFile C:/Users/HP/.ssh/id_rsa_gitlab


    # 该配置用于github
    # Host 服务器别名
    Host github.com
    # HostName 服务器ip地址或机器名
    HostName github.com
    # User连接服务器的用户名
    User zhizhang77
    # IdentityFile 密匙文件的具体路径
    IdentityFile C:/Users/HP/.ssh/id_rsa
```

之后提交时还有可能会要求输入用户名和email，如果不想每次都输入，可以在repo所在目录下设置不带--global的用户信息，这样该用户名和电邮就只对当前repo生效了

```sh
    git config user.email "you@example.com"
    git config user.name "Your Name"
```

## 一些git和github小技巧
- https://github.com/tiimgreen/github-cheat-sheet

## gitk中文乱码问题解决
1. 打开gitk->file菜单->start git gui
2. git gui->edit菜单->options
3. 将local和global的default file contents encoding都改为utf-8
4. 重启gitk

## github图片显示和文件下载出错

### 改hosts
先到https://www.ipaddress.com/ 输入raw.githubusercontent.com查询到真实IP地址，ping一下选择延迟最小的，然后手动修改hosts加映射
 - windows系统的hosts文件在C:\Windows\System32\drivers\etc目录下
 - linux在etc目录下

当然最简单的方法是翻墙~

### 用cloudflare worker或python对github进行转发
https://github.com/hunshcn/gh-proxy
现成站点：
https://gh.api.99988866.xyz/
https://g.ioiox.com
http://toolwa.com/github/