---
date: '2024-05-21'
lastmod: '2024-05-21'
title: '软件包管理'
prev: '/docs/Fedora 使用技巧'
url: '/docs/fedora/dnf'
weight: '1'
---

笔记作者：BrickLoo

## 更新软件

软件包更新命令：
```shell
sudo dnf upgrade
```

由于网络状况因素，部分软件源可能不能正常更新，导致整个更新进程停滞。例如笔者在连接校园网的情况下，无法从 RPM Fusion 的软件源正常更新，因此这时可以修改命令以暂时禁用不能正常使用的软件源：
```shell
sudo dnf upgrade --disablerepo=rpmfusion*
```
`disablerepo` 参数的影响只有本条命令，只要下次更新不带这个参数就依然是正常地从所有软件源更新。

## 查看软件源

为了确认软件源名字，使得我们在使用 `disablerepo` 参数时能够正确地禁用软件源，我们可以使用以下命令列出当前的所有软件源：
```shell
dnf repo list
```