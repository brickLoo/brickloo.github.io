---
date: '2024-02-11'
title: '用 SSH 连接 GitHub'
prev: '/docs/Git 操作指南'
url: '/docs/git/ssh-and-github'
next: '/docs/Git 操作指南/删除历史 commit 记录'
---

笔记作者：BrickLoo  

## 需求

- 全新安装 Windows 操作系统之后，可以配置 SSH 来免密连接 GitHub，方便开发；

## 步骤

{{% steps %}}

### 1. 生成 SSH 密钥

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```
- 将示例中使用的邮件地址替换为 GitHub 邮件地址；
- 可以直接按回车选择默认位置保存密钥；
- 可以直接按两次回车，不创建安全密码；
  - 一般推荐创建安全密码以保证安全性，但是我没创建，不确定创建后配置和使用的繁琐程度如何……
- 如果过程中遇到任何问题，可以在后面“参考资料”的文档中看更细致的补充说明；

### 2. 在账户中添加 SSH 密钥

- 用户文件夹下，“.ssh”文件夹中，打开名为“id_ed25519.pub”的文件，复制里面完整的文本内容；
- 在 GitHub 用户设置中，找到“SSH and GPG keys”，新建 SSH 密钥并将复制的公钥粘贴进去，Title 随意，密钥类型就用默认的“Authentication Key”即可；

### 3. 测试配置是否成功

```shell
ssh -T git@github.com
```
- 正常情况下，得到的输出反馈是：  
`
Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
`  
虽然高考中 but 表转折要警惕，但是这里是正常情况，只是为了说明 ssh 语句仅用于授权测试，不用作通常的 ssh 操作，不用慌（

{{% /steps %}}

## 参考资料

- [GitHub 相关文档](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)