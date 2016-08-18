title: Git Notes
date: 2016-08-06 15:18:55
tags: [Git]
categories: Coding
---

本文源于一年前（2015年7月）的Git学习笔记，主要是常用命令的总结，参考自[廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。

### 最常用命令: clone, add, commit, push  

``` bash  
git clone https://github.com/buguake/cms.git  

git add .  

git commit -m "<your comment of this commit>"  
#上面两条等价于下面这条命令  
git commit -a -m "blabla..."  

git push origin master  
```

### 非常有用的命令
``` bash
# 将本地分支与远程分支映射
git branch --set-upstream-to branch-name origin/branch-name  
# 清除缓存
git rm -r --cache .
#把一个已有的本地仓库与远程仓库关联  
git remote add origin https://github.com/xjiajiahao/learngit.git  
# shallow clone
git clone --depth=1 https://github.com/xjiajiahao/BPlusTree ~/BPlusTree
```
<!--more-->
### 基本命令  
``` bash  
#设置名字和邮箱:  
git config --global user.name "Your Name"  
git config --global user.email "email@example.com"  

#创建repository:  
mkdir learngit  
cd learngit  

#显示当前目录  
pwd    

#把当前目录建成仓库    
git init  

#添加文件到暂存区    
git add readme.txt  

#把文件提交到仓库    
git commit -m "wrote a readme file"  

#查看仓库当前的状态    
git status  

#查看difference */  
git diff readme.txt  

#查看提交日志  
git log  
git log --pretty=oneline  

#查看历史commit id  
git log --pretty=oneline --abbrev-commit  

#版本回退  
#上一个版本就是HEAD^，上上一个版本就是HEAD^^，上100个版本是HEAD~100    
git reset --hard HEAD^  
git reset --hard 3628164  #指定回到某个版本  
gitreflog    #查看每一次命令  

#cat命令  
cat readme.txt                        #一次查看整个文件  
cat > readme.txt                    #创建一个文件  
cat file1 file2 > file              #将几个文件合并为一个文件  

#查看工作区和版本库里面最新版本的区别  
git diff HEAD -- readme.txt  

#丢弃修改  
git checkout -- readme.txt       #丢弃工作区的修改  
git reset HEAD readme.txt        #把暂存区的修改撤销掉（unstage），重新放回工作区  

#删除repository中的文件  
git rm test.txt  
git commit -m "remove test.txt"  

#误删后从版本库中复原，把repository的文件覆盖到工作区，只能恢复到最新版本  
git checkout -- test.txt  
```

### 远程仓库
``` bash
#创建SSH Key  
ssh-keygen -t rsa -C"youremail@example.com"  

#推送到GitHub  
git remote add origin https://github.com/xjiajiahao/learngit.git  #把一个已有的本地仓库与远程仓库关联  
git push -u origin master  #把本地库的所有内容推送到远程库上  
git push origin master    #把本地master分支的最新修改推送至GitHub  

#克隆到本地库  
git clone git@github.com:xjiajiahao/gitskills.git  
git checkout -b dev origin/dev  
```

### 分支（Branch）
``` bash
#创建并切换分支  
git checkout -b dev  

#相当于以下两条命令  
git branch dev  
git checkout dev  

#查看当前分支  
git branch  

#合并分支到master  
git merge dev  

#删除分支  
git branch -d dev  

#因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，
#这和直接在master分支上工作效果是一样的，但过程更安全。  

#查看分支历史  
git log --graph --pretty=oneline --abbrev-commit  

#合并dev分支（禁用fast forward）  
git merge --no-ff -m "merge with no-ff" dev  

#把工作现场“储藏”起来，等以后再恢复  
git stash  

#查看stash  
git stash list  

#恢复工作区  
git stash pop  #恢复的同时把stash内容删除  
git stash apply #只恢复不删除stash  
git stash apply stash@{0}  #恢复指定stash  
git stash drop #删除stash内容  

#强行删除没有合并过的分支  
git branch -D feature-vulcan  
```

### 多人协作  
``` bash
#查看远程库的信息  
git remote  
git remote -v  #详细信息  

#把指定分支上的所有本地提交推送到远程库  
git push origin master  

#创建远程origin的dev分支到本地  
git checkout -b dev origin/dev  

#若自己的提交与他人他=的提交有冲突,应把最新的提交抓下来，解决冲突，再推送  
git pull  

#指定本地dev分支与远程origin/dev分支的映射  
git branch --set-upstream branch-name origin/branch-name  
```

### 其他
``` bash
#对所在分支打标签  
git tag v1.0  

#对指定的commit打标签，最后的参数为commit id  
git tag v0.96224937  

#查看标签     
git tag  

#查看标签信息  
git show <tagmame>  

#创建有说明的标签  
git tag -a v0.1 -m "version 0.1 released"3628164  

#用私钥 -s 给标签签名  
git tag -s v0.2 -m "signed version 0.2 released" fec145a  

#删除标签  
git tag -d v0.1  

#推送标签到远程  
git push origin v1.0  

#一次性推送全部标签  
git push origin --tags  

#删除远程标签（要先删除本地标签）  
git push origin :refs/tags/v0.9  

#配置别名  
git config --global alias.st status  #以st代表status  
git config --global alias.co checkout  
git config --global alias.ci commit  
git config --global alias.br branch  
git config --global alias.unstage 'reset HEAD'  # 把暂存区的修改撤销掉（unstage）  
git config --global alias.last 'log -1'    # 显示最后一次提交信息  
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"      # make log looks better
```
