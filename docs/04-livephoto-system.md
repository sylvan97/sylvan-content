# Livephoto 系统

> 来源：`AGENTS.md`、`sylvan-content-需求说明.md`、`content/tests/format-demo/index.md`

## 核心约定

在正文中使用标准 Markdown 图片语法嵌入 livephoto：

```markdown
![](https://cdn.xxx.com/livephoto/<名字>/cover.jpg)
```

消费端（sylvan-canopy 的 LivephotoPlayer）自动检测同一 CDN 路径下是否存在 `motion.mov`：

| 文件组合 | 渲染行为 |
|----------|---------|
| `cover.jpg` + `motion.mov` 都存在 | 交互式 Livephoto 播放器 |
| 只有 `cover.jpg` | 普通 `<img>` 标签 |
| 只有 `motion.mov` | 原生 `<video>` 播放器 |

## 多篇文章互不干扰

每处 livephoto 嵌入独立检测。一篇文章中的多个 livephoto 互不影响。

## 工作流程

```
1. 拍摄 livephoto → 得到 .heic（或 .jpg）和 .mov 文件
2. 上传到七牛云 `livephoto/<名字>/` 目录下
3. 在文章正文中用 `![](https://cdn.xxx.com/livephoto/<名字>/cover.jpg)` 引用
4. 消费端自动检测 motion.mov → 渲染播放器
```

## 与 Obsidian 的关系

- Obsidian 中显示封面图，不会尝试播放视频 → 无报错
- 在 Obsidian 看来就是一张普通图片
- 只在前端消费端（sylvan-canopy）才激活播放器逻辑

## 测试验证

在 `content/tests/format-demo/index.md` 中已有测试引用：

```markdown
![](https://cdn.xxx.com/livephoto/sunset/cover.jpg)
```

可用于验证消费端的 livephoto 渲染逻辑。

## 字段对应

在 frontmatter 中也可明确声明 livephoto 数据：

```yaml
livephoto:
  video: https://cdn.xxx.com/livephoto/sunset/motion.mov
```

正文插图用 `cover.jpg`，frontmatter 的 `livephoto.video` 字段给消费端备查。
