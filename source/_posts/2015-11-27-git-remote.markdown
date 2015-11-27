---
layout: post
title: "git-远程协作"
date: 2015-11-27 14:16:13 +0800
comments: true
categories: [git]
tags: [git]
---
##远程协作的主要命令

>git ssh-keygen -t rsa -C [github里注册的邮箱] 创建本地ssh

>git clone [地址]  拷贝代码

>git fetch 把远程仓库更新的最新代码下载到本地里

>git merget origin/master 合并到当前分支

>git pull 这个命令即是3、4的合并

>git push 把本地代码推送到远程仓库里(假如当前分支为v0，可以后边跟远程分支，既可以对应更新，如git push origin master 即把本地分支master更新到远程分支master）

>git tag -a v0 -m"[信息]" 创建一个带信息的标签v0（系统默认不能把标签推送到远程仓库里，要推送用命令：git push --tags）

>git branch -d [分支名] 删掉本地分支；git push --delete  origin [分支名] 删除远程分支 或可以这样删除远程分支 git push origin :[分支名]  冒号前边要有个空格，即用一个空的分支替换掉远程分支，即可以达到删除效果。

## 跟踪分支与远程跟踪分支

- 远程跟踪分支：用户只读
- 跟踪分支：用户可写

##远程操作中的remote配置
假如有两个工作者a,b，b工作者创建了一个test.txt文件，然后推送到远程仓库里，这个时候，a工作者fetch了远程仓库，还没merge；这个时候，b工作者发现不想要刚才的提交了，然后在本地先回退到上一个历史记录（git reset --hard HEAD~）然后他又创建了一个文件test1.txt然后强制推送（git push origin +master）到远程创库已达到覆盖刚才的历史提交记录；这个时候，a工作者又fetch远程仓库（git fetch origin master:remotes/orgin/master）会出现无法更新本地的远程仓库跟踪目录（称为：non-fast-forward）。只能强制更新（git fetch origin  +master:remotes/origin/master）;

>所以push操作的时候别用+强制更新试图抹杀掉之前的历史记录，这样会出现意想不到的后果。

git fetch origin master:remotes/origin/master 把远程仓库拉去到远程跟踪分支上

**创建命名空间的分支用于不同团队的开发**

`.git/config` 配置文件里可以配置

>push = refs/heads/*:refs/heads/qa/* 意思是把本地的heads下的分支推送到远程创库的qa命名空间下。

>fetch = +refs/heads/qa/*:refs/remotes/origin/qa/* 意思是把远程仓库里的命名空间qa下的分支，fetch到本地的远程仓库origin（这个可以是自己取的名）跟踪目录下。即用origin master来跟踪远程仓库下的qa命名空间下的master分支。

>git remote set-branches --add origin qa/* 创建qa命名空间 这样既在.git/config 里增加了fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*  这样一条记录。

>git push origin master:qa/master 用本地的master分支推送到远程仓库里qa命名下的master分支，假如远程仓库没有，就会创建该分支即qa.master  (可以在配置文件.git/config 配置 push=refs/heads/*:refs/heads/qa/* 即可以每次推送直接输入git push origin [要推送master分支 默认可以不写] 即可达到相同效果)

**给远程仓库命名**

>git remote add fork [url] 给该地址仓库用fork来引用，即给他命名。

>git remote rename fork forked 重命名 fork为forked

##远程操作中的branch配置

**[branch] 配置的作用**

>git brance --set-upstream-to=origin/v0 v0 配置本地的vo跟踪远程仓库中的vo（.git/config文件里会多出配置信息）然后直接在v0分支下就可以用git pull 来更新该分支了