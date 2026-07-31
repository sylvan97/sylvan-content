# AGENTS.md

本仓库是一个 **Git 原生内容 CMS**——纯 Markdown 仓库。文章在 Obsidian 中编写，媒体文件存在七牛云 CDN，内容由 **sylvan-canopy**（React 站点）消费。

- 绝不提交二进制文件（.gitignore 已拦截）
- 无构建系统、无 package.json
- `git push origin main` 即发布

## 核心约定

| 规则 | 一句话 |
|------|--------|
| 目录 = URL | `content/` 后的路径映射为 URL（去 `.md`） |
| Frontmatter | 每篇文章必须有 `title` + `date` |
| 媒体 | 使用完整七牛云 CDN URL，不走 Git |
| 命名 | kebab-case，如 `sunset-beach.md` |
| Livephoto | 正文写 `![](cdn/xxx/cover.jpg)`，sylvan-canopy 自动检测 `motion.mov` 渲染播放器 |

## 写作流程

```
content/<分区>/<年份>/<slug>/index.md → 写 frontmatter → 写正文 → git push
```

## 参考文档

| 文档 | 内容 |
|------|------|
| `docs/02-content-architecture.md` | Frontmatter 字段表、目录映射、命名规范 |
| `docs/03-writing-workflow.md` | 完整的写作步骤 |
| `docs/04-livephoto-system.md` | Livephoto 工作方式 |
| `docs/05-writing-spec.md` | 书写规范（支持的 Markdown 语法） |
| `docs/06-acceptance-criteria.md` | 发布前验收清单 |
