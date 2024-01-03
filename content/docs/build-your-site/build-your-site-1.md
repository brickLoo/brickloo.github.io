---
date: '2024-01-02'
title: '一、主题配置和部署'
prev: '/docs/build-your-site/'
---

作者：BrickLoo  
摘要：本文记录了从配置好 Git，Go 以及 Hugo 后，一直到成功打开部署在 GitHub 上个人网页的流程和相关笔记。本文仅包含基础部署流程，最后的效果是能够打开样例页面，尚未包含到个人博客的迁入部分。

## 在本地创建个人网站项目

关于静态网页生成框架的选择，目前在中文互联网环境下了解到的主要是 Hexo 和 Hugo。通过网上的各种文章简单对比了一下两者：因为功能上暂时没有明确的需求，基本的用途两者都能满足，但是 Hugo 生成速度更快，这个应该是日常感知比较明显的，所以最终选择了 Hugo。

我首先是照着 Hugo 官方的[快速入门教程](https://gohugo.io/getting-started/quick-start/)走了一遍，然后再正式开始搭建自己的网页。跟着教程在本地创建项目时，第二个指令就是安装主题。因此在正式开始搭建个人博客之前，我把 Hugo 的[主题列表](https://themes.gohugo.io/)翻了一遍。一通对比之下，我个人对 Hextra 的结构和 UI 都比较心动，所以选择用 Hextra 作为博客的 UI 主题。对于看到这篇文章的其他读者，我个人会比较鼓励你看看有没有更心动的其他主题，然后仅把我的这篇记录文档的操作和流程作为参考，因为其他主题或许会有更让你心动的 UI 风格，以及可能会有更加详细和清晰的使用文档（，这样我也少了一个撞主题的人，嘿嘿。）

在查看 Hextra 的[官方文档](https://imfing.github.io/hextra/docs/getting-started/)的时候，我发现里面创建本地项目的步骤已经比较完整了，不需要再对照 Hugo 的文档。文档里提供了几种使用这个主题开始网站项目的方式：第一种方法是从官方模板开始，但是个人感觉不太确定这玩意应该怎么用，而且里面有多平台的配置文件比较多余，所以没用这种方式，只是抄了一下模板里提供的 Actions workflow；另外两种方法分别是 **Git Submodule** 和 **Hugo Modules**，Hugo 的[快速入门教程](https://gohugo.io/getting-started/quick-start/)使用的是 **Git Submodule**，但是 Hextra 的官网更推荐 **Hugo Modules**，所以最后选择了这种方法创建本地项目，不过就是指令敲得有点不明所以（<mark> TODO：学习 [hugo mod](https://gohugo.io/hugo-modules/) 和 Go 的相关知识 </mark>）

{{% steps %}}

### 1. 创建项目文件夹

```shell
hugo new site my-site --format=yaml
```

- 这个命令会根据 Hugo 的[模板目录结构](https://gohugo.io/getting-started/directory-structure/)自动创建项目文件夹
- 项目文件夹名将被自动命名为 my-site（感觉还挺合适的就不改了）
- Hextra 官方的命令选择使用 yaml 作为项目配置文件的格式（Hugo 的默认是 toml 不过反正都不太熟，yaml 就 yaml 吧）
	- 不过默认的模板文件（archetypes/default.md）似乎不会根据这个格式进行对应的更改，可以根据[ Hugo 官方文档](https://gohugo.io/content-management/archetypes/)手动修改一下

### 2. 获取主题

- 首先进行模块的初始化。根据网上的[文章](https://zhuanlan.zhihu.com/p/394103953)这里的 `github.com/username/my-site` 是模块名，可以不写成这个格式，但是写成这个格式方便其他项目引用。好像个人网站也不需要别人引用，但是可能还是按照官方给的指令来稳妥一点。记得把指令里的 username 给换一下。（不确定这里是否区分大小写，但是上网搜了一下发现大家 username 一般采用全小写，稳妥起见我还是直接把 GitHub 的 username 改成全小写好了）

```shell
# initialize hugo module
cd my-site
hugo mod init github.com/username/my-site
```

- 然后获取 Hextra 主题

```shell
# add Hextra theme
hugo mod get github.com/imfing/hextra
```

- 再在 `hugo.yaml` 加入如下配置：

```yaml
module:
  imports:
    - path: github.com/imfing/hextra
```

- 这里补充一个知识点：处于不同编辑器或工具对 tab 存在处理差异，且缩进在 YAML 中有比较重要的地位，所以官方禁用了 tab 来缩进的行为。缩进需要使用空格，通常选择 2 或 4 个空格。

### 3. 创建样例文件及效果预览

Hextra 官方文档中虽然给出了创建样例文件和预览的代码，但是只创建了空白内容，预览的时候也是一片空白，后续上传到 GitHub 上的时候也不好检查有没有问题。所以这里选择下载[ Hextra 主题官方模板](https://github.com/imfing/hextra-starter-template)的项目，然后从项目里把示例的网页内容复制到自己的项目里。

看了一下模板里的文件，只需要将 content 文件夹整个搬来就能通过 `hugo server` 指令预览到效果。另外可以从 `hugo.yaml` 里面复制一点有用的配置项，顺便改一下里面的 baseURL，languageCode 和 title。修改完内容和配置文件后，只需要刷新一下页面就可以看到效果。

{{% /steps %}}

## 知识铺垫：GitHub Pages

想要把本地的静态网站发布到网上让大家看到，网上大家都比较推荐的方法是上传到 GitHub 上并开启 GitHub Pages。这样做具体的效果是，别人可以通过“个人网站地址 + 项目名”的链接格式打开这个项目的网页（具体链接见下文步骤 3）。

步骤：
1. 进入项目的 **Settings**，并在选项列中，找到 **Code and automation** 部分的 **Pages**；
2. 检查其中的设置，确保当前的 **Source** 选项栏选择的是 "Deploy from a branch" ，然后 **Branch** 选项栏选择 `main`，保存；
3. 刷新页面（可能需要等待一会），然后可以在项目 **Settings** 的 **Pages** 中找到 **Visit site** 按钮，点击即可访问已经启用的 GitHub Pages（具体的链接也就是会在这里显示了）
4. GitHub Pages 会依次寻找 `index.html`，`index.md`，`README.md` 并选择最先找到的那个文件作为项目网页启动时展示的首页。注意需要把这个首页文件放在第 2 步设置时选择的位置（默认在项目根目录），而不是这个位置的子目录中。

## 将本地的个人网站部署到 GitHub

{{% steps %}}

### 1. 在 GitHub 上创建项目

使用 `username.github.io` 为名字创建项目，此时的 GitHub Pages 的链接就是 `username.github.io` 而不带项目名，其中 username 替换为自己的用户名。

可以注意一下创建项目的选项，不要让 GitHub 在项目里自动创建文件，得到一个空的项目目录，这时就可以看到 GitHub 官方的上传提示了，可以结合官方的上传提示完成后面的步骤。但是这里先不急着上传，可以完成后面的步骤再一起上传。

### 2. 配置网站的部署工作流

参考 Hugo 官方的[部署教程](https://gohugo.io/hosting-and-deployment/hosting-on-github/)并根据 Hextra 官方文档中的[部署教程](https://imfing.github.io/hextra/docs/guide/deploy-site/)，将[ Hextra 主题官方模板](https://github.com/imfing/hextra-starter-template)中的 `.github` 目录整个拷贝到自己的项目文件夹中，然后根据文档和需求修改配置中的 baseURL 项。感觉文档里的部署脚本维护得不够模板里的及时，所以不要直接复制文档里面的配置。

这段 workflow 脚本的主要功能是让用户在每次将新内容 push 到 GitHub 上的时候自动生成静态页面文件（也就是通过命令行直接运行 `hugo` 时生成的 public 文件夹里的内容），然后自动把这些静态页面文件部署成 GitHub Pages。所以上传项目时只需要以整个项目文件夹为根目录进行 `git init` 和上传即可，不需要单独上传 public 文件夹。（至于细节是怎么实现的，workflow 还干了啥，就不是很懂了，回头再学学吧）（<mark> TODO：学习 [GitHub Action](https://docs.github.com/en/actions) 的相关知识 </mark>）

### 3. 调整项目设置为工作流部署

参考 GitHub Pages 的知识铺垫，在 GitHub 项目中进行设置。但是第二步不选择 "Deploy from a branch"，而是选择 GitHub Action。选择后没有额外的保存按钮，是自动保存的。也不需要再根据弹出的提示选择对应 workflow 进行新建，我们再下一步把我们准备好的本地 workflow 上传上去即可。

### 4. 上传本地的项目内容

结合 GitHub 官方的上传提示，使用以下指令上传项目：

```shell
git init
git add -A
git commit -m "first commit"
git branch -M main
git remote add origin <项目 SSH 地址>
git push -u origin main
```

- `push` 的参数 `-u` 似乎是为了能让后续可以直接 push 而不带其他参数的（参考了这篇[教程](https://www.yiibai.com/git/git_push.html)的描述，不过好像还不是很懂）（<mark> TODO：学习 [Git](https://git-scm.com/doc) 的相关知识 </mark>）
- 上传完之后就会自动触发 workflow 脚本的自动执行，稍等片刻就能通过网址看到内容了，至此大功告成。

{{% /steps %}}

## 后续维护

可以通过更新 Hugo 模块的方式更新主题。以下命令可以更新项目中的所有模块：

```shell
hugo mod get -u
```

或者如果只想更新这个 UI 主题的对应模块，则使用以下命令：

```shell
hugo mod get -u github.com/imfing/hextra
```

## 参考资料

- [Hugo 官方文档](https://gohugo.io/documentation/)
- [Hextra 主题官方文档](https://imfing.github.io/hextra/docs/getting-started/)
- [Hextra 主题官方模板](https://github.com/imfing/hextra-starter-template)
- [GitHub Pages 官方文档](https://docs.github.com/en/pages)
- [GitHub Pages 官方入门教程](https://github.com/skills/github-pages)
- [【知乎】golang 系列: go mod 使用](https://zhuanlan.zhihu.com/p/394103953)