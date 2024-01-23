---
date: 2024-01-22
authors:
  - P1sc3s007
categories:
  - Git
tags:
  - Git
slug: git
---
# Git
Git的使用教程
![image.png](pic/git1.png)
<!-- more -->
## 版本控制
版本迭代，版本管理器
## 本地版本控制
![image.png](pic/git1.png)

## 集中版本控制
![image.png](pic/git2.png)<br />![image.png](pic/git‘.png)

## 分布式版本控制
![image.png](pic/git3.png)<br />每个人拥有全部代码<br />![image.png](pic/git4.png)
# Git配置
![image.png](pic/git5.png)<br />查看配置：`git config -l`<br />本地配置：`git config --system --list`<br />全局配置：`git config --global --list`<br />**Git相关配置文件**

1. **Git\etc\gitconfig：系统级**
2. **C:\Users\Administrator\.gitconfig 只适用于当前用户的配置（全局）**

设置用户名：`git config --gloabal user.name "名称"`<br />设置邮箱：`git config --gloabal user.email "邮箱"`
## Linux命令
![image.png](pic/git6.png)
# Git理论
![image.png](pic/git7.png)<br />![image.png](pic/git8.png)<br />流程：本地—>添加到暂存区—>commit提交到本地git仓库—>push到远程仓库<br />![image.png](pic/git9.png)<br />![image.png](pic/git10.png)<br />2：`git add .`<br />3：`git commit`
# Git项目搭建
![image.png](pic/git11.png)
## 本地仓库搭建
创建全新仓库：项目根目录运行 `git init`
## 克隆远程仓库
`git clone [url]`
# Git文件操作
![image.png](pic/git12.png)<br />查看文件状态

- `git status [filename]`
- `git status`
- `git add . `      添加所有文件到暂存区
- `git commit -m “消息内容”`    提交暂存区内容到本地仓库（-m 提交信息）

![image.png](pic/git13.png)
# Github
![image.png](pic/git14.png)<br />![image.png](pic/git15.png)
