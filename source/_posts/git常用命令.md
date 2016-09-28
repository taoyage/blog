---
title: git常用命令
date: 2016-09-28 23:41:04
categories: 前端开发工具
tags: 
  -- git
  -- github
---


### 初始化本地git仓库

`$ git init`

### 查看当前仓库状态

`$ git status`

### 查看修改日志

`$ git log`

### 恢复到某个版本

`$ git reset --hard HEAD`(HEAD 指的是通过`$ git log`命令来获取的commit的后6位值)

### 添加所有文件到缓存

`$ git add --all`

### 提交修改信息

`$ git commit -m "修改信息"`

### 推送到远程仓库

`$ git push -u origin master`

### 从远程仓库获取数据

`$ git pull origin master`

### 列出所有分支

`$ git branch`

### 创建新分支

`$ git branch name`

### 切换分支

`$ git checkout name`

### 删除本地分支

`$ git branch -d name`

### 删除远程分支

`$ git push origin : name`





