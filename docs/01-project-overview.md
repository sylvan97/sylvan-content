# 项目定位与核心理念

> 来源：`README.md`、`sylvan-content-需求说明.md`

## 一句话描述

一个专门存内容的 Git 仓库，所有文章写成 `.md` 文件，图片视频放七牛云 CDN。不用打开 Notion，在 Obsidian 里直接写，推上去就行。网站、APP、任何人想读内容，都从这个仓库来。

## 给谁用

作者 sylvan（zzffan），个人项目。

## 解决什么问题

| 问题 | 方案 |
|------|------|
| Notion 对 livephoto 等富媒体支持差 | 内容在纯文本 `.md`，媒体在 CDN |
| 内容被锁在别人平台 | Git 仓库，平台无关 |
| 图片签名链接会过期 | 七牛云 CDN，路径稳定 |
| 无法在 Obsidian 和网站上共享同一份内容 | 仓库即事实来源，各端各自消费 |

## 核心设计原则

1. **纯文本仓库**：只提交 `.md` 文件，二进制文件不走 Git
2. **目录即 URL**：`content/` 后的路径直接映射到 URL
3. **平台无关**：同一份 `.md` 文件可在 Obsidian、sylvan-canopy、GitHub、任意渲染器中呈现
4. **标准化 frontmatter**：每篇文章有统一的 YAML 元数据头
5. **CDN 媒体**：所有图片/视频使用完整 CDN URL，不在仓库中存储

## 消费端

| 消费端 | 读取方式 |
|--------|---------|
| **Obsidian** | 以 vault 打开此仓库；frontmatter 在属性面板展示 |
| **sylvan-canopy** | React 应用，clone/拉取后渲染 `.md` 文件 |
| **未来 API** | `GET /api/posts?path=<路径>` 返回 JSON |
| **任意 Markdown 渲染器** | 展示封面图和正文文字 |

## 关键术语

| 术语 | 含义 |
|------|------|
| frontmatter | 文件开头用 `---` 包裹的 YAML 元数据区域 |
| CDN | 七牛云对象存储的 URL 前缀 |
| livephoto | Apple 动态照片，封面图 `.jpg` + 影片 `.mov` |
| sylvan-canopy | React 消费端，渲染此仓库所有内容 |
