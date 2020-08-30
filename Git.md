# Git - 分布式版本控制系统
本文提炼自[Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600)  

[Git 官网](https://git-scm.com/)  
[Git 命令汇总](https://gitee.com/liaoxuefeng/learn-java/raw/master/teach/git-cheatsheet.pdf)  


## 目录
- [Git - 分布式版本控制系统](#git---分布式版本控制系统)
  - [目录](#目录)
  - [安装](#安装)
  - [本地仓库](#本地仓库)
  - [远程仓库](#远程仓库)
    - [创建SSH_Key](#创建ssh_key)
    - [从零开发](#从零开发)
    - [创建远程仓库与本地关联](#创建远程仓库与本地关联)
    - [push推送分支](#push推送分支)
    - [fetch抓取分支](#fetch抓取分支)
    - [pull合并远程分支](#pull合并远程分支)
    - [从远程库删除某些文件但保留本地的文件](#从远程库删除某些文件但保留本地的文件)
  - [版本回退](#版本回退)
    - [撤销版本库的修改](#撤销版本库的修改)
    - [撤销工作区的修改](#撤销工作区的修改)
    - [撤销暂存区的修改](#撤销暂存区的修改)
  - [分支管理](#分支管理)
    - [解决冲突](#解决冲突)
    - [bug分支](#bug分支)
    - [feature分支](#feature分支)
  - [标签管理](#标签管理)
    - [创建标签](#创建标签)
  - [GitHub](#github)
  - [Git配置](#git配置)
    - [.gitignore](#gitignore)
    - [配置别名/快捷命令](#配置别名快捷命令)
    - [搭建Git服务器](#搭建git服务器)
  - [SourceTree-GUI](#sourcetree-gui)


## 安装
Ubuntu
```bash
sudo apt-get install git
```

[Window下载地址](https://git-scm.com/downloads)  
[目录](#目录)


## 本地仓库
新建一个空仓库
```bash
mkdir test
cd test
git init
```

添加一些文件至/test后需要告知git添加哪些文件至仓库
```bash
git add test.md     //  添加至缓存区, 没有消息返回就是操作成功
git add file2.txt file3.txt
git add .           //  添加所有

git commit -m "详细的修改说明"  //  提交到仓库
```

- 暂存区和工作区 -- 在工作区进行修改后 add 将修改添加到暂存区, commit 时再一次性将此前的修改都提交到分支, 提交后如果没有内容修改, 则工作区是干净的

查看仓库状态
```bash
git status      //  查看仓库状态, 可以显示哪些文件被修改但还没提交
git diff test.md    //  查看修改的文件中哪些部分被修改
```

删除文件
- 从本地版本库删除多余的文件
    ```bash
    git rm test.txt
    git commit -m "remove test.txt"
    ```

    - 先手动删除文件的话需要进行 `git add test.txt`
- 误删了文件后从本地版本库恢复
    ```bash
    git checkout -- test.txt
    ```

    - 其实是用版本库里的版本替换工作区的版本, 无论工作区是修改还是删除, 都可以“一键还原”
    - 注意：从来没有被添加到版本库就被删除的文件, 是无法恢复的！
    - 只能恢复文件到最新版本, 你会丢失最近一次提交后你修改的内容  


## 远程仓库
并不是一定要把本地分支往远程推送  
- 一般需要与远程同步的是 master 和 dev 分支
- bug 和 feature 分支取决于是否完成和是否需要协作


### 创建SSH_Key
+ 在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人  
如果已经有了，可直接跳到下一步。如果没有，打开终端，创建SSH Key：
    ```bash
    ssh-keygen -t rsa -C "youremail@example.com"
    ```

+ 登陆GitHub，打开“Account settings”，“SSH Keys”页面, 点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容
+ 免费 GitHub 上的内容将完全公开

### 从零开发
最好的方式是先创建远程仓库后克隆
+ 登陆GitHub，右上角“Create a new repo”创建
+ 复制克隆链接
`git clone git@github.com:michaelliao/gitskills.git`
    - 使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https
    - 默认情况下, 协作时 clone 下来的只能看到 master 分支, 如果需要在 dev 分支上进行开发, 就必须创建远程origin的dev分支到本地
    ```bash
    git checkout -b dev origin/dev
    ```

### 创建远程仓库与本地关联
- 登陆GitHub，右上角“Create a new repo”创建
- 关联本地仓库, 参数分别是本地上为该远程仓库的命名, 默认为origin, 可定义为github等; 链接为对应仓库链接
    ```bash
    git remote add <origin> <git@github.com:michaelliao/learngit.git>
    ```
    
    - 查看远程仓库状态
    ```bash
    git remote
    git remote -v   //  显示详细信息, 包含地址
    ```

    - 删除本地关联的远程仓库
    ```bash
    git remote rm origin
    ```

+ 本地仓库推送到 origin 仓库的 master 分支, 第一次推送 master 分支时，加上了 -u 参数可以关联
    - 第一次使用 Git 的 clone 或者 push 命令连接 GitHub 时，会得到一个 SSH 警告, 输入 yes 确认后就不再出现

    ```bash
    git push -u origin master
    ```
    

### push推送分支
未指定参数时, 根据 HEAD 跟踪的分支确定 push 的目的地  
推送时，要指定本地分支 place，这样 Git 就会把该分支推送到远程库对应的远程分支上
```bash
git push <remote> <place>
```

要同时为源和目的地指定 <place> 的话, "注意如果 <src> 传了个空值过去, 会删除该远程分支"
```bash
git push origin <src>:<dst>
```

### fetch抓取分支
仅下载并更新远程分支指针, 不改变本地仓库的状态, 不会更新master分支
```bash
git fetch
```

### pull合并远程分支
当你想要 push 时发现远程仓库在未来版本, 需要使自己的工作基于远程的提交后再 push  
可以先 fetch 后再合并, 也可以直接 pull
```bash
//  rebase 使你的提交树变得很干净, 可以把本地未push的分叉提交历史整理成直线
git rebase rep_name/master
//  可以保留提交树的历史
git merge rep_name/master

git pull        //  相当于 git fetch + git merge rep_name/master
git pull -r     //  --rebase的缩写
```

如果你试图推送的提交和别人已经推送的提交有冲突部分(比如两个人都创建了一个相同的文件), 你的小伙伴的最新提交和你试图推送的提交有冲突, 会推送失败, 解决方法:
- 用`git pull`把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送
    - 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建
    ```bash
    git branch --set-upstream-to=origin/dev dev
    ```

    - 合并有冲突，需要手动解决, 再 push

### 从远程库删除某些文件但保留本地的文件
有时我们会误把一些不必要的文件（如目标文件、log 日志等）提交并推送到了远程库, 现在希望从远程库删除这些文件但保留本地的文件
```bash
git rm -r --cached dir1         //  文件夹
git rm --cached dir2/-.pyc      //  文件
git commit -m "remove irrelated files"
git push origin branch1
```


## 版本回退
### 撤销版本库的修改
查看历史版本, 打印出来的 commit id 是版本回退的依据
```bash
git log     //  查看历史提交情况
git log --graph --pretty=oneline --abbrev-commit    //  查看提交树
```

Git 中, 当前版本是HEAD, 上一版本是HEAD^, 上上个版本是HEAD^^, 以此类推, HEAD~100 是往前100个版本
```bash
git reset --hard HEAD^  //  回到上一个版本
```

注意 : 回到旧版本后如果再想回到未来版本, 有两种方法
- 若能找到查找 commit id, 可以直接返回
    ```bash
    git reset --hard 1094a  //  版本号不必写完整
    ```

- 否则, 通过 log 可以查找所有历史命令及其 commit id
    ```bash
    git reflog  //  记录了每一次命令
    ```

- 不同于 revert, 假设是 A->B->C
    - 使用 git reset 回到 B, 变为 A->B, 丢失 C
    - 使用 git revert 增加一个 B, 变为 A->B->C->B, 
    - 具体看需求是否需要保留 C, 若 C 是误提交的不能公开的内容则必须使用reset

### 撤销工作区的修改
```bash
//  让这个文件回到最近一次git commit或git add时的状态, 注意带有"--"的是撤销文件, 不带的是切换分支
git checkout -- readme.md
```

### 撤销暂存区的修改
```bash
//  如果修改已经 add 但还没 commit, 可以把暂存区的修改撤销掉（unstage）, 重新放回工作区
git reset HEAD readme.md
git checkout -- readme.md   //  然后撤销工作区的修改即可
```


## 分支管理
- git 的分支管理通过改变指针来实现, 因此可以非常快地完成分支新增 切换 合并 删除操作  
- 实际开发中, master 是稳定版本, 仅在发布新版本时合并, 开发在 dev 分支上进行, 每个开发者从 dev 上创建新分支, 完成工作后 合并到 dev 分支上
- 解决 bug 时单独开辟一个临时分支, 解决完成后再合并到 dev 分支上  
![](https://www.liaoxuefeng.com/files/attachments/919022325462368/0)  
创建dev分支, 加上 -b 参数表示创建并切换
```bash
git checkout -b dev  或  git switch -c dev
//  相当于
git branch dev  //  创建分支
git checkout dev  或  git switch dev    //  切换分支
```

![](https://www.liaoxuefeng.com/files/attachments/919022363210080/l)  
查看分支状况, 打印出所有分支, * 表示当前分支
```bash
git branch
    * dev
    master
```

在 dev 分支上修改完成后提交  
![](https://www.liaoxuefeng.com/files/attachments/919022387118368/l)  
回到 master 分支, 合并到 dev, 合并指定分支到当前分支
```bash
git merge dev
```

![](https://www.liaoxuefeng.com/files/attachments/919022412005504/0)  
合并完成后可以删除分支
```bash
git branch -d dev
```

![](https://www.liaoxuefeng.com/files/attachments/919022479428512/0)  

### 解决冲突
如果 master 分支和 feature1 分支各自都分别有新的提交  
![](https://www.liaoxuefeng.com/files/attachments/919023000423040/0)  
这种情况下 Git 无法实现快速合并, 只能先尝试将各自的修改合并, 但如果修改中出现了冲突的部分就会合并失败
```bash
git merge feature1
    Auto-merging readme.txt
    CONFLICT (content): Merge conflict in readme.txt
    Automatic merge failed; fix conflicts and then commit the result.
```

Git告诉我们，readme.txt 文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件  
Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容, 修改好冲突的部分才能合并  
![](https://www.liaoxuefeng.com/files/attachments/919023031831104/0)  
查看分支合并图
```bash
git log --graph
```

使用Fast forward模式合并在删除分支后会丢失部分分支信息, 看不出来曾经做过合并  
如果不使用Fast forward模式合并, Git就会在merge时生成一个新的commit, 能在历史信息看到合并记录
```bash
git merge --no-ff -m "merge with no-ff" dev
```  

![](https://www.liaoxuefeng.com/files/attachments/919023225142304/0)

### bug分支
如果临时出现一个紧急处理的 bug, 需要创建新的临时分支处理, 但是当前处于 dev 且有未完成的工作, 可以先保存现场, 处理完 bug 后再恢复现场
```bash
git stash
```

假定需要在master分支上修复，就从master创建临时分支`git checkout master`, `git checkout -b issue-101`, 修复完成后, `git switch master`, `git merge --no-ff -m "merged bug fix 101" issue-101`, 切换回 dev 继续干活 `git switch dev`
```bash
git status
    On branch dev
    nothing to commit, working tree clean
```

修复前的工作进度不在, 需要恢复现场, 查看暂存的现场
```bash
git stash list
    stash@{0}: WIP on dev: f52c633 add merge
```

恢复现场有两种方式
- 用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除
- 用`git stash pop`，恢复的同时把stash内容也删除
```bash
git stash pop
```

由于 dev 分支由 master 而来, master 存在的 bug, 在 dev 上也存在, 所以需要将刚刚对 master 的 debug 修改过程复制到 dev 上, 减少重复工作
- 注意：我们只想复制`4c805e2 fix bug 101`这个提交所做的修改，并不是把整个master分支merge过来, 两个commit只是改动相同，但确实是两个不同的commit, 因此 commit id 不同
在 dev 分支上
```bash
git cherry-pick 4c805e2
```

### feature分支
开发一个新feature，最好新建一个分支  
如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除


## 标签管理
使用一个 tag 与某个 commit 绑定, tag 使用便于使用或理解的名称, 用于快速定位到所需的版本, 而不需要查找 commit id, tag 与 分支类似, 但 tag 无法移动

### 创建标签
切换到需要打标签的分支上`Switched to branch 'master'`  
还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字
```bash
git tag v1.0
git tag -a v0.1 -m "version 0.1 released" 1094adb
```

默认标签是打在最新提交的commit上的, 如果需要补打标签, 方法是找到历史提交的commit id
```bash
git tag v0.9 f52c633
```

查看所有标签
```bash
git tag
    v0.9
    v1.0
git show v0.9   //  查看标签详细信息
```

标签推送到远程仓库
```bash
git push origin v1.0
git push origin --tags  //  一次性推送全部尚未推送到远程的本地标签
```

删除标签  
- 如果标签仅在本地
```bash
git tag -d v0.1
```
- 如果标签已经推送至远程
```bash
git tag -d v0.9
git push origin :refs/tags/v0.9
```

## GitHub
GitHub 使所有人共同参与开发成为可能, 在开源库上通过 Fork 可以将库克隆到自己的账户, 然后通过 `git clone`克隆到本地修改, 修改完后可以往自己的账户上 push, 也可以在 GitHub 上发起一个 pull request, 但是对方是否接受就不一定了


## Git配置

### .gitignore
推送时, 忽略一些机密文件, 忽略一些编译自动生成的文件, 忽略缩略图文件等垃圾文件, 以及忽略自定义的某个文件或文件类型
- 官方提供的各种类型工程的[基础 .gitignore 文件](https://github.com/github/gitignore)
- 如果在 add 某个文件时发现无法添加, 有可能是 .gitignore 规则有问题
    - 可以查看是哪个规则限制了该文件
    ```bash
    git check-ignore -v App.class
    ```

    - 可以强制添加该文件
    ```bash
    git add -f App.class
    ```

### 配置别名/快捷命令
告诉 Git 以后 st 表示 status, --global参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.cm 'commit -m'
git config --global alias.br branch
git config --global alias.unstage 'reset HEAD'
    //  git unstage test.py     //  即 git reset HEAD test.py   撤销暂存区的修改
git config --global alias.last 'log -1'
    //  git last    //  显示最近一次的提交
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
    //  git lg      //  简洁高亮打印 log 提交树记录
git config --global alias.df "diff --compact-summary"
    //  git df      //  通过diff来显示更改的文件，只输出文件名和修改整体信息
```

如需删除配置好的别名, 修改对应的配置文件即可
- local 的每个仓库的Git配置文件都放在.git/config文件中
- global 的当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中

### 搭建Git服务器
搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的apt命令就可以完成安装
1. 安装`sudo apt-get install git`
2. 创建一个git用户，用来运行git服务
    ```bash
    sudo adduser git
    ```

3. 创建证书登录, 收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个
4. 初始化Git仓库, 先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
    ```bash
    sudo git init --bare sample.git
    ```

    Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：
    ```bash
    sudo chown -R git:git sample.git
    ```

5. 禁用shell登录, 出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行
    ```bash
    //  git:x:1001:1001:,,,:/home/git:/bin/bash 改为:
    git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
    ```

    这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出
6. 克隆远程仓库`git clone`
- 如果团队很大, 难以收集所有人公钥, 可以用Gitosis来管理公钥
- 如为保密要像SVN那样变态地控制权限，用Gitolite


## SourceTree-GUI
Git图形界面工具  
- [下载](https://www.sourcetreeapp.com/)
- 添加仓库
    - 直接从资源管理器把文件夹拖拽到SourceTree上
    - 或选择“New”-“Clone from URL”直接从远程克隆到本地
- 双击仓库展示这个Git库的当前所有分支以及文件状态
    - 选择左侧面板的“WORKSPACE”-“File status”，右侧会列出当前已修改的文件（Unstaged files）
    - 选中某个文件，该文件就自动添加到“Staged files”，=`git add`
    - 输入Commit描述，点击“Commit”，就完成了一个本地提交, =`git commit -m "update README.md"`
    - 分支上点击右键，在弹出菜单中选择“Checkout master”, = `git checkout master`
    - 当前是master时, 分支上点击右键，在弹出菜单中选择“Merge dev into master”, = `git merge dev`
    - 有Pull和Push按钮，分别对应命令`git pull`和`git push`