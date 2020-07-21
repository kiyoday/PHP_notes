## 初始配置

### 查看所有配置

```bash
git config --global --list
```

### 配置用户名和邮箱

```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```



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

```bash
set nu
set fencs=utf-8,gbk,utf-16,utf-32,ucs-bom
```

修改git默认编辑器

```bash
$ git config --global core.editor "'D:\Sublime\sublime_text.exe' -w"
```

