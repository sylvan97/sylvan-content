# 内容架构

> 来源：`AGENTS.md`、`content/tests/simple-demo/index.md`、`content/tests/format-demo/index.md`

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
├── knowledge/       # 知识笔记
└── tests/           # 测试/示例（仅供验证渲染）
    ├── simple-demo/index.md
    └── format-demo/index.md
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

## 现有文件参考

### simple-demo/index.md

最小化的示范文章，用于验证 git 内容源正常渲染。包含：

- 标准 frontmatter（title、date、section、tags、cover、featured）
- 简单正文：标题、无序列表、代码块、引用块

### format-demo/index.md

Markdown 格式功能大全，用于验证消费端渲染完备性。包含：

- 各级标题、文本样式
- 引用（含多级嵌套）
- 无序/有序/任务列表
- 代码块（JS、Python、CSS）
- 表格（含对齐）
- 链接与图片（含 CDN 引用）
- 行内公式 & 块级公式（LaTeX）
- Mermaid 图表（流程图、时序图、甘特图）
- 脚注、水平线、定义列表、折叠详情
- 混合测试：代码中的数学、引用中的公式、表格中的数学

详见 `docs/05-format-reference.md`。
