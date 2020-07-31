## SSH免密码登录

### windows端

```powershell
> new-guid # 生成uuid
> ssh-keygen -t rsa #生成公钥私钥
```

 

### linux端

复制到~./ssh下目录中某文件即可

## 自动断开修改

```shell
$ vi /etc/ssh/sshd_config
#加入
ClientAliveCountMax 86400
ServerAliveInterval 60
ClientAliveInterval 30

$ service sshd reload 
```



## 美化terminal





资源管理器中粘贴 并复制自己想要的图片

- %LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\RoamingState

```json
"backgroundImage" : "ms-appdata:///roaming/自己的图片.jpg",
"backgroundImageOpacity" : 0.75,
"backgroundImageStrechMode" : "fill",
```

### 安装powerline

```shell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

` Set-Culture zh-CN`设置环境

`notepad $PROFILE`设置powerline

```
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Robbyrussell
```



## 目录结构

```powershell
tree /F
```

## 添加WebStorm右键打开文件夹

#### 1:手动进入注册表



```dart
win+r快捷键  =>  输入regedit => 确定
```

------

运行>regedit>计算机\HKEY_CLASSES_ROOT\Directory\shell

#### 2:添加WebStorm右键打开`文件`

找到`HKEY_CLASSES_ROOT` \ `*` \ `shell`
 在shell内新建**项**,命名为`WebStorm`
 在WebStorm项上右键新建字符串值 `icon`
 WebStorm项内右侧双击默认数值数据内填写你右键菜单的名称`WebStorm打开此文件`
 双击icon数值数据内填写`C:\Users\Sercl\AppData\Local\JetBrains\Toolbox\apps\WebStorm\ch-0\183.4886.41\bin\webstorm64.exe`的路径
 在WebStorm项下新建`command`项
 command项内默认填写`C:\Users\Sercl\AppData\Local\JetBrains\Toolbox\apps\WebStorm\ch-0\183.4886.41\bin\webstorm64.exe %1`记得路径末尾添加`%1`(%前有一个英文空格)
 此时右键文件时菜单项会有`WebStorm打开此文件`

------

#### 3:添加WebStorm右键打开`文件夹`

找到`HKEY_CLASSES_ROOT` \ `Directory` \ `shell`
 在shell内新建**项**,命名为`WebStorm`
 在WebStorm项上右键新建字符串值 `icon`
 WebStorm项内右侧双击默认数值数据内填写你右键菜单的名称`WebStorm打开此文件夹`
 双击icon数值数据内填写`C:\Users\Sercl\AppData\Local\JetBrains\Toolbox\apps\WebStorm\ch-0\183.4886.41\bin\webstorm64.exe`的路径
 在WebStorm项下新建`command`项
 command项内默认填写`C:\Users\Sercl\AppData\Local\JetBrains\Toolbox\apps\WebStorm\ch-0\183.4886.41\bin\webstorm64.exe %1`记得路径末尾添加`%1`(%前有一个英文空格)
 此时右键文件夹时菜单项会有`WebStorm打开此文件夹`



## PowerShell | git log 中文乱码问题解决

```bash
git config --global --list
```

1、在PowerShell中输入以下命令

```shell
git config --global core.quotepath false
git config --global gui.encoding utf-8
git config --global i18n.commit.encoding utf-8
git config --global i18n.logoutputencoding utf-8
$env:LESSCHARSET='utf-8'
```

2、在系统环境变量中添加变量LESSCHARSET为utf-8.


直接新建一个变量添加即可

## Windows下Git Bash中VIM打开文件中文乱码

```shell
$ cd /etc

$ vi vimrc
```

在打开的`vimrc`文件开头添加以下代码：

```
set nu
set fencs=utf-8,gbk,utf-16,utf-32,ucs-bom
```

```
git config --global core.editor "'D:\Sublime\sublime_text.exe' -w"
```

