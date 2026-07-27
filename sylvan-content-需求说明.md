# sylvan-content 内容仓库 需求说明

## 产品概述
- **一句话说清楚**：一个专门存内容的 Git 仓库，所有文章写成 `.md` 文件，图片视频放七牛云 CDN。不用打开 Notion，在 Obsidian 里直接写，推上去就行。我的网站、以后的 APP、任何人想读内容，都从这个仓库来。
- **给谁用**：我自己（sylvan）。
- **解决什么麻烦**：Notion 对 livephoto 这类富媒体支持很差，内容被锁在别人平台里，导出麻烦、图片签名链接会过期、没法在 Obsidian 和网站上共享同一份内容。

## 功能列表

### 功能一：目录结构即 URL 路径
- **用户想做**：我想在仓库里按目录组织文章，目录长什么样，URL 就长什么样。
- **入口**：在 `content/<任意目录层级>/` 下创建 `.md` 文件
- **输入**：frontmatter（title、date、tags、cover、livephoto、draft、featured）+ markdown 正文，图片/视频全部用七牛云 CDN 的完整 URL
- **输出**：一个 `.md` 文件。URL 路径 = `content/` 之后的目录路径 + 文件名（去 `.md`）。例如 `content/livephoto/2026/phuket/sunset-beach.md` → `https://zzffan.cn/livephoto/2026/phuket/sunset-beach`

### 功能二：嵌入 livephoto 就像插一张图
- **用户想做**：我想在文章里放一张 livephoto，不用学任何特殊语法——就像平时插图一样写 markdown 图片语法。
- **入口**：正文中 `![](https://cdn.xxx.com/livephoto/<名字>/cover.jpg)`
- **输入**：七牛云 CDN 上的封面图 URL
- **输出**：聪明的消费端（如 sylvan-canopy）如果检测到同一 CDN 路径下有 `motion.mov`，自动渲染成交互式播放器；其他消费端显示封面图

### 功能三：大文件不上 Git
- **用户想做**：livephoto 的 .heic 和 .mov 都扔七牛云，Git 仓库里只有纯文本的 .md 文件。
- **入口**：上传文件到七牛云对象存储，拿到 CDN URL
- **输入**：二进制文件（.heic、.mov）
- **输出**：仓库只有 KB 级的文本文件，clone 秒完

### 功能四：同一份文件，各端各取所需
- **用户想做**：我在 Obsidian 里能正常浏览编辑；在 sylvan-canopy 上能看交互式 livephoto 播放器；未来随便哪个平台拿到这个文件，也能展示出它该有的样子。
- **入口**：任何消费端读这个 `.md` 文件
- **输入**：`.md` 文件内容
- **输出**：Obsidian 显示 frontmatter 属性 + markdown 正文；sylvan-canopy 渲染 livephoto 播放器；其他 markdown 渲染器至少显示封面图和文字

### 功能五：未来通过 API 获取内容
- **用户想做**：我不想每个消费端都去 clone 整个 git 仓库，想有一个简单的 HTTP 接口，请求一篇文章就返回给我。
- **入口**：`GET /api/posts?path=<URL路径>` 或 `GET /api/posts/<路径>`
- **输入**：文章的 URL 路径（如 `livephoto/2026/phuket/sunset-beach`）
- **输出**：JSON，包含 frontmatter 全部字段 + 正文用 base64 编码

## 用户场景

- **场景一**：从普吉岛回来想写游记。在仓库里建 `content/travel/2026/phuket/`，创建 `index.md`，写上标题、日期、正文。图片用 `https://cdn.xxx.com/travel/phuket/airport.jpg`。保存、推送。Obsidian 里能看，网站上能看，URL 是 `https://zzffan.cn/travel/2026/phuket`。

- **场景二**：拍了一段 livephoto。把 .heic 和 .mov 上传到七牛云 `livephoto/sunset/` 路径下。在游记正文里写一行 `![](https://cdn.xxx.com/livephoto/sunset/cover.jpg)`。sylvan-canopy 检测到同目录的 `motion.mov` 存在 → 渲染交互式播放器。在 Obsidian 里就是一张普通封面图，不报错。

- **场景三**：半年后做了一个手机 APP 想展示 livephoto 合集。不需要理解 sylvan-canopy 的代码——clone 仓库，读 `.md` 的 frontmatter 拿到 CDN 视频 URL 就能渲染。或者调一下未来 API，直接拿 JSON。

## 接口定义

- 功能一：写文章
  - **入口**：仓库 → `content/<路径>/` → 创建 `.md` 文件
  - **触发**：保存文件 + git push
  - **输入参数**：frontmatter（title、date、section、tags、cover、livephoto、draft、featured）+ markdown 正文。图片/视频 URL 为完整七牛云 CDN 地址
  - **输出**：URL 路径 = `content/` 之后的目录路径 + 文件名（去 `.md`）。sylvan-canopy 通过扫描文件系统生成路由

- 功能二：嵌入 livephoto
  - **入口**：正文中任意位置
  - **触发**：保存含 `![](https://cdn.xxx.com/livephoto/<name>/cover.jpg)` 的正文
  - **输入参数**：CDN 上的封面图完整 URL
  - **输出**：消费者按约定检测同一 CDN 路径下 `motion.mov` 是否存在。两者都有 → 交互式播放器；只有图片 → 普通 `<img>`；只有视频 → 原生 `<video>`

- 功能三：多端消费
  - **入口**：各消费端读取 .md 文件
  - **触发**：读取文件 / git clone / git pull
  - **输入参数**：文件路径
  - **输出**：Obsidian → frontmatter 属性 + markdown 正文；sylvan-canopy → React 组件渲染（含 LivephotoPlayer）；其他 → 纯 markdown

- 功能四：未来 API
  - **入口**：`GET /api/posts?path=<路径>` 或 `GET /api/posts/<路径>`
  - **触发**：HTTP 请求
  - **输入参数**：路径参数（如 `livephoto/2026/phuket/sunset-beach`）
  - **输出**：JSON（frontmatter 字段 + `content` 为 base64 编码的正文）

## 验收标准

- 功能一：目录结构即 URL
  - [ ] 在 `content/livephoto/2026/phuket/sunset-beach.md` 创建文件，对应 URL 为 `https://zzffan.cn/livephoto/2026/phuket/sunset-beach`
  - [ ] 支持任意层级深度：`content/a/b/c/d/e.md` → `https://zzffan.cn/a/b/c/d/e`
  - [ ] 目录名和文件名支持中文（URL 中自动 encode）
  - [ ] 没有 `cover` 字段的文章不报错，渲染时默认无封面图
  - [ ] 标记 `draft: true` 的文章，sylvan-canopy 列表页不显示

- 功能二：livephoto 约定
  - [ ] `![](https://cdn.xxx.com/livephoto/sunset/cover.jpg)` + 同一 CDN 路径下有 `motion.mov` → sylvan-canopy 渲染为 LivephotoPlayer
  - [ ] 只有 `cover.jpg` 没有 `motion.mov` → 保持普通 `<img>` 标签
  - [ ] 只有 `motion.mov` 没有 `cover.jpg` → 渲染为原生 `<video>` 播放器
  - [ ] 一篇文章中有多个 livephoto，各自独立判断，互不干扰
  - [ ] Obsidian 中显示封面图，无报错

- 功能三：多端消费
  - [ ] Obsidian 以仓库为 vault 打开，所有 .md 文件显示为 markdown 文档，frontmatter 在属性面板显示
  - [ ] sylvan-canopy 以 CONTENT_SOURCE=git 方式运行，文章分区、标签、封面图、livephoto 均正常渲染
  - [ ] 正文中的 `![](cdn-url)` 图片在 Obsidian 中正常加载显示

- 功能四：未来 API
  - [ ] `GET /api/posts?path=livephoto/2026/phuket/sunset-beach` 返回 JSON，包含完整 frontmatter 字段
  - [ ] 返回的 `content` 是合法的 base64 编码，解码后与原始 `.md` 文件正文一致
  - [ ] `cover` 和 `livephoto.video` 字段为完整 CDN URL，消费端拿到就能直接用

## 大家最在意的几点

- **好不好上手**：就是写 markdown。会用 Obsidian 写笔记就会用这个仓库。
- **收不收费**：全免费。GitHub 存代码免费；七牛云有免费额度，个人使用绰绰有余。
- **安不安全 / 隐私咋办**：仓库可以设 private，内容是你自己的，不在任何第三方平台手里。

## 出问题了怎么办

- **最怕什么**：文章丢了。但这是 Git 仓库——每次推送都是备份。
- **图片裂了**：如果七牛云上的文件被删或迁移，正文里引用的 CDN URL 就断了。解决办法：CDN 路径保持稳定，不随便挪文件。
- **七牛云欠费**：免费额度用完后图片视频会挂。解决办法：到了那个量级再考虑付费或换服务商，到时只需要改 frontmatter 和正文里的 URL 前缀。
