---
title: Git 的Push和Pull
date: 2017-01-14 11:12:39
categories: others
tags:
  - git
---

#### Git pull     

git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。      
```
$ git pull origin master:master 
```
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。    
```
$ git pull origin master ##取回远程仓库的master分支并和当前分支合并  
```
<!--more-->

上句命令等价于:  

```bash
$ git fetch origin  ##取回远程仓库的所有分支的更新(后面可指定分支)
$ git merge origin/master ##将当前的分支和远程仓库的master分支合并
```
<!--more-->

当本地对本地分支进行更新并提交后，如果本地仓库比远程仓库的版本更高时，会产生以下错误  
```bash
 fatal: refusing to merge unrelated histories 
```

这时有两种方案：  
1.强行合并远程和本地仓库:  
```bash
$ git pull origin master --allow-unrelated-histories 
```
2.强行用远程仓库覆盖本地仓库:    
```bash
$ git fetch --all  ##将远程仓库更新到本地远程仓库，如果已经更新过则可忽略此句。
$ git reset --hard origin/master # 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致   
```
---
#### Git push    

git push命令用于将本地分支的更新，推送到远程仓库。   
```bash
$ git push origin master:master  ##<远程主机名> <本地分支名>:<远程分支名>
```
如果远程仓库比本地仓库版本高，则向远程仓库推送时Git会报错，要求先在本地做git pull合并差异，  
然后才能推送到远程仓库。这时，如果你一定要推送，可以使用--force选项。   
```bash
$ git push --force origin ##或者用-f替代,慎用该命令
```
将本地的仓库作为远程仓库的初始化仓库
```bash
$ git remote add origin https://github.com/leepengcheng/springboot-spider
$ git push -u origin master
```


#### Git Sync   
     
这个不是Git命令,是vscode的一个功能。原理应该是先pull到本地，对比版本后再      
确定是合并到本地还是push到远程仓库，点下按钮就Ok，感谢微软大法。 


