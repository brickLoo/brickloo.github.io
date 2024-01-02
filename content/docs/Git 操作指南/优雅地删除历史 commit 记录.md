---
date: '2024-01-03'
title: '优雅地删除历史 commit 记录'
prev: '/docs/Git 操作指南/'
---

## 需求

- GitHub Pages 托管的网站由于是公开仓库，涉及个人隐私的历史 commit 记录希望删除；
- 删除 `.git/` 文件夹在大部分情况下是可行的方案，但是在使用到 submodules 的时候可能会遇到问题；

## 步骤

{{% steps %}}

### 1. 按需备份 `.git/` 文件夹

### 2. 创建全新分支

```shell
git checkout --orphan newBranch
```

### 3. 提交所有本地文件

```shell
git add -A  # Add all files and commit them
git commit
```

### 4. 删除旧分支

```shell
git branch -D main  # Deletes the main branch
```

### 5. 将新分支重命名为旧分支

```shell
git branch -m main  # Rename the current branch to main
```

### 6. 彻底删除旧分支记录

```shell
git gc --aggressive --prune=all  # remove the old files
git reflog expire --expire-unreachable=all --all  # free disk space
```

### 7. 使用强制推送上传到 GitHub

```shell
git push -f origin main  # Force push main branch to github
```

{{% /steps %}}

## 参考资料

- [stack overflow 相关问答](https://stackoverflow.com/questions/9683279/make-the-current-commit-the-only-initial-commit-in-a-git-repository)