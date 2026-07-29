# 写作工作流

> 来源：`AGENTS.md`、`sylvan-content-需求说明.md`

## 标准流程

```
创建文件 → 写 frontmatter → 写正文 → 本地预览 → 提交推送
```

### 步骤详解

**Step 1：确定路径**

```
内容类型 → content/<分区>/<年份>/<slug>/
```

示例：游记 → `content/travel/2026/phuket/index.md`

**Step 2：创建文件**

```bash
# 方式一：目录风格的 index.md
mkdir -p content/travel/2026/phuket
touch content/travel/2026/phuket/index.md

# 方式二：单文件
touch content/livephoto/2026/phuket/sunset-beach.md
```

**Step 3：写 frontmatter**

最少要求：`title` 和 `date`

```yaml
---
title: 普吉岛日落
date: 2026-07-15
section: travel
tags: [thailand, beach, sunset]
---
```

**Step 4：写正文**

所有图片/视频使用完整的七牛云 CDN URL：

```markdown
## 正文开始

今天在普吉岛看了日落。

![](https://cdn.xxx.com/travel/phuket/sunset.jpg)

海浪声让人平静。
```

**Step 5：预览**

- 在 Obsidian 中打开仓库（以 vault 形式）
- 检查 frontmatter 是否能正常解析
- 检查图片是否加载

**Step 6：提交推送**

```bash
git add content/travel/2026/phuket/index.md
git commit -m "feat: 添加普吉岛游记"
git push origin main
```

- 文章草稿从 `main` 创建分支，写完可发布时合并
- `git push origin main` 即发布到生产环境（sylvan-canopy 自动部署）

## Livephoto 写作流程

详见 `docs/04-livephoto-system.md`。

## 多端检查清单

发布前检查：

- [ ] frontmatter 字段齐全（至少 `title`、`date`）
- [ ] 文件名为 kebab-case
- [ ] 图片使用 CDN 完整 URL，非本地路径
- [ ] 在 Obsidian 中预览无报错
- [ ] 正文中的链接可访问
