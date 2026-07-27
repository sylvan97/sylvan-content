# AGENTS.md

本文件向所有 Agent 提供操作此仓库时的指引。

## 仓库定位

一个 **Git 原生内容 CMS**——纯 Markdown 仓库。所有文章以 `.md` 文件形式存在；图片、livephoto 视频等媒体存放在**七牛云 CDN**。内容在 Obsidian 中编写，推送到 GitHub，由 **sylvan-canopy**（React 站点）或其他消费端使用。

- **无构建系统、无测试、无 package.json**——纯文本仓库。
- **绝不提交二进制文件**——媒体文件全部上传到七牛云对象存储。

**Git 远程:** `https://github.com/zzffan/sylvan-content.git`  
**作者:** sylvan (zzffan)

## 内容架构

### 目录结构 → URL 映射

所有内容放在 `content/` 下。`content/` 之后的路径直接映射到 URL（去掉 `.md`）：

```
content/livephoto/2026/phuket/sunset-beach.md
→ https://zzffan.cn/livephoto/2026/phuket/sunset-beach

content/a/b/c/d/e.md
→ https://zzffan.cn/a/b/c/d/e
```

- 目录名和文件名支持中文，URL 中会自动 encode。
- 使用 `index.md` 实现目录索引风格的 URL（例如 `content/travel/2026/phuket/index.md` → `https://zzffan.cn/travel/2026/phuket`）。

### Frontmatter Schema

每篇 `.md` 文件应包含 YAML frontmatter，字段如下：

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `title` | string | 是 | 文章标题 |
| `date` | date | 是 | 发布日期（ISO 8601 格式） |
| `section` | string | 否 | 内容分区（如 `travel`、`livephoto`） |
| `tags` | string[] | 否 | 标签列表 |
| `cover` | string | 否 | 封面图 CDN URL |
| `livephoto` | object | 否 | Livephoto 视频数据：`{video: "cdn-url"}` |
| `draft` | boolean | 否 | 为 `true` 时列表页隐藏此文章 |
| `featured` | boolean | 否 | 标记为精选文章 |

示例：

```yaml
---
title: 普吉岛日落
date: 2026-07-15
section: travel
tags: [thailand, beach, sunset]
cover: https://cdn.xxx.com/travel/phuket/airport.jpg
livephoto:
  video: https://cdn.xxx.com/livephoto/sunset/motion.mov
draft: false
featured: true
---
```

### Livephoto 约定

使用标准 Markdown 图片语法嵌入 livephoto：

```markdown
![](https://cdn.xxx.com/livephoto/<名字>/cover.jpg)
```

消费端检测逻辑（已在 sylvan-canopy 的 LivephotoPlayer 中实现）：

- **`cover.jpg` + `motion.mov` 都存在** → 渲染交互式 Livephoto 播放器
- **只有 `cover.jpg`** → 渲染普通 `<img>`
- **只有 `motion.mov`** → 渲染原生 `<video>`

每处 livephoto 嵌入独立检测，一篇文章中的多个 livephoto 互不干扰。

### 命名约定

- 目录和文件名优先使用 **kebab-case**（小写字母、连词号分隔）：`sunset-beach.md`，而非 `sunset_beach.md` 或 `SunsetBeach.md`。
- 适当情况下可以使用中文：`content/旅行/2026/普吉岛/`。
- 避免使用浏览器难以处理的特殊字符。

## 写作工作流

1. 在 `content/<分区>/<年份>/<slug>/index.md` 或 `content/<分区>/<年份>/<slug>.md` 创建文件。
2. 添加 YAML frontmatter，至少包含 `title` 和 `date`。
3. 编写 Markdown 正文——所有图片/视频使用完整的七牛云 CDN URL。
4. 保存、提交、推送。
5. Obsidian（以 vault 打开）本地渲染文件。sylvan-canopy 拉取更新并渲染站点。

## 消费端

| 消费端 | 内容读取方式 |
|--------|-------------|
| **Obsidian** | 以 vault 打开此仓库；属性面板中展示 frontmatter + Markdown 正文 |
| **sylvan-canopy** | React 应用，克隆/拉取此仓库后渲染每个 `.md` 文件；开发时使用 `CONTENT_SOURCE=git` 模式；检测 livephoto CDN 路径以渲染交互式播放器 |
| **未来 API** | 规划的 `GET /api/posts?path=<路径>` 接口，返回 JSON（frontmatter + base64 编码正文）——轻量消费端无需 clone |
| **任意 Markdown 渲染器** | 至少能展示封面图和正文文字 |

## Git 工作流

- 文章草稿从 `main` 创建分支，写完可发布时合并。
- 提交信息应说明内容变更内容（例如 `feat: 添加普吉岛游记`）。
- `git push origin main` 即发布到生产环境（sylvan-canopy 自动部署）。
