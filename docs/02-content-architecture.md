# 内容架构

> 来源：`AGENTS.md`、`docs/05-writing-spec.md`

## 目录结构 → URL 映射

所有内容放在 `content/` 下。`content/` 之后的路径直接映射到 URL（去掉 `.md`）：

```
content/livephoto/2026/phuket/sunset-beach.md
→ https://zzffan.cn/livephoto/2026/phuket/sunset-beach

content/a/b/c/d/e.md
→ https://zzffan.cn/a/b/c/d/e
```

- 目录名和文件名支持中文，URL 中会自动 encode
- 使用 `index.md` 实现目录索引风格：`content/travel/2026/phuket/index.md` → `https://zzffan.cn/travel/2026/phuket`

## 分区结构

```
content/
├── travel/          # 游记
├── life/            # 生活记录
└── knowledge/       # 知识笔记
```

## Frontmatter Schema

每篇 `.md` 文件以 `---` 包裹的 YAML 开头：

```yaml
---
title: 文章标题            # 必需，字符串
date: 2026-07-27          # 必需，ISO 8601 格式
section: travel           # 可选，分区名称
tags: [thailand, beach]   # 可选，标签列表
cover: https://...jpg     # 可选，封面图 CDN URL
livephoto:                # 可选，视频数据
  video: https://...mov   #   视频 CDN URL
draft: false              # 可选，true 时列表页隐藏
featured: true            # 可选，标记为精选
---
```

### 字段规则

- 没有 `cover` 字段的文章不报错，渲染时默认无封面图
- 没有 `draft` 字段时不隐藏（默认 `false`）
- `livephoto` 对象只在需要嵌入 livephoto 时提供
- `section` 建议与 `content/` 下的子目录名一致

## 命名约定

- 目录和文件名优先使用 **kebab-case**：`sunset-beach.md` 而非 `sunset_beach.md` 或 `SunsetBeach.md`
- 适当情况下可以使用中文：`content/旅行/2026/普吉岛/`
- 避免浏览器难以处理的特殊字符

## 书写规范

仓库支持的全部 Markdown 语法（标题、样式、引用、列表、代码、表格、链接、公式、图表等）及使用约定，见 `docs/05-writing-spec.md`。
