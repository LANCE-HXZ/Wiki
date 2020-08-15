# Git - 分布式版本控制系统
## 安装
Ubuntu
```bash
sudo apt-get install git
```
Window
* https://git-scm.com/downloads

## 创建本地仓库
新建一个空仓库
```bash
mkdir test
cd test
git init
```
添加一些文件至/test后需要告知git添加哪些文件至仓库
```bash
git add test.md     // 添加至缓存区，没有消息返回就是操作成功
git add file2.txt file3.txt
git commit -m "详细的修改说明"  //  提交到仓库
```
查看仓库状态，可以显示哪些文件被修改但还没提交，如果没有内容需要提交会提示仓库是干净的
```bash
git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")
```
查看修改的文件中哪些部分被修改
```bash
git diff test.md
```
查看历史提交情况
```bash
git log
```