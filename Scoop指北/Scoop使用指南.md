## Scoop使用指南
### 环境要求
- Windows 版本不低于 Windows 7

- [PowerShell 5](https://p3terx.com/go/aHR0cHM6Ly9ha2EubXMvd21mNWRvd25sb2Fk)（或更高版本，包括 [PowerShell Core](https://p3terx.com/go/aHR0cHM6Ly9kb2NzLm1pY3Jvc29mdC5jb20vZW4tdXMvcG93ZXJzaGVsbC9zY3JpcHRpbmcvaW5zdGFsbC9pbnN0YWxsaW5nLXBvd2Vyc2hlbGwtY29yZS1vbi13aW5kb3dzP3ZpZXc9cG93ZXJzaGVsbC02)）和 [.NET Framework 4.5](https://p3terx.com/go/aHR0cHM6Ly93d3cubWljcm9zb2Z0LmNvbS9uZXQvZG93bmxvYWQ)（或更高版本）

```powershell
$PSVersionTable.PSVersion.Major   #查看Powershell版本
$PSVersionTable.CLRVersion.Major  #查看.NET Framework版本
```

- Windows 用户名为**英文**（Windows 用户环境变量中路径值不支持中文字符）

- **正常、快速**的访问 GitHub 并下载资源



### 安装Scoop

Scoop 默认使用普通用户权限，本体和安装的软件默认会放在 *%USERPROFILE%\scoop*(即 *C:\Users\用户名\scoop*)，使用管理员权限进行全局安装 (-g) 的软件在 *C:\ProgramData\scoop*。

#### 设置安装路径环境变量

1. 打开 PowerShell
2. 设置用户安装路径
```powershell
$env:SCOOP='D:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
```
3. 设置全局安装路径（需要管理员权限）
```powershell
$env:SCOOP_GLOBAL='D:\Scoop_Global'
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $env:SCOOP_GLOBAL, 'Machine')
```
4. 设置允许 PowerShell 执行本地脚本
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
#### 安装 Scoop

```powershell
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
#或者
iwr -useb get.scoop.sh | iex
```
#### Scoop运行所需基础软件

```powershell
scoop install sudo
sudo scoop install 7zip git  -g 
scoop install innounp dark
#TODO:这几个是否需要全局?
```
#### 代理设置
Scoop 默认使用系统代理

`scoop config proxy localhost:4780` 设置代理（ http 协议）
`scoop config rm proxy` 取消代理，恢复使用系统代理
`scoop config proxy`查看代理

#### 开启多线程下载

##### 安装Aria2

使用 Scoop 安装 Aria2 后，Scoop 会自动调用 Aria2 进行多线程加速下载。
`scoop install aria2`

##### 优化Aria2
使用 `scoop config` 命令可以对 Aria2 进行设置，比如 `scoop config aria2-enabled false` 可以禁止调用 Aria2 下载。
Aria2 有关的设置选项：

```powershell
aria2-enabled: 开启 Aria2 下载，默认true
aria2-retry-wait: 重试等待秒数，默认2
aria2-split: 单任务最大连接数，默认5
aria2-max-connection-per-server: 单服务器最大连接数，默认5 ，最大16
aria2-min-split-size: 最小文件分片大小，默认5M
```
**推荐设置**

```powershell
scoop config aria2-split 32 
scoop config aria2-max-connection-per-server 16
scoop config aria2-min-split-size 1M
scoop config aria2-warning-enabled false
scoop config aria2-enabled true
```
#### scoop-complestion 自动补全
##### 安装scoop-complestion
```powershell
# add auto-update bucket
scoop bucket add scoop-completion https://github.com/Moeologist/scoop-completion
# install
scoop install scoop-completion
```
##### 在当前shell启用补全
`Import-Module "$($(Get-Item $(Get-Command scoop.ps1).Path).Directory.Parent.FullName)\modules\scoop-completion"`

##### 自动加载补全
```powershell
# create profile if not exist
if (!(Test-Path $profile)) { New-Item -Path $profile -ItemType "file" -Force }

# print $profile path
$profile
```
在文本编辑器打开 $profile，然后添加启用代码（Import-Module 行）到该文件。

##### 卸载scoop-complestion
```powershell
#卸载:
scoop uninstall scoop-completion
scoop bucket rm scoop-completion
#然后手动修改 $Profile (移除启用代码)
```

### 基础使用
#### 全命令解释
```powershell
alias          管理别名
bucket         管理软件源
cache          显示或清除下载缓存
checkup        检查潜在问题
cleanup        通过删除旧版本来清理应用程序
config         获取或设置配置值
create         创建一个自定义应用程序清单
depends        列出应用程序的依赖项
export         导出(一个可导入的)已安装的应用程序列表
help           显示命令的帮助
hold           禁用应用程序更新
home           打开应用程序主页
info           显示应用程序的信息
install        安装应用程序
list           已安装的应用程序列表
prefix         返回指定应用程序的路径
reset          切换应用程序版本
search         搜索可用的应用程序
status         显示状态和检查新的应用程序版本
unhold         启用应用程序更新
uninstall      卸载应用程序
update         更新应用程序或Scoop本身
virustotal     在virustotal.com上寻找应用程序的哈希
which          定位shim/可执行文件(类似于 Linux 上的 'which'）
```
#### 常用命令总结
```powershell
# 更新 scoop 及软件包列表
scoop update

#搜索软件
scoop search <app>

## 安装软件 ##
# 非全局安装（并禁止安装包缓存）
scoop install -k <app>
# 全局安装（并禁止安装包缓存）
sudo scoop install -gk <app>
#安装特定版本软件
scoop install <app>@<version>

## 卸载软件 ##
# 卸载非全局软件（并删除配置文件）
scoop uninstall -p <app>
# 卸载全局软件（并删除配置文件）
sudo scoop uninstall -gp <app>

#检查哪些软件有更新
scoop status

## 更新软件 ##
# 更新所有非全局软件（并禁止安装包缓存）
scoop update -k *
# 更新所有软件（并禁止安装包缓存）
sudo scoop update -gk *
#禁止软件更新
scoop hold <app>

#版本切换
scoop reset <app>@<版本>

## 垃圾清理 ##
# 删除所有旧版本非全局软件（并删除软件包缓存）
scoop cleanup -k *
# 删除所有旧版本软件（并删除软件包缓存）
sudo scoop cleanup -gk *
# 清除软件包缓存
scoop cache rm *
```
### Scoop换源
要改善 Scoop 的下载速度，详细可以参照 [Scoop | Gitee 版](https://gitee.com/squallliu/scoop#install-scoop-to-a-custom-directory-by-changing-scoop) 的说明更换下载源。换源之后的Scoop，速度提升不是一星半点儿。
- 更换 Scoop 源
```powershell
scoop config SCOOP_REPO https://gitee.com/squallliu/scoop
scoop update
```
- 更换 bucket 源
```powershell
scoop install git
# 注意：引号里面换成自己的路径，如果是默认路径则为${Env:USERPROFILE}\scoop\buckets\<bucket_name>

git -C "D:\Scoop\buckets\main" remote set-url origin https://hub.fastgit.org/ScoopInstaller/Main.git

git -C "D:\Scoop\buckets\extras" remote set-url origin https://hub.fastgit.org/lukesampson/scoop-extras.git
```

### 进阶使用
#### scoop search 搜索软件包
`scoop search <app>`搜索软件包名字
#### scoop home 打开软件主页
`scoop home <app>`打开软件主页
#### scoop info 查看软件信息
`scoop info <app>` 可以在安装软件前可以对包体有个了解。

#### scoop bucket管理软件源

##### 官方Bucket

###### 查询Bucket
```powershell
$ scoop bucket known
nightlies
nirsoft #是一个 NirSoft 开发的小工具的安装合集。NirSoft 制作了大量的（dozens and dozens）小工具，包括系统工具、网络工具、密码恢复等等
php
java
games #游戏（和与游戏相关的工具）合集。包含了大量免费/开源的小游戏
jetbrains

extras # 诸多有用的软件都在里面
main # 默认的大仓库
nerd-fonts # 编程字体一览无遗
nonportable # 收录神奇的UWP应用
versions # 收录软件包的历史版本
```
###### 添加Bucket
```powershell
scoop bucket add <bucketname>
```
###### 从官方bucket 中安装软件
```powershell
scoop install <app>
```

##### 第三方Bucket
###### 查询Bucket及软件
- [scoopsearch](https://scoopsearch.github.io/#/apps)
- [scoop-directory](https://rasa.github.io/scoop-directory/search)

###### 添加Bucket
```powershell
scoop bucket add <bucketname> https://github.com/xxx/xxx
```
###### 从第三方 bucket 中安装软件
```powershell
scoop install <bucketname>/<app>
scoop install https://gist.github.com/xxxx/xxxx/raw/app.json
```

###### 删除Bucket

`scoop bucket rm <bucket>`

#### scoop cache清理安装包缓存

Scoop 会保留下载的安装包，对于卸载后又想再安装的情况，不需要重复下载。但长期累积会占用大量的磁盘空间，如果用不到就成了垃圾。这时可以使用 `scoop cache` 命令来清理。
```powershell
scoop cache show - 显示安装包缓存
scoop cache rm <app> - 删除指定应用的安装包缓存
scoop cache rm * - 删除所有的安装包缓存
```
#### scoop cleanup删除旧版本软件
当软件被更新后 Scoop 还会保留软件的旧版本，更新软件后可以通过 `scoop cleanup` 命令进行删除。
```powershell
scoop cleanup <app> - 删除指定软件的旧版本
scoop cleanup * - 删除所有软件的旧版本
```

与安装软件一样，删除旧版本软件的同时也可以清理安装包缓存，同样是加上 `-k` 选项。
```powershell
scoop cleanup -k <app> 删除指定软件的旧版本并清除安装包缓存
scoop cleanup -k * 删除所有软件的旧版本并清除安装包缓存
```
#### scoop checkup 自我检查
检查软件潜在问题

####  scoop reset 软件版本切换
这在你同时需要几个版本的软件包时会比较有用，比如 Python2 和 Python3。
```powershell
$ scoop install python27 python
$ python --version        # -> Python 3.6.x

$ scoop reset python27
$ python --version        # -> Python 2.7.x

$ scoop reset python
$ python --version        # -> Python 3.6.x
```
### scoop备份

#### scoop export导出软件列表
`scoop export >> C:\list.txt`
#### scoop backup备份
##### 安装
```powershell
scoop bucket add knox-scoop https://git.irs.sh/KNOXDEV/knox-scoop
scoop install scoop-backup
```
##### 保存安装(.ps1)
```powershell
scoop-backup .\path\to\folder\
#ex.
scoop-backup C:\
```
##### 恢复备份
用powershell执行
`./backup-*.ps1`

##### 另存为压缩的批处理文件(.bat)
```
scoop-backup --compress
# >> "output-folder\backup.bat"
```

##### scoop配置文件位置

*C:\Users\Administrator\.config\scoop*

### Scoop 迁移及重装后恢复使用
根据 GitHub issue 「How to use scoop after reinstalling the system」 及参考该文章（文章作者即为上述 issue 的提出者），步骤如下：

1. 将 Scoop 安装目录完整保存至他处
2. 将上述保存的文件夹放置在目标处
3. 在用户环境变量中，新建一个 SCOOP 变量，值为第二步中 scoop 文件夹地址，如 D:\Scoop
4. 在用户变量 Path 中新增一条，值为第二步中 scoop 文件夹下 shims 文件夹地址，如 D:\Scoop\shims
5. 允许脚本执行：set-executionpolicy remotesigned -s currentuser
6. scoop reset * 等待 scoop 重置完




### Bucket 源

一般来说，可谷歌搜索「软件名+Scoop」就可以找到我们想要安装的软件有没有被某个bucket 软件仓库收录。

```powershell
scoop bucket add extras
scoop bucket add versions
scoop bucket add nirsoft
scoop bucket add nerd-fonts
scoop bucket add nonportable
scoop bucket add games

#合并bucket：
scoop bucket add apps https://github.com/kkzzhizhou/scoop-apps

#nirsoft大神的软件建议一个一个安装，因为有些恢复/查看密码的功能基本会被大多数杀毒软件误杀（其实不完全算误杀，算是危险动作）

#scoop这种管理的软件适合配合防火墙使用，当安装一些功能上不需要联网的软件时我就会通过scoop安装，禁用自动更新功能并用防火墙彻底禁止其联网，所有升级操作都用scoop来完成。
```

### 软件包
```
# dev tools
scoop install postman

# CLI tools
scoop install wget
scoop install curl
scoop install youtube-dl
scoop install vim
scoop install latex
scoop install pandoc

# apps tools
scoop install vlc   # vedio
scoop install copyq # 剪切板工具
scoop install quicklook         #快速预览工具
scoop install mactype-np  #装机必备的字体优化工具
```

### 使用 DISM++ 检查 WIN10 环境
```powershell
# 现在 scoop 安装 Dism
# 扫描全部系统文件并和官方系统文件对比，扫描计算机中的不一致情况
Dism /Online /Cleanup-Image /ScanHealth
# 这条命令必须在前一条命令执行完以后，发现系统文件有损坏时使用
Dism /Online /Cleanup-Image /CheckHealth
# 不同的系统文件还原成官方系统源文件
DISM /Online /Cleanup-image /RestoreHealth
# 完成重启后
sfc /SCANNOW
```
