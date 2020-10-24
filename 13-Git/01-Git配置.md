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

## 配置文件

设置别名`lg`配置：

```bash
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

配置文件放哪了？每个仓库的Git配置文件都放在`.git/config`文件中：

```bash
$ cat .git/config 
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com:michaelliao/learngit.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[alias]
    last = log -1
```

别名就在`[alias]`后面，要删除别名，直接把对应的行删掉即可。

而当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

```bash
$ cat .gitconfig
[alias]
    co = checkout
    ci = commit
    br = branch
    st = status
[user]
    name = Your Name
    email = your@email.com
```

配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。

## PowerShell | git log 中文乱码问题解决x

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

