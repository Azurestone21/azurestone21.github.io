---
title: Git修改commit注释
date: 2022-07-11 16:05:04
categories: [Git]
tags: [Git]
---

## 修改最新提交的注释（还没有push）

1. `git commit --amend`【第一行显示注释】
2. 输入 `i`【进入vim编辑模式，修改完成】
3. `Esc`【退出编辑模式】
4. `:wq` 或 `ZZ`【保存并退出即可】
5. `git log`【查看提交记录】

## 修改历史提交的注释

1. `git rebase -i HEAD~2`【数字指的是倒数第n次提交记录的注释】
2. `pick` 改成 `edit`【输入`i` 编辑模式，将你需要修改的注释前的pick改为edit】
3. `Esq`【退出编辑模式】
4. `:wq`【保存退出】
5. `git commit --amend`【同上有提示，第一行进行你真正需要的修改，修改完后，保存退出】
6. `git rebase --continue`【退出后，输入最后一步】
