# Git

## 版本控制种类

### 分布式

可以离线

- Git

  - 版本控制与托管不同

### 集中式

有一台主服务器保管

## 相关术语

### 版本控制系统 / 源代码管理器

- 版本控制系统（简称 VCS）是一个管理源代码不同版本的工具。
- 源代码管理器（简称 SCM）是版本控制系统的另一个名称

### 提交（Commit）

- Git 将数据看做微型文件系统的一组快照。每次 commit（在 Git 中保持项目状态），它都对文件当时的状况拍照，并存储对该快照的引用。你可以将其看做游戏中的保存点，它会保存项目的文件和关于文件的所有信息。
- 你在 Git 中的所有操作都是帮助你进行 commit，因此 commit 是 Git 中的基本单位。

### 仓库（Repository / repo）

- 仓库是一个包含项目内容以及几个文件（在 Mac OS X 上默认地处于隐藏状态）的目录，用来与 Git 进行通信。
- 仓库可以存储在本地，或作为远程副本存储在其他计算机上。
- 仓库是由 commit 构成的。

### 工作目录 / 工作区

（Working Directory）

- 工作目录是你在计算机的文件系统中看到的文件。当你在代码编辑器中打开项目文件时，你是在工作目录中处理文件。
- 与这些文件形成对比的是保持在仓库中（在 commit 中！）的文件。
- 在使用 Git 时，工作目录与命令行工具的 current working directory （当前工作目录）不一样，后者是 shell 当前正在查看的目录。

### 检出（Checkout）

- 检出是指将仓库中的内容复制到工作目录下

### 暂存区 / 暂存索引 / 索引

（Staging Area / Staging Index / Index）

- Git 目录下的一个文件，存储的是即将进入下个 commit 内容的信息。
- 可以将暂存区看做准备工作台，Git 将在此区域获取下个 commit。暂存索引中的文件是准备添加到仓库中的文件

### SHA

- 全称是"Secure Hash Algorithm"（安全哈希算法）
- SHA 是每个 commit 的 ID 编号
- 它是一个长 40 个字符的字符串（由 0–9 和 a–f 组成），并根据 Git 中的文件或目录结构的内容计算得出

### 分支（Branch）

- 分支是从主开发流程中分支出来的新的开发流程。这种分支开发流程可以在不更改主流程的情况下继续延伸下去

## Windows 设置步骤

### 1、安装 Git

- 转到 https://git-scm.com/downloads

- 下载 Windows 版软件
- 安装 Git 并选择所有默认选项

### 2、在 Windows 上配置命令提示符

- MP4

### 3、初次配置 Git

#### 设置你的 Git 用户名

git config --global user.name "<Your-Full-Name>"

#### 设置你的 Git 邮箱

git config --global user.email "<your-email-address>"

#### 确保 Git 输出内容带有颜色标记

git config --global color.ui auto

#### 对比显示原始状态

git config --global merge.conflictstyle diff3

git config --list

## 创建仓库

### 创建仓库git init

- 运行此命令可以创建隐藏 .git 目录
- 此 .git 目录是仓库的核心/存储中心。它存储了所有的配置文件和目录，以及所有的 commit

### 克隆仓库git clone

- 会获取现有仓库的路径
- 默认地将创建一个与被克隆的仓库名称相同的目录
- 可以提供第二个参数，作为该目录的名称
- 将在现有工作目录下创建一个新的仓库

### 判断仓库的状态git status

- 命令将显示仓库的当前状态
- 告诉我们已在工作目录中被创建但 Git 尚未开始跟踪的新文件
- Git 正在跟踪的已修改文件

## 仓库历史

### 显示commit:git log

- 基本操作
- git log --oneline 简略显示
- git log --stat

  - 可以用来显示 commit 中更改的文件以及添加或删除的行数

- git log -p（和 --patch 选项一样）

  - 此命令会向默认输出中添加以下信息：
    显示被修改的文件
    显示添加/删除的行所在的位置
    显示做出的实际更改
  - 查看特定SHA

    - git log -p fdf5493
    - git show

## 仓库添加commit

### 暂存git add

- git add <name>
- 添加路径下所有git add .

### 保存到仓库git commit

- 会打开编辑器
- 在第一行输入本次提交的message
- 绕过编辑器

  - git commit -m "Initial commit"

### 查看未c更改git diff

- 已经修改的文件
- 添加/删除的行所在的位置
- 执行的实际更改

### 忽略.gitignore

- 
- 通配符

## 分支管理

### 标签git tag

-  git log --decorate

   - 可以看见标签信息，log新版默认有标签

-  添加标签git tag -a v1.0

   - 向最近的 commit 添加标签
   - 如果提供了 SHA，则向具体的 commit 添加标签

-  删除标签git tag -d v1.0

### 分支git branch

- git branch

  - 列出仓库中的所有分支名称
  - 创建新的分支

    - git branch <name>

  - 删除分支

    - git branch -d <name>

      - 需要切到其他分支上
      - 有c内容要-D大写

- git branch alt-sidebar-loc 42a69f

  - 创建分支并指向指定commit

- git checkout <name>

  - 切换到某一分支上

- 活跃分支git branch
- 日志中显示分支
  git log --oneline --decorate

### git checkout

- 切换并创建分支

  - git checkout -b <name>

- 从指定起点创建分支

  - git checkout -b <name><SHA>

    - git checkout -b footer master

- 同时查看所有分支

  - git log --oneline --decorate --graph --all

    - --graph 选项将条目和行添加到输出的最左侧。显示了实际的分支。
    - --all 选项会显示仓库中的所有分支

### git merge

- 合并git merge <name>

  - 查看将合并的分支
  - 查看分支的历史记录并寻找两个分支的 commit 历史记录中都有的单个 commit
  - 将单个分支上更改的代码行合并到一起
  - 提交一个 commit 来记录合并操作

- 快进合并分支在master前

  - git merge footer

- 普通合并：在m后但是没有冲突

  - git merge <name>

### 冲突解决

- 1、git status 查看冲突

  - 打开冲突文件

- 2、

  - 修改结束后commit
  - 或者丢弃git merge --abort

- 3、commit不会检查冲突符，需要diff检查

## 撤销更改

### 更改最后一个commit

- git commit --amend
- 更新最近的 commit，而不是创建新的 commit

### 还原

- git revert <SHA-of-commit-to-revert>
- 用于还原之前创建的 commit

### 重置

- 引用

  - 子主题 1

- git reset <reference-to-commit>
- 备份分支

  - git branch backup

- 三个选项

  - --mixed移至工作目录

    -  git reset --mixed HEAD^

  - --soft移至暂存区
  - --hard 直接清除

## 连接github

### 连接验证

- ssh-keygen -t rsa -C "youremail@qq.com"

  - 后面的 your_email@youremail.com 改为你在 Github 上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在 ~/ 下生成 .ssh 文件夹，进去，打开 id_rsa.pub，复制里面的 key。

回到 github 上，进入 Account => Settings（账户配置）。

- ssh -T git@github.com

### 提交到 Github

- git remote add origin git@github.com:tianqixin/runoob-git-test.git
- git push -u origin master

### 同步更新

- git fetch origin
- git merge origin/master

