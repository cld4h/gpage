---
title: hexo 的配置及使用
date: 2019-09-17 12:02:24
tags:
---

[hexo documentation](https://hexo.io/docs/)

## 安装

```sh
sudo pacman -S git npm
sudo npm install -g hexo-cli
```

查看全局环境下安装的npm软件包

```sh
npm list -g --depth 0
```

安装完成hexo之后，运行下面的命令以在`<folder>`中初始化hexo

```
hexo init <folder>
cd <folder>
npm install
```

## 配置

### 安装NEXT主题

进入到 `<folder>` 目录

```sh
mkdir themes/next
curl -s https://api.github.com/repos/theme-next/hexo-theme-next/releases/latest | grep tarball_url | cut -d '"' -f 4 | wget -i - -O- | tar -zx -C themes/next --strip-components=1
```

### 插件安装--git部署插件
```sh
npm i --save hexo-deployer-git
```

### 插件安装--asciidoctor渲染插件
```sh
npm i --save hexo-renderer-asciidoc
```

### 插件安装--`hexo-renderer-mathjax` 插件
```sh
npm i --save hexo-renderer-mathjax
```

### 插件替换--markdown渲染插件(为支持MathJax)
```sh
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

### 配置`_config.yml`(包括MathJax配置)

#### NEXT 主题配置文件
```yml
# Math Formulas Render Support
math:
  enable: true

mathjax:
  enable: true

mathjax: //cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML
```

#### 主配置文件
```yml
title: cld4h's blog
author: Xu Hao
language: zh
url: https://cld4h.github.io
permalink: :title/
skip_render: source/_posts/_static

# 支持在 `_posts` 目录下存放图片等asset
post_asset_folder: true

deploy:
  type: git
  repo: git@github.com:cld4h/cld4h.github.io.git

math.mathjax:
  enable: true
```


#### 修改LaTeX 和 MarkDown 的语法冲突

打开 `node_modules/kramed/lib/rules/inline.js`，将`escape`和`em`进行替换：

将
```js
escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
```
替换为
```js
escape: /^\\([`*\[\]()# +\-.!_>])/,
```

将
```js
em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```
替换为
```js
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

> 需要用到LaTeX的文章在头部开启mathjax
```
---
mathjax: true
---
```

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

