---
title: SpringMVC之ResponseBody
date: 2017-01-15 16:22:31
categories: "Spring"
tags: ["Spring","SpringMvc"]
---
* **Git pull**     

git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。      
```
$ git pull origin master:master 
```
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

```
$ git pull origin master ##取回远程仓库的master分支并和当前分支合并  
``` 

<!--more-->

等价于:
```bash
$ git fetch origin  ##取回远程仓库的所有分支的更新(后面可指定分支)
$ git merge origin/master ##将当前的分支和远程仓库的master分支合并
```
当本地对本地分支进行更新并提交后，如果本地仓库比远程仓库的版本更高时，会产生以下错误 
```bash
 fatal: refusing to merge unrelated histories
```
这时有两种方案：
强行合并远程和本地仓库:
```bash
$ git pull origin master --allow-unrelated-histories 
```
强行用远程仓库覆盖本地仓库:   
```bash
git fetch --all  ##将远程仓库更新到本地远程仓库，如果已经更新过则可忽略此句。
git reset --hard origin/master # 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
```

*  **Git push**      

git push命令用于将本地分支的更新，推送到远程仓库。   
```bash
$ git push origin master:master  ##<远程主机名> <本地分支名>:<远程分支名>
```
如果远程仓库的的比本地仓库版本更新，向远程仓库推送时Git会报错，要求先在本地做git pull合并差异，  
然后才能推送到远程仓库。这时，如果你一定要推送，可以使用--force选项。   
```bash
$ git push --force origin ##或者用-f替代,慎用该命令
```