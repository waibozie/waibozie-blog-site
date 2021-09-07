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
