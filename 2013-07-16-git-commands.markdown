---
layout: post
title: "git常用命令"
date: 2013-07-16 23:17
comments: true
categories: git
---
### 使用git和github有一定的时间了。也使用的不少一些git的命令，这篇日志就当一个备忘本吧。

*   `git status -s` 用于显示当先git信息
*   `git remote add [github] [http://github.com/XX/XX.git]` 加入远程版本库
*   `git pull [github] [branch]` 将**远程库的某一个分支**同步到本地
*   `git push [github] [branch]` 将**本地分支**提交更新到**远程库的某一个分支**
*   `git branch` 用来显示当前分支
*   `git branch [branch name]` 新建一个名为[branch name]的分支
*   `git checkout [branch]` 切换到某一个分支
*   `git branch -d [branch]` 删除某一个分支
*   `git stash` 保存当前工作区的更改(一般在切换分支的时候使用)
*   `git stash pop` 恢复工作区的更改
*   `git checkout -- [file]` 使用暂存区快照恢复工作目录文件，工作目录的文件修改被抛弃。 
*   `git checkout HEAD^ [file]` 直接 "签出" 代码仓库中的某个文件版本到工作目录，该操作同时会取消暂存区快照。