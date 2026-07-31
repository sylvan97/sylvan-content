# sylvan-content 仓库参考文档

> **入口文档。所有 Agent 初次处理此仓库时，请先阅读本节。**

本文档是对此仓库中所有 `.md` 文件的系统化总结，按主题渐进式组织。

## 仓库文件一览

| 文件 | 说明 |
|------|------|
| `README.md` | 项目简介 |
| `AGENTS.md` | Agent 操作指引——核心约定与入口 |
| `docs/requirements.md` | 完整需求文档——功能列表、用户场景、验收标准、FAQ |

## 目录结构

```
sylvan-content/
├── AGENTS.md                     # Agent 入口（必读）
├── README.md                     # 项目简介
├── .gitignore                    # Git 忽略规则
├── content/                      # 文章目录（目录 = URL）
│   ├── travel/
│   ├── life/
│   └── knowledge/
└── docs/                         # 渐进式参考
    ├── README.md                 #   ← 先读这里
    ├── 01-project-overview.md
    ├── 02-content-architecture.md
    ├── 03-writing-workflow.md
    ├── 04-livephoto-system.md
    ├── 05-writing-spec.md
    ├── 06-acceptance-criteria.md
    ├── 07-sync-workflow.md
    └── requirements.md
```

## 导航

```
先读 AGENTS.md ← Agent 指令入口
  └── 读完进入 docs/ 按需查阅
       ├── 01-project-overview.md        理解"为什么"做这个
       ├── 02-content-architecture.md    理解"内容怎么组织"
       ├── 03-writing-workflow.md        理解"怎么发布一篇文章"
       ├── 04-livephoto-system.md        理解"livephoto 怎么工作"
       ├── 05-writing-spec.md            写文章时的书写规范
       ├── 06-acceptance-criteria.md     发布前的验收清单
       └── 07-sync-workflow.md           Workflow 部署与排错
```
