---
layout: post
title: "Hello my Blog"
subtitle: ""
author: "Marshall"
header-img: "img/post-bg-web.jpg"
header-mask: 0.3
tags:
  - 自我救赎
---

*Hello Blog*


## 记录一下开通过程

### 参照
Ref1: [fork 模版](https://github.com/Huxpro/huxpro.github.io)  
Ref2: [安装jekyll环境](https://www.jianshu.com/p/9f198d5779e6)

--- 
### 步骤
 - fork ref1项目到自己github，并clone到本地
 - 环境搭建

```sh
# mac不需要安装python，直接安装nvm（管理nodejs工具）
# install nvm 
brew install nvm
echo "source $(brew --prefix nvm)/nvm.sh" >> .bash_profile

# 查看最近node版本
nvm ls-remote --lts

# install node
nvm install --lts

# install jekyll bundler
sudo gem install jekyll
sudo gem install  bundler

# 测试，新建一个blog 并启动本地服务到 localhost:4000
sudo jekyll new myblog
cd myblog
sudo bundle exec jekyll serve # 看localhost:4000

# 如果上述都可以，就可以在之前fork好的项目路径下启动了
cd xxx.github.io
sudo bundle exec jekyll serve
```

 - 开发一个markdown文章， push到github
 - 稍后访问xxx.github.io 

