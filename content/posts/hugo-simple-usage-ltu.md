---
title: "hugo使用入门（LTU，持续更新中）"
date: 2021-09-06T21:56:24+08:00
draft: false
---

这是一个简单的hugo使用入门总结，也是承载我后续持续输出博客的工具。我并不打算了解hugo的所有特性、细节，只要能支持本博客的相关需求就好，所以标题叫“使用入门”， 我会持续更新本博客用到的hugo能力。由衷地感谢[hugo](https://gohugo.io/)为我们贡献如此优秀的静态博客生产工具。

## 一、安装与初步使用

根据hugo文档的指引，我使用homebrew完成了hugo的安装

``` shell
brew install hugo
```

hugo的使用通过CLI方式完成， 使用到了如下命令：

1. 创建site

``` shell
# 将博客创建到waibozie目录下
hugo new site waibozie
```

1. 引入主题（theme）

```shell
cd waibozie
# 初始化git， 将theme当成一个子module引入，方便后续更新
git init .
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

# 配置当前site使用的主题为ananke: 在config.toml文件中新增theme = 'ananke'配置
vim config.toml
```

1. 创建第一篇博客

```shell
hugo new posts/hugo-simple-usage.md
```

1. 启动hugo server

```shell
# -D为告知hugo渲染draft类型的博客，默认情况下hugo会忽略draft
hugo server -D
```

以LiveReload的方式编辑文档，保存后即可在 <http://localhost:1313> 中“实时”看到效果（hugo编译很快，几乎感觉不到延迟，这点真的赞）。

## 二、使用hugo创建github pages

本小结总结hugo生成的静态网页文件托管到github并创建属于个人的github pages的过程

1. 在github账户下创建仓库  
新增仓库：`<username>.github.io`, 其中username为github的用户名，如我的仓库叫[waibozie.github.io](https://github.com/waibozie/waibozie.github.io)

1. 关联github pages仓库到博客内容仓库  
将上一步创建的`<username>.github.io`作为module引入到博客内容关联的代码仓中

```shell
 git submodule add git@github.com:waibozie/waibozie.github.io.git public
```

这里我将module定义到`public`目录的原因是因为hugo渲染的内容默认也是生成到`public`目录下， 这样每次渲染就相当于对`<username>.github.io`仓库做修改，后续我只需做一个`git commit` 和 `git push`就可以直接更新github page的显示了。

1. 修改博客baseURL以指向github pages  
修改博客内容仓库中的`config.toml`， 使hugo渲染的页面能在github pages上如预期工作。

```toml
baseURL = 'https://waibozie.github.io/'
```

本地通过 `hugo server -D` 启动预览时，hugo会忽略这个baseURL配置，所以不用担心对本地文章创作的影响。

1. 渲染博客并提交渲染结果  
接下来就可以渲染博客，并提交到github pages查看效果了。

```shell
# 进入博客内容管理的根目录 
hugo
cd public
git commit -m <message>
# git push <origin> <branch>
git push origin main
```

 通过如下网址访问github pages: `https://<username>.github.io`, 如我的博客<https://waibozie.github.io>
