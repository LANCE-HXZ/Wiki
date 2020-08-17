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
git add test.md     //  添加至缓存区，没有消息返回就是操作成功
git add file2.txt file3.txt
git add .           //  添加所有

git commit -m "详细的修改说明"  //  提交到仓库
```

```bash
git status      //  查看仓库状态，可以显示哪些文件被修改但还没提交

git diff test.md    //  查看修改的文件中哪些部分被修改

git log     //  查看历史提交情况
```

## 本地与远程仓库同步

### push 提交变更到远程仓库
未指定参数时，根据 HEAD 跟踪的分支确定 push 的目的地
```bash
*/ 切到本地仓库中的 <place> 分支，获取所有的提交，再到远程仓库 <remote> 中找到 <place> 分支，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我 */
git push <remote> <place>

// 要同时为源和目的地指定 <place> 的话，"注意如果 <src> 传了个空值过去，会删除该远程分支"
git push origin <src>:<dst>
```

### fetch 从远程仓库下载本地仓库缺失的提交记录，并更新远程分支指针
```bash
// 仅下载，并不改变本地仓库的状态，不会更新master分支
git fetch
```

### pull 合并远程分支
当你想要 push 时发现远程仓库在未来版本，需要使自己的工作基于远程的提交后再 push

可以先 fetch 后再合并，也可以直接 pull
```bash
git rebase rep_name/master      //  rebase 使你的提交树变得很干净, 所有的提交都在一条线上
git merge rep_name/master       //  可以保留提交树的历史

git pull        //  相当于 git fetch + git merge rep_name/master
git pull -r     //  --rebase的缩写
```

### 从远程库删除某些文件但保留本地的文件
有时我们会误把一些不必要的文件（如目标文件、log 日志等）提交并推送到了远程库，现在希望从远程库删除这些文件但保留本地的文件
```bash
git rm -r --cached dir1         //  文件夹
git rm --cached dir2/*.pyc      //  文件
git commit -m "remove irrelated files"
git push origin branch1
```