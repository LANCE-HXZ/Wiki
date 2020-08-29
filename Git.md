# Git - 分布式版本控制系统

## 安装
Ubuntu
```bash
sudo apt-get install git
```
[Window下载地址](https://git-scm.com/downloads)

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

### 创建 SSH Key
+ 在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人. 如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
`ssh-keygen -t rsa -C "youremail@example.com"`
+ 登陆GitHub，打开“Account settings”，“SSH Keys”页面, 点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容
+ 免费 GitHub 上的内容将完全公开

### 从零开发
最好的方式是先创建远程仓库后克隆
+ 登陆GitHub，右上角“Create a new repo”创建
+ 复制克隆链接
`git clone git@github.com:michaelliao/gitskills.git`
    - 使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https

### 创建远程仓库与本地关联
+ 登陆GitHub，右上角“Create a new repo”创建
+ 关联本地仓库, 参数分别是本地上为该远程仓库的命名, 默认为origin, 可定义为github等; 链接为对应仓库链接
`git remote add <origin> <git@github.com:michaelliao/learngit.git>`
+ 本地仓库推送到 origin 仓库的 master 分支, 第一次推送 master 分支时，加上了 -u 参数可以关联
`git push -u origin master`
    - 第一次使用 Git 的 clone 或者 push 命令连接 GitHub 时，会得到一个 SSH 警告, 输入 yes 确认后就不再出现


### push 提交变更到远程仓库
未指定参数时, 根据 HEAD 跟踪的分支确定 push 的目的地
```bash
-/ 切到本地仓库中的 <place> 分支, 获取所有的提交, 再到远程仓库 <remote> 中找到 <place> 分支, 
将远程仓库中没有的提交记录都添加上去, 搞定之后告诉我 -/
git push <remote> <place>

// 要同时为源和目的地指定 <place> 的话, "注意如果 <src> 传了个空值过去, 会删除该远程分支"
git push origin <src>:<dst>
```

### fetch 从远程仓库下载本地仓库缺失的提交记录, 并更新远程分支指针
```bash
// 仅下载, 并不改变本地仓库的状态, 不会更新master分支
git fetch
```

### pull 合并远程分支
当你想要 push 时发现远程仓库在未来版本, 需要使自己的工作基于远程的提交后再 push

可以先 fetch 后再合并, 也可以直接 pull
```bash
//  rebase 使你的提交树变得很干净, 所有的提交都在一条线上
git rebase rep_name/master
//  可以保留提交树的历史
git merge rep_name/master

git pull        //  相当于 git fetch + git merge rep_name/master
git pull -r     //  --rebase的缩写
```

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