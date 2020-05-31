---
title: hexo如何搭建个人博客
date: 2019-07-04 21:00:38
categories: 其他
tags: 
    - 博客搭建 
---

## 如何利用hexo搭建个人博客

### 1. 简介

hexo是一个个人博客框架，可以在本地写好博客之后，然后同步到github上，这样就能够搭建起个人的一个博客。hexo可以通过简单的命令行操作来轻松创建博客。

### 2. 环境配置

首先在本地安装`node.js` 具体安装参考官网

安装完成之后，可以将node的源改为国内阿里镜像

`npm install -g cnpm --registry=https://registry.npm.taobao.org`

安装完成之后可以使用`cnpm`安装依赖

首先安装`hexo`框架

`cnpm install -g hexo-cli`

然后创建文件夹，如`blog`之后的所有内容都在这个文件夹中

进入到文件夹中，运行`hexo init`初始化，下载相关文件

其中`_config.yml`是配置文件

`hexo s`为启动博客，一般只是用于测试

`hexo n "我的第一篇博客"` 会在`/source/_post`文件夹下中存在着创建的文章，为md格式，然后利用编辑器编辑即可。

创建完博客之后，在`blog`目录下运行如下命令：

```bash
hexo clean  # 清理文件
hexo g     # 重新生成
hexo s     # 启动服务  然后在本地进行测试
```



### 3. 部署到github

在个人github账号中创建仓库`user_name.github.io`

得到仓库路径之后，需要配置`blod/_config.yml`

配置最后的`Deployment`，如下所示：

```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/wangdh15/wangdh15.github.io.git
  branch: master
```

然后运行`hexo d`即可完成部署

然后访问`wangdh15.github.io`即可访问到自己的博客

在每次写博客的时候，需要进行的操作是

```bash
hexo n blog_name  # 创建博客 ， 编辑内容
hexo clean  # 清空内容
hexo g  # 重新生成
hexo s  # 本地启动测试
hexo d  # 部署到远程

```



### 4. 修改主题

这里使用的是`yilia`主题，[项目地址](https://github.com/litten/hexo-theme-yilia)

具体使用过程如下：

将项目pull到`blog/themes`文件夹下

然后修改`blod/_config.yml`中的`theme`为上述主题，如下所示：

```yml
theme: yilia
```

然后重新整个博客项目即可。具体的`yilia`相关的配置，可以参考项目。



### 5. 添加图片

首先在`hexo `的配置文件中将`post_asset_folder`设置为`true`，这样在创建一个文档的时候，会创建一个同名的文件夹，可用于存放这个博客的资源。

然后安装一个插件`npm install hexo-asset-image@0.0.2 --save`

之后在笔记中直接使用md的语法，生成的文档就能够正常显示图片
