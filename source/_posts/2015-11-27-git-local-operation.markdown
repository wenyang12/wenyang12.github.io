---
layout: post
title: "git-本地操作"
date: 2015-11-27 14:02:28 +0800
comments: true
categories: [git]
tags: [git]
---
## git 基本配置
**git 配置的增、删、改、查**

>增  git config --global user.name wenyang

>删 git config --global unset user.name

>改 git config --global user.name yangwen

>查 git config --list --global  或  git config --get user.name

**给命令取别名**

>git config --global  alias.co checkout  给checkout命令取了co别名

>git config --global  alias.br branch  给branch命令取了br别名

>git config --global  alias.st status 给查看当前状态status取别名st

>git config --global  alias.ci commit  给提交commit取别名ci

>git config --global alias.lol "log --oneline"  给输出一行日志取了别名lol

## git 基本的工作流程

>建立仓库 git init wen  建立了一个wen文件夹，在wen目录下有一个.git目录（里边即为git的工作区间）

>建立一个裸仓库 git init --bare  即创建出来不带工作区.git工作的目录

>在一个目录下输入 git init 即会在该目录下穿件一个工作区.git

>克隆仓库 git clone wen/ wen1  把wen里边的文件 克隆到 wen1目录下

>git touch a  创建文件a

>git add a 把工作区的a文件添加到暂存区里

>git commit -m "initial commit" 把暂存区里边的文件添加到历史记录里 注释的文本为 "initial commit"

>git rm a 删除工作区跟暂存区里的a

>git reset HEAD a  从历史记录恢复a到暂存区里

>git checkout a 从暂存区里捡出a到工作区

>git rm --cached a 从暂存区里删除a

>git mv a c 将a重命名为c  或mv a c即将a删除添加c

>创建.gitignore  文件用于忽略不提交的文件

- *[oa] 匹配以o和a为后缀的所有文件
- *~匹配以~结尾的所有文件
- *.html 匹配所有以html后缀结尾的文件
- !test.html 即忽略这个文件
- **/res 匹配res文件夹
- build/ 匹配build目录下的所有文件

## 本地分支和合并

>git branch test  创建一个分支test

>git tag "v0" 1813cee(历史提交里的7位hash数 如1813cee)  给1813cee对应的历史提交添加一个标签“v0” 方便维护时，可以直接切换到这次历史提交进行修改。

>git tag 查看自己创建了多少个tag

>git log --oneline --decorate --graph --all 查看所有提交单行的历史示意图

>git show v0  查看v0标签对应的历史提交信息

>git checkout vo 切换到当前标签所对应的历史记录里，但是要是修改了，在切换到别的分支上，这个历史记录会被丢弃掉，所有 要修改可以在这个历史记录上建立一个分支，然后切换到建立的这个分支里修改

>git checkout -b "fix_v0" 在当前标签的历史记录里穿件一个分支“fix_v0”并切换到该分支下

>git stash save -a "stash1" 把工作区跟暂存区保存起来（当在fix_v0这个分支里修改文件并添加到暂存区了，但未提交，那么要想切换到别的分支时，先 stash保存起来，不然就会报错）

>git stash list  查看stash里的信息

>git stash pop --index stash@{0} 要是切换到别的分支工作时，然后在切换回来fix_v0分支，通过这个命令可以还原回第8条保存起来的，命令中的pop即也吧这个stash的历史记录删掉了，要是换成 apply 就不会删除掉。

>git stash drop stash@{0} 把这个stash历史记录清楚掉。

>git stash clear  可以全部清理掉stash历史记录

>git merge test_merge 把分支test_merge合并到当前分支

>git merge --abort放弃这次合并（假如合并冲突的时候）

## 查看以对比历史记录

> git show master显示master指向的历史提交

> git show master^ 或 git show master~ 显示master指向的第一副提交（即第一次合并到master分支的历史提交信息）；git show master^2  指向的是第二副提交;只显示一行 git show --oneline master^2

> git show --stat master^2  输出提交所做的改变统计信息；git show --format=%T master^2 格式化显示 第一行显示tree对象的hash；

> git log 输出日志完整信息，太多我们可以输入"空格或f"向下翻页，“b”向上翻页，按q退出。

> git log  -p 输出每一个commit之间的差异信息

> git log --oneline --decorate --graph --all  其中“--decorate”是输出commit引用信息，“--graph”输出图像化信息 “--all” 输出所有分支的一个信息。

> git diff 输出工作区与暂存区之间的差异；git dff --cached 查看暂存区与历史提交的差异；git diff HEAD~2 -- master.txt 查看指定的最近两次的历史提交与工作区差异 这里指定了master.txt的差异；需要比较暂存区与其他历史提交的差异：git diff --cached HEAD~2; 比较两个commit之间的差异：git diff HEAD HEAD~2 比较当前的历史提交与之前的两个历史提交的差异； 用颜色输出差异：git diff --color-words; 查看增加了什么信息：git diff --word-diff

## 撤销修改
>git checkout -- master.txt 撤销"master.txt"工作区添加的内容，或git checkout -- .恢复所有工作区添加的内容

>git reset HEAD master.txt 撤销"master.txt"缓存区添加的内容,或后边不接文件名，即git reset HEAD 恢复所有缓存区

>git checkout [分支名或标签名] -- [文件名]  指定某个分支或标签的某个文件来覆盖当前的暂存区 和工作区；

>git checkout HEAD -- .把当前的历史记录恢复到暂存区还有工作区。

>git reset [分支名或标签名] -- [文件名] 指定某个分支或标签的某个文件来覆盖当前的暂存区。

>git clean -n 列出将要删除的文件；git clean -f  删除 -列出的文件；git clean -n -X 列出在.gitignore里匹配的文件；git clean -X -f 删除.gitignore里匹配的文件(其中要是-X为小写的话，将删除 -n和-n -X 列出的文件)。

>git revert HEAD -m "[信息]" 有某些提交的内容不需要，产生新的提交覆盖当前这个历史提交，即取反提交，刚提交的添加文件，即删除该文件等。

## 重写历史记录

>git commit --amend -m "[提交信息]"  用于修改当前的历史记录提交，即替换掉当前历史提交。

>git rebase master  合并master分支并产生线性提交。修改合并后，继续：git rebase --continue 然后添加：git add . 然后继续git rabase --continue；

>git reflog 显示所有HEAD引用的历史记录（通常reflog 要配合reset来使用）

>git  reset --hard HEAD@{3}  回退到历史HEAD@{3}指向的引用（实际上是把当前分支名和HEAD引用指定到某个历史记录里） --hard参数是让引用的这个历史的提交还原当前工作区和暂存区（要是换成--mixed参数，它只还原暂存区，未还原工作区）（换成--soft 那么都不还原当前工作区与暂存区）；