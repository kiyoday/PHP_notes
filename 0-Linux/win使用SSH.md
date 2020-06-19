## SSH免密码登录

### windows端

```powershell
> new-guid #
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

` Set-Culture zh-CN`设置环境

`notepad $PROFILE`设置powerline



资源管理器中粘贴 并复制自己想要的图片

- %LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\RoamingState

```json
"backgroundImage" : "ms-appdata:///roaming/自己的图片.jpg",
"backgroundImageOpacity" : 0.75,
"backgroundImageStrechMode" : "fill",
```

## 目录结构

```powershell
tree /F
```

