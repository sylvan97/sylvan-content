# Sync Workflow 部署记录

> 2026-07-31 · `sync.yml` TLS 故障排查与修复

## 现状

GitHub Actions 推送后自动同步内容到服务器：

```
git push main
  → sync.yml: SSH 进服务器 → git pull → curl /api/revalidate ✅
  → notify-app.yml: curl https://zzffan.cn/api/revalidate ✅
```

## 修复记录

### 问题：git pull 报 GnuTLS 错误

workflow SSH 到目标服务器后 `git pull` 失败：

```
fatal: unable to access 'https://github.com/sylvan97/sylvan-content.git/':
GnuTLS recv error (-110): The TLS connection was non-properly terminated.
```

**根因**：服务器上的 git 用 HTTPS 连接 GitHub 时 TLS 握手异常（ca-certificates 过旧或 GnuTLS 版本问题）。

### 尝试过的方案

| 方案 | 结果 |
|------|------|
| 更新 ca-certificates、git | ❌ 仍然报相同错误 |
| git 改 SSH remote | ✅ 一劳永逸 |

### 最终修复：HTTPS → SSH

1. 在服务器上生成新的 SSH key pair：
   ```bash
   ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -N ""
   ```

2. 把公钥添加到 GitHub Repo Deploy Keys（勾选 Allow write access）：
   ```
   Settings → Deploy keys → Add deploy key
   ```

3. 修改 remote 地址：
   ```bash
   cd /data/sylvan-content
   git remote set-url origin git@github.com:sylvan97/sylvan-content.git
   ```

### 验证方法

```bash
# 服务器上测试 SSH 连通性
ssh -T git@github.com
# 输出：Hi sylvan97! You've successfully authenticated...

# 测试 git pull
cd /data/sylvan-content && git pull
```

## Workflow 文件参考

### `sync.yml`

SSH 进服务器 → git pull → 触发容器内的缓存刷新。

```yaml
name: Sync content to server
on:
  push:
    branches: [main]
jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - name: SSH into server, git pull & revalidate
        uses: appleboy/ssh-action@v1.2.0
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            set -e
            cd /data/sylvan-content
            git pull
            curl -s -X POST http://localhost:3000/api/revalidate \
              -H "Authorization: Bearer ${{ secrets.REVALIDATE_SECRET }}" \
              -H "Content-Type: application/json" \
              -d '{"event":"git-update"}'
```

### `notify-app.yml`

从 GitHub Actions 直接请求外部域名刷新缓存（作为 sync.yml 的补充）。

```yaml
name: Notify App
on:
  push:
    branches: [main]
jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger revalidation
        run: |
          curl -sf -X POST \
            -H "Authorization: Bearer ${{ secrets.REVALIDATE_SECRET }}" \
            -H "Content-Type: application/json" \
            -d '{"event":"git-update"}' \
            https://zzffan.cn/api/revalidate
```

## GitHub Secrets

以下 Secret 需要在 GitHub Repo 的 Settings → Secrets and variables → Actions 中配置：

| Secret | 用途 |
|--------|------|
| `SERVER_HOST` | 服务器 IP |
| `SERVER_USER` | SSH 用户名 |
| `SERVER_SSH_KEY` | 服务器私钥 |
| `REVALIDATE_SECRET` | revalidate 接口的鉴权密钥 |

## 相关内容

- `docs/02-content-architecture.md` — 内容架构
- `docs/03-writing-workflow.md` — 写作流程
