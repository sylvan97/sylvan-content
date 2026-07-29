# 验收标准

> 来源：`sylvan-content-需求说明.md`
>
> 发布新文章或新功能前对照此清单。

## 功能一：目录结构即 URL

- [ ] `content/livephoto/2026/phuket/sunset-beach.md` → `https://zzffan.cn/livephoto/2026/phuket/sunset-beach`
- [ ] 支持任意层级深度：`content/a/b/c/d/e.md` → `https://zzffan.cn/a/b/c/d/e`
- [ ] 目录名和文件名支持中文（URL 中自动 encode）
- [ ] 没有 `cover` 字段的文章不报错，渲染时默认无封面图
- [ ] 标记 `draft: true` 的文章，sylvan-canopy 列表页不显示

## 功能二：Livephoto 约定

- [ ] `![](cdn/xxx/livephoto/sunset/cover.jpg)` + 同路径有 `motion.mov` → 交互式播放器
- [ ] 只有 `cover.jpg` 没有 `motion.mov` → 普通 `<img>`
- [ ] 只有 `motion.mov` 没有 `cover.jpg` → 原生 `<video>`
- [ ] 一篇文章多个 livephoto，各自独立判断，互不干扰
- [ ] Obsidian 中显示封面图，无报错

## 功能三：多端消费

- [ ] Obsidian 以仓库为 vault 打开，所有 `.md` 文件正常显示
- [ ] sylvan-canopy 以 `CONTENT_SOURCE=git` 方式运行，分区/标签/封面/livephoto 正常
- [ ] 正文中的 `![](cdn-url)` 图片在 Obsidian 中正常加载

## 功能四：未来 API

- [ ] `GET /api/posts?path=livephoto/2026/phuket/sunset-beach` 返回 JSON
- [ ] 返回 `content` 为合法的 base64 编码
- [ ] `cover` 和 `livephoto.video` 字段为完整 CDN URL

## 写作规范验收

- [ ] Frontmatter 包含必需字段（`title`、`date`）
- [ ] 文件名使用 kebab-case
- [ ] 图片使用 CDN 完整 URL（非本地路径）
- [ ] 无二进制文件被提交到 Git
