# Marshall Blog

个人技术博客，基于 [Hux Blog](https://huangxuan.me) 主题构建，专注于后端开发、流计算（Apache Flink）、微服务架构和 TiDB 等技术领域。

---

## 项目概述

本项目是一个基于 Jekyll 的静态博客网站，采用响应式设计，支持多语言内容展示。经过模块化重构，具有良好的可扩展性和可维护性，方便集成第三方服务（如 Dify AI 助手）。

### 技术栈

- **Jekyll 4.0** - 静态站点生成器
- **Liquid** - 模板引擎
- **Kramdown** - Markdown 解析器（支持 GFM）
- **Rouge** - 代码语法高亮
- **Bootstrap** - 前端 CSS 框架
- **Grunt** - 前端资源编译工具（LESS、JS 压缩）
- **PWA** - 支持离线访问和渐进式 Web 应用

---

## 快速开始

### 环境要求

- Ruby 2.7+
- Bundler
- Node.js (用于 Grunt 构建)

### 安装步骤

1. **克隆项目**
```sh
git clone https://github.com/MarcelLeon/MarcelLeon.github.io.git
cd MarcelLeon.github.io
```

2. **安装 Ruby 依赖**
```sh
bundle install
```

3. **安装 Node.js 依赖**（如需修改样式或脚本）
```sh
npm install
```

4. **启动本地开发服务器**
```sh
bundle exec jekyll serve
# 或者
npm start
# 开发模式（包含 Grunt watch）
npm run dev
```

5. **访问网站**
打开浏览器访问 `http://localhost:4000`

---

## 项目架构

### 目录结构

```
MarcelLeon.github.io/
├── _config.yml              # 站点配置文件
├── _posts/                  # 博客文章（Markdown）
├── _layouts/                # 页面布局模板
│   ├── default.html        # 基础布局
│   ├── post.html           # 文章页布局
│   ├── page.html           # 普通页面布局
│   └── keynote.html        # 演讲展示布局
├── _includes/               # 可复用组件
│   ├── head.html           # HTML head 部分
│   ├── nav.html            # 导航栏
│   ├── footer.html         # 页脚
│   ├── integrations/       # 🆕 第三方集成模块
│   │   ├── analytics.html  # 分析统计（GA、百度）
│   │   ├── comments.html   # 评论系统（Disqus）
│   │   └── custom-scripts.html  # 自定义脚本（Dify等）
│   └── about/              # 多语言关于页面
│       ├── zh.md           # 中文
│       └── en.md           # 英文
├── less/                    # LESS 样式源文件
│   ├── hux-blog.less       # 主样式文件
│   └── highlight.less      # 代码高亮样式
├── js/                      # JavaScript 文件
│   ├── hux-blog.js         # 源文件
│   └── hux-blog.min.js     # 压缩后文件
├── img/                     # 图片资源
├── _config.example.yml      # 🆕 集成配置示例
└── CLAUDE.md               # Claude Code 项目指南
```

### 核心组件说明

#### 1. 博客文章系统
- 文章存放于 `_posts/` 目录
- 文件命名格式：`YYYY-MM-DD-title.md`
- 支持 YAML front matter 配置（标题、标签、封面图等）
- 支持多语言文章（通过 `multilingual` 参数）

#### 2. 布局系统
- `default.html` - 基础布局，包含导航和页脚
- `post.html` - 继承 default，添加文章特有功能（目录、评论）
- `page.html` - 普通页面布局
- 所有布局使用 Liquid 模板语言

#### 3. 资源编译
- **LESS 编译**：`less/` → `css/`
- **JS 压缩**：`js/hux-blog.js` → `js/hux-blog.min.js`
- 使用 Grunt 自动化构建

---

## 重构说明

### 🎯 重构目标

为了提升项目的可扩展性和可维护性，对项目进行了模块化重构，主要目标：

1. **模块化第三方集成** - 将分散在各处的第三方服务代码统一管理
2. **配置驱动** - 通过配置文件控制功能开关，无需修改代码
3. **为未来集成做准备** - 预留 Dify AI 助手等第三方服务的集成点

### 🔧 重构内容

#### 1. 创建模块化集成系统

**新增目录：** `_includes/integrations/`

包含三个核心模块：

- **`analytics.html`** - 分析统计模块
  - Google Analytics
  - 百度统计

- **`comments.html`** - 评论系统模块
  - Disqus
  - 网易云跟帖

- **`custom-scripts.html`** - 自定义脚本模块
  - 支持 Dify iframe/script 集成
  - 支持任意自定义 JavaScript
  - 可按页面类型加载不同脚本

#### 2. 简化布局文件

**重构前：**
```liquid
<!-- 评论系统代码直接硬编码在 post.html 中 -->
{% if site.disqus_username %}
  <!-- 大段 Disqus 代码 -->
{% endif %}
{% if site.netease_comment %}
  <!-- 大段网易云跟帖代码 -->
{% endif %}
```

**重构后：**
```liquid
<!-- 一行引入，简洁清晰 -->
{% include integrations/comments.html %}
```

**影响文件：**
- `_layouts/post.html` - 使用 comments 模块
- `_layouts/default.html` - 添加 custom-scripts 模块
- `_includes/footer.html` - 使用 analytics 模块

#### 3. 配置示例文件

创建 `_config.example.yml` 提供完整的配置示例，包括：

- 分析统计配置
- 评论系统配置
- **Dify 集成配置**（iframe 和 script 两种方式）
- 自定义脚本配置

### 📦 集成 Dify 示例

#### 方式 1：悬浮聊天窗口（iframe）

在 `_config.yml` 中添加：

```yaml
integrations:
  dify:
    enabled: true
    type: iframe
    url: "https://cloud.dify.ai/chatbot/your-bot-id"
    style: "position: fixed; bottom: 20px; right: 20px; width: 350px; height: 500px; border: none; box-shadow: 0 2px 10px rgba(0,0,0,0.2); z-index: 9999;"
```

#### 方式 2：嵌入式脚本（script）

```yaml
integrations:
  dify:
    enabled: true
    type: script
    url: "https://cloud.dify.ai/embed.js"
    init_script: |
      window.difyConfig = {
        appId: 'your-app-id',
        title: 'AI Assistant'
      };
```

### ✨ 重构优势

1. **代码复用** - 同一功能只写一次，多处引用
2. **易于维护** - 修改第三方服务配置只需改一个文件
3. **高扩展性** - 添加新服务无需修改布局，只需：
   - 在 `_config.yml` 添加配置
   - 在集成模块中添加逻辑
4. **配置隔离** - 服务配置与代码分离，便于版本控制
5. **向后兼容** - 保持原有配置项不变，无需大规模修改

---

## 开发指南

### 写文章

在 `_posts/` 目录创建新文件，格式：`YYYY-MM-DD-title.md`

**示例：**

```markdown
---
layout: post
title: "我的第一篇博客"
subtitle: "副标题"
author: "Marshall"
header-img: "img/post-bg.jpg"
header-mask: 0.3
tags:
  - 技术
  - 学习
---

文章正文使用 Markdown 编写...
```

### 修改样式

1. 编辑 `less/` 目录下的 LESS 文件
2. 运行 `grunt` 编译为 CSS
3. 或使用 `grunt watch` 自动监听变化

### 修改脚本

1. 编辑 `js/hux-blog.js`
2. 运行 `grunt` 压缩为 `hux-blog.min.js`

### 添加第三方服务

参考 `_includes/integrations/README.md` 和 `_config.example.yml`

---

## 配置说明

### 基础配置

在 `_config.yml` 中修改：

```yaml
title: 你的博客名称
SEOTitle: SEO 标题
description: 博客描述
keyword: 关键词
url: "https://yourdomain.com"
```

### 社交账号

```yaml
github_username: your-github
twitter_username: your-twitter
zhihu_username: your-zhihu
```

### 分析统计

```yaml
ga_track_id: 'UA-xxxxxx-xx'  # Google Analytics
ba_track_id: 'your-id'       # 百度统计（可选）
```

### 评论系统

```yaml
disqus_username: your-disqus-shortname
```

---

## 测试与构建

### 本地测试

```sh
bundle exec jekyll serve
# 访问 http://localhost:4000
```

### 构建静态文件

```sh
bundle exec jekyll build
# 输出到 _site/ 目录
```

### 前端资源编译

```sh
# 一次性编译
grunt

# 监听文件变化自动编译
grunt watch
```

---

## 部署

本项目配置为 GitHub Pages 部署：

1. 推送代码到 `master` 分支
2. GitHub 自动构建并部署
3. 访问 `https://yourusername.github.io`

**自定义域名：**
- 修改 `CNAME` 文件
- 在域名 DNS 设置中添加 CNAME 记录

---

## 项目特性

- ✅ 响应式设计，支持移动端
- ✅ PWA 支持，可离线访问
- ✅ 代码语法高亮（支持多种语言）
- ✅ 文章目录自动生成
- ✅ 标签云系统
- ✅ 多语言支持
- ✅ 搜索功能
- ✅ Google Analytics / 百度统计
- ✅ Disqus 评论系统
- ✅ **模块化第三方集成系统** 🆕
- ✅ **Dify AI 助手支持** 🆕

---

## 常见问题

### 1. 如何禁用某个第三方服务？

在 `_config.yml` 中注释或删除对应配置即可，例如：

```yaml
# disqus_username: your-username  # 注释掉即禁用
```

### 2. 如何添加新的自定义脚本？

在 `_config.yml` 的 `integrations.custom_scripts` 数组中添加：

```yaml
integrations:
  custom_scripts:
    - name: "My Script"
      enabled: true
      type: external
      url: "https://example.com/script.js"
      position: all
```

### 3. Jekyll 构建失败怎么办？

检查：
- Liquid 语法是否正确
- YAML front matter 格式是否规范
- 文件编码是否为 UTF-8
- 查看错误日志定位问题

---

## 参考资源

- [Jekyll 官方文档](https://jekyllrb.com/)
- [Liquid 模板语法](https://shopify.github.io/liquid/)
- [Kramdown 语法](https://kramdown.gettalong.org/)
- [Rouge 支持的语言列表](https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers)
- [原始主题 - Hux Blog](https://github.com/Huxpro/huxpro.github.io)

---

## License

Apache License 2.0.
Copyright (c) 2015-present Huxpro

本博客基于 [Hux Blog](https://github.com/Huxpro/huxpro.github.io) 开发，该主题源自 [Clean Blog Jekyll Theme (MIT License)](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll/)

---

## 更新日志

### v2.0.0 (2025-11-02)

**重构优化**
- 🎨 重构第三方集成系统，模块化设计
- ✨ 新增 `_includes/integrations/` 目录
- ✨ 新增 Dify AI 助手集成支持
- ✨ 新增自定义脚本配置系统
- 📝 更新项目文档和配置示例
- ♻️ 简化 layout 文件，提升可维护性

**技术提升**
- 配置驱动的集成管理
- 支持 iframe 和 script 两种集成方式
- 可按页面类型加载不同脚本
- 完善的文档和示例

---

## 贡献

欢迎提交 Issue 和 Pull Request！

## 联系方式

- Email: wangzq0708@gmail.com
- GitHub: [@MarcelLeon](https://github.com/MarcelLeon)
