<div align="center">
  
# 🍃 淡若风云博客 🍃

### [fy.ll.sd](https://fy.ll.sd)

</div>

基于 [Congo](https://github.com/jpanther/congo) 主题的 Hugo 静态博客网站。

## 🔮 自定义功能和改进

主题在原有Congo功能基础上进行了多项定制和增强，包括图片处理、布局优化和多语言支持等。

### 图片处理增强

#### 自定义图片参数

增加了以下自定义图片参数：

1. **urlImage**: 支持使用外部URL图片链接作为文章特色图片
   ```yaml
   ---
   title: "文章标题"
   urlImage: "https://example.com/image.jpg"
   ---
   ```

2. **staticImage**: 支持使用静态目录下的图片作为文章特色图片
   ```yaml
   ---
   title: "文章标题"
   staticImage: "/images/feature.jpg"
   ---
   ```

### 布局优化

1. **目录样式优化** ✨: 目录使用边框线分隔，提高可读性
   ```html
   <div class="toc pe-5 lg:sticky lg:top-10 print:hidden border-s border-dotted border-neutral-300 dark:border-neutral-600">
   ```

2. **首页文章列表增强** 📝: 首页支持显示多种内容类型
   ```toml
   [homepage]
     showRecent = ["post", "ai"]
     recentLimit = 5
   ```

### 多语言支持扩展

完整支持简体中文(zh-Hans)、英文、法语(fr)、泰米尔语(ta)等多种语言。

## 📘 主题使用指南

### 配置文件

配置文件位于 `config/_default/` 目录下：

1. **config.toml** - 基本站点配置
2. **params.toml** - 主题参数配置
3. **languages.zh-Hans.toml** - 中文语言配置
4. **menus.zh-Hans.toml** - 中文菜单配置
5. **markup.toml** - Markdown渲染配置
6. **module.toml** - Hugo模块配置

### 常用参数设置

#### 外观设置
```toml
# 颜色方案
colorScheme = "congo"
# 默认外观：light(亮色)或dark(暗色)
defaultAppearance = "light" 
# 是否自动切换外观
autoSwitchAppearance = true
```

#### 功能开关
```toml
# 是否启用搜索功能
enableSearch = true
# 是否启用代码复制按钮
enableCodeCopy = true
# 是否启用图片懒加载
enableImageLazyLoading = true
# 是否启用WebP图片格式
enableImageWebp = true
```

#### 页面布局
```toml
# 头部设置
[header]
  # 布局类型：basic(基本), hamburger(汉堡菜单), hybrid(混合), custom(自定义)
  layout = "basic" 
  logo = "img/logo.jpg"
  logoDark = "img/dark-logo.jpg"
  # 是否显示标题
  showTitle = true
```

### 文章前置参数

使用以下参数自定义文章显示：

```yaml
---
title: "文章标题"
date: 2023-01-01
# 文章封面图
cover: "cover.jpg"
# 或使用外部URL图片
urlImage: "https://example.com/image.jpg"
# 或使用静态目录图片
staticImage: "/images/feature.jpg"
# 是否显示目录
showTableOfContents: true
# 是否显示评论
showComments: false
# 分类和标签
tags: ["标签1", "标签2"]
categories: ["分类1"]
---
```

## 🚀 原始Congo功能

### 短代码使用

主题支持多种短代码增强内容展示：

#### 按钮

```markdown
{{< button href="https://example.com" target="_blank" >}}
访问网站
{{< /button >}}
```

#### 图片

```markdown
{{< figure
  src="image.jpg"
  alt="图片描述"
  caption="图片标题"
  default=true
>}}
```

#### 图标

```markdown
{{< icon "github" >}}
```

#### 提示框

```markdown
{{< alert >}}
这是一条重要提示信息
{{< /alert >}}
```

### 官方文档

更多详细信息请参考 Congo 主题官方文档：

- 🔗 [安装](https://jpanther.github.io/congo/zh-hans/docs/installation/)
- 🔗 [快速开始](https://jpanther.github.io/congo/zh-hans/docs/getting-started/)
- 🔗 [基本配置](https://jpanther.github.io/congo/zh-hans/docs/configuration/)
- 🔗 [主页布局](https://jpanther.github.io/congo/zh-hans/docs/homepage-layout/)
- 🔗 [Front Matter](https://jpanther.github.io/congo/zh-hans/docs/front-matter/)
- 🔗 [短代码](https://jpanther.github.io/congo/zh-hans/docs/shortcodes/)
- 🔗 [Partials](https://jpanther.github.io/congo/zh-hans/docs/partials/)
- 🔗 [内容示例](https://jpanther.github.io/congo/zh-hans/docs/samples/)
- 🔗 [高级定制](https://jpanther.github.io/congo/zh-hans/docs/advanced-customisation/)
- 🔗 [部署](https://jpanther.github.io/congo/zh-hans/docs/hosting-deployment/)
