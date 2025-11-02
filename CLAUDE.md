# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based personal blog (MarcelLeon.github.io) forked from Hux Blog. The blog focuses on backend development, stream processing (Apache Flink), microservices, and TiDB, written primarily in Chinese with some English content.

## Development Commands

### First-Time Setup
```sh
# Install Ruby dependencies
bundle install

# Install Node.js dependencies for Grunt
npm install
```

### Local Development
```sh
# Serve the site locally at localhost:4000
bundle exec jekyll serve
# OR
npm start

# Development mode with live reload and Grunt watch (recommended)
npm run dev
```

### Build and Asset Management
```sh
# Compile LESS to CSS and minify JavaScript (run after modifying styles or scripts)
grunt

# Watch for changes and auto-compile (runs in background during development)
grunt watch
```

## Architecture

### Jekyll Structure
- **`_posts/`**: Blog posts in Markdown, named `YYYY-MM-DD-title.md`
  - Front matter includes: layout, title, subtitle, author, header-img, header-mask, tags
- **`_layouts/`**: Page templates (default, page, post, keynote)
- **`_includes/`**: Reusable HTML components (nav, footer, head, sidebar, etc.)
  - `_includes/about/`: Multilingual about pages (en.md, zh.md)
- **`_config.yml`**: Site configuration (title, SEO, social links, analytics, sidebar settings)

### Theme Customization
- **LESS sources**: `less/` directory contains all styles
  - Main file: `less/hux-blog.less` (imports all other LESS files)
  - Syntax highlighting: `less/highlight.less` (compatible with Pygments themes)
  - Other modules: `mixins.less`, `variables.less`, `sidebar.less`, `search.less`, etc.
  - **IMPORTANT**: Always run `grunt` after modifying LESS files to compile to CSS
- **JavaScript**: Source in `js/hux-blog.js`, compiled to `js/hux-blog.min.js`
  - **IMPORTANT**: Always run `grunt` after modifying JS to minify
- **Grunt tasks**: Defined in `Gruntfile.js`
  - `uglify`: Minifies JavaScript from source to .min.js
  - `less`: Compiles LESS to CSS (both expanded and minified versions)
  - `usebanner`: Adds Apache 2.0 license banner to compiled files
  - `watch`: Auto-compiles on file changes

### Key Technologies
- **Jekyll 4.0**: Static site generator (with jekyll-paginate plugin)
- **Liquid**: Template engine for layouts and includes
- **Kramdown**: Markdown processor with GFM (GitHub Flavored Markdown) input
- **Rouge**: Syntax highlighter with line numbers enabled for code blocks
- **Bootstrap**: CSS framework (older version, inherited from theme)
- **Grunt**: Task runner for asset compilation (LESS, JS minification, watch)

## Configuration Notes

- **Domain**: Configured in `CNAME` file at repository root
- **Analytics**: Google Analytics (ga_track_id) and optional Baidu Analytics in `_config.yml`
- **Comments**: Disqus enabled (username: marshall), Netease comments option available
- **PWA**: Service worker enabled (`sw.js`), Chrome tab theme color configured
- **Pagination**: 10 posts per page
- **Sidebar**: Enabled by default with avatar, description, and featured tags
- **Future posts**: `future: true` allows posts with future dates to be published
- **Multilingual**: About page supports English (en.md) and Chinese (zh.md)

## Writing Blog Posts

Create new posts in `_posts/` following the naming convention `YYYY-MM-DD-title.md` with proper front matter:
```yaml
---
layout: post
title: "Your Title"
subtitle: "Optional subtitle"
author: "Marshall"
header-img: "img/your-image.jpg"
header-mask: 0.3
tags:
  - tag1
  - tag2
---
```