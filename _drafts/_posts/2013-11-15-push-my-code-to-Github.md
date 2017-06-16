---
title: Push my code to Github
categories:
  - Dev
tags:
  - Git
---

看了好多网上的例子，感觉好多都是互相抄的，对我的机子都没有用，经过自己的摸索，以下的命令是在我自己的机子上可行的。

首次在github上创建repository

```bash
cd `the directory of the repo`

git remote add origin https://github.com/usrname/repo_name # git remote set-url origin https://github.com/usrname/repo_name

git pull origin master

git push -u origin master
```

向repo实现推送

```bash
git add -A

git commit -m "okay"

git push origin master
```